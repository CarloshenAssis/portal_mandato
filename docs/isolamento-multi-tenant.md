# Portal do Mandato — Isolamento Multi-Tenant e RLS

## Status: especificação a ser implementada na Fase 1 (Fundação Técnica)

Este documento existe porque o isolamento entre mandatos é o requisito não-negociável do produto. Uma falha aqui não é um bug — é o conteúdo do político A aparecendo para o político B, ou no site público errado. Isso compromete a credibilidade da empresa, não só de um cliente.

Este documento complementa `database.md` e `multi-tenant.md`. Não os substitui — detalha **como garantir, na prática**, que a regra "nenhum dado de um mandato é visível para outro" nunca seja violada, mesmo sob erro humano ou pressão de prazo.

---

## Regra fundamental

> Nenhuma linha de qualquer tabela pode ser lida, escrita, atualizada ou relacionada cruzando o `mandato_id` do usuário autenticado (painel) ou do domínio resolvido (portal público).

Essa regra precisa estar garantida em **duas camadas independentes**, porque uma falha em qualquer uma delas sozinha não pode causar vazamento:

| Camada | O que faz | O que acontece se falhar |
|---|---|---|
| 1. Aplicação (middleware / queries) | Resolve `mandato_id` e filtra toda query | RLS (camada 2) ainda bloqueia |
| 2. RLS no banco (Supabase) | Bloqueia no nível do Postgres, independente da query da aplicação | Se também falhar, vaza |

**Nunca confiar em apenas uma camada.** O objetivo deste documento é que a Camada 2 esteja correta mesmo se a Camada 1 tiver um bug.

---

## 1. Resolução de tenant (Camada 1)

### Portal público

```
Requisição → Header Host → SELECT mandato_id FROM mandatos WHERE dominio = host
           → mandato_id injetado no contexto da requisição
           → toda query da página usa esse mandato_id
```

- Fallback: domínio não encontrado → 404 / página "mandato não encontrado". Nunca cair para um mandato default.
- Subdomínio padrão (`{slug}.portalmandato.com.br`) segue a mesma resolução, via tabela `mandatos`.

### Painel administrativo

```
Login → auth.uid() → SELECT mandato_id FROM usuarios WHERE id = auth.uid()
      → mandato_id injetado na sessão
      → toda query do painel usa esse mandato_id
```

- Um usuário nunca pode trocar de `mandato_id` manualmente (ex: via parâmetro de URL ou payload). O `mandato_id` da sessão é sempre derivado do banco, nunca aceito como input do client.

### Regra crítica de implementação

Toda função de acesso a dados (query, mutation, server action) deve receber o `mandato_id` resolvido pelo contexto da requisição — **nunca** deve aceitar `mandato_id` vindo diretamente do client sem revalidar contra a sessão/domínio.

---

## 2. RLS no banco (Camada 2) — onde realmente costuma falhar

RLS "existir" não é suficiente. Os pontos abaixo são onde isolamento multi-tenant quebra na prática, mesmo com RLS escrito.

### 2.1 RLS precisa ser habilitado explicitamente por tabela

No Postgres, criar uma tabela **não** ativa RLS. Se uma tabela nova for criada sem o comando abaixo, ela fica **aberta por padrão**, não fechada:

```sql
ALTER TABLE nome_da_tabela ENABLE ROW LEVEL SECURITY;
```

**Regra de processo:** toda migration que cria uma tabela com `mandato_id` (direto ou indireto) deve incluir `ENABLE ROW LEVEL SECURITY` + as policies **na mesma migration**, nunca como passo separado posterior.

### 2.2 Tabelas de relação N:N — o ponto mais frágil

Olhando o `database.md`, estas tabelas **não têm coluna `mandato_id` própria**:

- `conteudo_areas_atuacao`
- `conteudo_bairros`
- `conteudo_tags`
- `conteudo_relacionados`

Isso significa que a policy de RLS não pode filtrar direto por `mandato_id` — precisa validar via subquery contra `conteudos`:

```sql
-- Exemplo: conteudo_tags
CREATE POLICY "Isolamento por mandato via conteudo"
  ON conteudo_tags
  USING (
    conteudo_id IN (
      SELECT id FROM conteudos
      WHERE mandato_id IN (
        SELECT mandato_id FROM usuarios WHERE id = auth.uid()
      )
    )
  );
```

**Regra adicional para `conteudo_relacionados`:** precisa de uma constraint/trigger própria, porque RLS sozinho não impede que um `INSERT` relacione conteúdo de mandatos diferentes (RLS controla *visibilidade*, não impede automaticamente a *criação* de uma relação inválida se a policy de INSERT não validar os dois lados):

```sql
CREATE OR REPLACE FUNCTION valida_mesmo_mandato_relacionado()
RETURNS TRIGGER AS $$
BEGIN
  IF (SELECT mandato_id FROM conteudos WHERE id = NEW.conteudo_origem_id)
     != (SELECT mandato_id FROM conteudos WHERE id = NEW.conteudo_destino_id) THEN
    RAISE EXCEPTION 'Conteúdos relacionados pertencem a mandatos diferentes';
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_valida_mandato_relacionado
  BEFORE INSERT ON conteudo_relacionados
  FOR EACH ROW EXECUTE FUNCTION valida_mesmo_mandato_relacionado();
```

