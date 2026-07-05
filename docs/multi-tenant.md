# Portal do Mandato — Multi-Tenancy

## Estratégia

Multi-tenant por coluna (shared database, shared schema). Todas as tabelas de conteúdo possuem `mandato_id`. Um único banco de dados, um único deploy, múltiplos clientes.

---

## Hierarquia

```
Tenant (conta/assinatura)
  └── Mandato (entidade central)
        └── Usuários
        └── Módulos ativos
        └── Configurações
        └── Conteúdo
        └── Domínio
```

Um tenant pode ter mais de um mandato (ex: político com mandato anterior e atual, ou empresa que gerencia múltiplos políticos).

---

## Isolamento de dados

### Regra absoluta

Nenhum dado de um mandato é visível para outro mandato. Isso vale para: conteúdos, usuários, configurações, uploads, solicitações, auditoria, menus, categorias, tags, bairros, áreas de atuação.

### Implementação

1. **Row Level Security (RLS)** no Supabase em todas as tabelas com `mandato_id`.
2. **Middleware de resolução de tenant**: toda requisição do portal público resolve o `mandato_id` a partir do domínio (header `Host`). Toda requisição do painel resolve a partir do usuário autenticado.
3. **Queries sempre filtradas**: nenhuma query no código pode rodar sem filtro por `mandato_id`. Isso é reforçado pelo RLS como camada de segurança adicional.

---

## Resolução de tenant

### Portal público

O domínio da requisição determina qual mandato está sendo acessado.

Fluxo:

```
Requisição HTTP
  → Header Host: "vereadorjoao.com.br"
  → Middleware busca: SELECT * FROM mandatos WHERE dominio = 'vereadorjoao.com.br'
  → Resolve mandato_id
  → Toda a página é renderizada com dados daquele mandato
```

Fallback: se o domínio não for encontrado, retorna 404 ou página de "mandato não encontrado".

### Domínio padrão

Todo mandato recebe um subdomínio gratuito: `{slug}.portalmandato.com.br`. O domínio personalizado é opcional.

### Painel administrativo

O painel roda em `painel.portalmandato.com.br` (domínio único). O mandato é resolvido a partir do usuário autenticado:

```
Login
  → auth.uid()
  → SELECT mandato_id FROM usuarios WHERE id = auth.uid()
  → Todas as operações filtradas por mandato_id
```

---

## Configuração por mandato

Cada mandato possui suas próprias configurações, totalmente independentes:

| Configuração | Escopo |
|---|---|
| Logotipo | Por mandato |
| Cor primária | Por mandato |
| Favicon | Por mandato |
| Domínio | Por mandato |
| SEO global | Por mandato |
| Menu | Por mandato |
| Redes sociais | Por mandato |
| Módulos ativos | Por mandato |
| Categorias | Por mandato |
| Áreas de atuação | Por mandato |
| Bairros | Por mandato |
| Tags | Por mandato |
| Equipe | Por mandato |

---

## Módulos por tipo de cargo

Ao criar um mandato, os módulos padrão são ativados conforme o tipo de cargo:

### Vereador

Módulos automáticos: noticias, projetos, indicacoes, oficios, mocoes, requerimentos, agenda, galeria, biblioteca, equipe, contato, solicitacoes.

### Prefeito

Módulos automáticos: noticias, obras, programas, secretarias, decretos, metas, agenda, galeria, biblioteca, equipe, contato, solicitacoes.

### Deputado Estadual / Federal

Módulos automáticos: noticias, projetos, emendas, comissoes, frentes_parlamentares, agenda, galeria, biblioteca, equipe, contato, solicitacoes.

### Senador

Módulos automáticos: noticias, projetos, pec, emendas, comissoes, frentes_parlamentares, agenda, galeria, biblioteca, equipe, contato, solicitacoes.

Após a criação, o administrador pode ativar ou desativar qualquer módulo manualmente.

---

## Tema

O tema visual é controlado por uma única variável: `cor_primaria` (hex). Todo o restante da interface utiliza a paleta neutra fixa do Design System.

A cor primária é aplicada via CSS custom properties:

```css
:root {
  --color-primary: #2563EB; /* valor do mandato */
  --color-primary-light: /* calculado automaticamente */;
  --color-primary-dark: /* calculado automaticamente */;
}
```

---

## Storage

Cada mandato possui seu próprio diretório no Supabase Storage:

```
mandatos/{mandato_id}/
```

Políticas de storage por mandato: só usuários do mandato podem fazer upload. Leitura pública para arquivos do portal.

---

## Deploy

Um único deploy na Vercel atende todos os mandatos. Não há deploy separado por cliente.

O middleware do Next.js resolve o tenant a cada requisição com base no domínio. Domínios personalizados são configurados via Vercel Domains API.

---

## Limites e planos (futuro)

| Recurso | Free | Basic | Premium |
|---|---|---|---|
| Conteúdos publicados | 50 | 500 | Ilimitado |
| Storage | 100 MB | 1 GB | 10 GB |
| Usuários | 1 | 3 | 10 |
| Domínio personalizado | Não | Sim | Sim |
| Módulos extras | Não | Não | Sim |
| Analytics avançado | Não | Não | Sim |
| Suporte | Comunidade | Email | Prioritário |

Esses limites são verificados no backend antes de cada operação (upload, criação de conteúdo, criação de usuário).