### 2.3 Service role ignora RLS — por design

O `database.md` já define que o portal público usa **service role com filtro explícito de `mandato_id`** (para performance, já que o cidadão não está autenticado). Isso é uma decisão correta, mas tem uma consequência importante:

> **Service role do Supabase ignora RLS por padrão.** Se um endpoint que usa service role esquecer o filtro manual de `mandato_id`, RLS não vai bloquear, porque foi desligado de propósito naquela conexão.

**Regra de processo:** todo endpoint/query que usa service role precisa, obrigatoriamente:
1. Filtrar explicitamente por `mandato_id` resolvido pelo domínio.
2. Passar por code review com checklist específico (ver seção 4).
3. Ter teste automatizado cobrindo isolamento (ver seção 3), já que o RLS não está ativo ali para servir de rede de segurança.

### 2.4 Policies "temporárias" esquecidas

Nunca usar `USING (true)` mesmo para debug local — é fácil esquecer de reverter antes do merge. Se precisar testar sem RLS, desabilitar a sessão de teste, não a policy.

---

## 3. Testes automatizados de isolamento (obrigatório antes de fechar a Fase 1)

Critério de aceite da fundação técnica: **nenhuma feature é considerada "pronta" sem teste de isolamento cross-tenant.**

Estrutura mínima de teste, a rodar no CI:

```
1. Criar mandato_teste_A e mandato_teste_B (com tenants, usuários, conteúdos próprios)
2. Autenticar como usuário de A
3. Tentar SELECT em todas as tabelas com mandato_id (direto ou via subquery) filtrando por dados de B
   → esperado: 0 linhas retornadas
4. Tentar INSERT/UPDATE relacionando conteúdo de A com conteúdo de B
   → esperado: erro (trigger ou policy bloqueando)
5. Repetir para o portal público: requisição com Host de A não pode retornar dados de B
6. Repetir para cada endpoint que usa service role
```

Esse teste deve falhar o build no CI. Não é checagem manual pontual — é regressão permanente, porque qualquer tabela nova ou query nova pode reintroduzir o problema.

---

## 4. Checklist de code review (toda PR que toca em dados)

- [ ] A tabela nova tem `ENABLE ROW LEVEL SECURITY` na mesma migration?
- [ ] Existe policy de `SELECT`, `INSERT`, `UPDATE` (e `DELETE` se aplicável) para a tabela?
- [ ] Se a tabela não tem `mandato_id` direto, a policy usa subquery correta contra a tabela pai?
- [ ] Se a query usa service role, há filtro explícito de `mandato_id` no código?
- [ ] Se a feature cria relação entre duas linhas (ex: `conteudo_relacionados`), há validação de que ambas pertencem ao mesmo mandato?
- [ ] Existe teste de isolamento cross-tenant cobrindo essa tabela/endpoint?
- [ ] O `mandato_id` usado na operação vem do contexto da sessão/domínio — nunca de input do client?

Nenhuma PR que toque em tabela com `mandato_id` (direto ou indireto) deve ser aprovada sem essas respostas.

---

## 5. Resumo das tabelas e sua estratégia de isolamento

| Tabela | `mandato_id` direto? | Estratégia de RLS |
|---|---|---|
| `mandatos` | — (é a própria entidade) | Acesso restrito por `tenant_id` do usuário |
| `usuarios` | Sim | Filtro direto |
| `modulos_ativos` | Sim | Filtro direto |
| `conteudos` | Sim | Filtro direto |
| `categorias` / `areas_atuacao` / `bairros` / `tags` | Sim | Filtro direto |
| `conteudo_areas_atuacao` | Não | Subquery via `conteudos` |
| `conteudo_bairros` | Não | Subquery via `conteudos` |
| `conteudo_tags` | Não | Subquery via `conteudos` |
| `conteudo_relacionados` | Não | Subquery via `conteudos` **+ trigger de validação cruzada** |
| `conteudo_anexos` | Sim (duplicado para RLS) | Filtro direto |
| `conteudo_galeria` | Sim (duplicado para RLS) | Filtro direto |
| `equipe_membros` | Sim | Filtro direto |
| `solicitacoes` | Sim | Filtro direto **+ atenção especial a LGPD** |
| `menus` | Sim | Filtro direto |
| `audit_logs` | Sim | Filtro direto, **somente leitura** (nunca UPDATE/DELETE, nem por administrador) |

---

## 6. Fora de escopo deste documento

- Autenticação e proteção de login/senha (2FA, rate limiting) — tratado em documento próprio de segurança de acesso.
- LGPD e tratamento de dados pessoais em `solicitacoes` — tratado em documento próprio de privacidade.

Este documento cobre exclusivamente isolamento de dados entre mandatos (multi-tenancy), que é pré-requisito técnico para qualquer outra camada de segurança fazer sentido.
