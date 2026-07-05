# Portal do Mandato — Banco de Dados

## Princípios

- Banco de dados Supabase (PostgreSQL).
- Multi-tenant por coluna: toda tabela de conteúdo possui `mandato_id`.
- Row Level Security (RLS) em todas as tabelas.
- Soft delete: conteúdos são arquivados, nunca excluídos fisicamente (exceto por administrador).
- Auditoria automática: toda alteração gera registro no log.
- UUIDs como chave primária em todas as tabelas.
- Timestamps `created_at` e `updated_at` em todas as tabelas.

---

## Diagrama de entidades

```
tenants (plataforma)
  └── mandatos
        ├── usuarios
        ├── configuracoes
        ├── modulos_ativos
        ├── conteudos (polimórfico)
        │     ├── conteudo_areas_atuacao
        │     ├── conteudo_bairros
        │     ├── conteudo_tags
        │     ├── conteudo_anexos
        │     ├── conteudo_galeria
        │     └── conteudo_relacionados
        ├── categorias
        ├── areas_atuacao
        ├── bairros
        ├── tags
        ├── equipe_membros
        ├── solicitacoes
        ├── menus
        └── audit_logs
```

---

## Tabelas

### tenants

Representa a conta/assinatura na plataforma.

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| nome | text | Nome da conta |
| slug | text | Unique, usado em URLs internas |
| plano | text | free, basic, premium |
| ativo | boolean | Default true |
| created_at | timestamptz | |
| updated_at | timestamptz | |

---

### mandatos

Entidade central. Um tenant pode ter mais de um mandato (ex: legislaturas diferentes).

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| tenant_id | uuid | FK → tenants |
| tipo_cargo | text | vereador, prefeito, deputado_estadual, deputado_federal, senador, secretario |
| nome | text | Nome do titular |
| foto_url | text | URL da foto institucional |
| cargo_display | text | Texto exibido (ex: "Vereador", "Deputado Estadual") |
| partido | text | |
| cidade | text | |
| estado | text | UF |
| legislatura | text | Ex: "2025-2028" |
| biografia | text | Rich text |
| missao | text | |
| valores | text | |
| bandeiras | text | |
| frase_institucional | text | Frase curta para o hero |
| dominio | text | Unique, domínio personalizado |
| logo_url | text | |
| favicon_url | text | |
| cor_primaria | text | Hex, ex: "#2563EB" |
| redes_sociais | jsonb | { whatsapp, telefone, email, instagram, facebook, youtube, twitter, site } |
| seo_titulo | text | |
| seo_descricao | text | |
| seo_imagem_url | text | |
| analytics_ga | text | ID do Google Analytics |
| analytics_plausible | text | Domínio do Plausible |
| ativo | boolean | Default true |
| created_at | timestamptz | |
| updated_at | timestamptz | |

---

### modulos_ativos

Define quais módulos estão habilitados para cada mandato.

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| modulo | text | noticias, projetos, indicacoes, oficios, mocoes, requerimentos, obras, programas, secretarias, decretos, emendas, comissoes, frentes_parlamentares, pec, convênios, licitacoes, metas |
| ativo | boolean | Default true |
| ordem | integer | Ordem no menu |
| created_at | timestamptz | |

Constraint: UNIQUE(mandato_id, modulo).

---

### usuarios

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK, FK → auth.users |
| mandato_id | uuid | FK → mandatos |
| nome | text | |
| email | text | |
| papel | text | administrador, assessor, editor, somente_leitura |
| ativo | boolean | Default true |
| created_at | timestamptz | |
| updated_at | timestamptz | |

---

### conteudos

Tabela polimórfica central. Todo tipo de conteúdo (notícia, projeto, indicação, ofício, obra, evento, documento) é armazenado aqui.

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| tipo | text | noticia, projeto, indicacao, oficio, mocao, requerimento, obra, programa, decreto, evento, documento, equipe, resultado |
| titulo | text | Obrigatório |
| resumo | text | |
| conteudo | text | Rich text (HTML) |
| slug | text | Gerado a partir do título |
| categoria_id | uuid | FK → categorias (nullable) |
| status | text | rascunho, publicado, agendado, arquivado |
| destaque | boolean | Default false. Conteúdo fixado na home |
| publicado_em | timestamptz | Data de publicação visível |
| agendado_para | timestamptz | Data de publicação automática |
| autor_id | uuid | FK → usuarios |
| seo_titulo | text | |
| seo_descricao | text | |
| seo_imagem_url | text | |
| visualizacoes | integer | Default 0 |
| metadata | jsonb | Campos específicos por tipo (ver abaixo) |
| created_at | timestamptz | |
| updated_at | timestamptz | |

Constraint: UNIQUE(mandato_id, tipo, slug).

#### Campo metadata por tipo

O campo `metadata` (jsonb) armazena os campos específicos de cada tipo:

**projeto**:
```json
{
  "numero": "PL 001/2025",
  "tipo_projeto": "pl | pr | mocao | emenda | pec",
  "status_tramitacao": "em_tramitacao | aprovado | rejeitado | sancionado | arquivado",
  "timeline": [
    { "data": "2025-02-01", "descricao": "Apresentado em plenário" },
    { "data": "2025-03-15", "descricao": "Aprovado em primeira votação" }
  ]
}
```

**indicacao**:
```json
{
  "numero": "IND 042/2025",
  "status_indicacao": "enviada | atendida | parcialmente_atendida | nao_atendida"
}
```

**oficio**:
```json
{
  "numero": "OF 015/2025",
  "destinatario": "Secretaria de Saúde",
  "situacao": "enviado | respondido | sem_resposta"
}
```

**requerimento**:
```json
{
  "numero": "REQ 008/2025",
  "destinatario": "Prefeitura Municipal",
  "situacao": "enviado | respondido | sem_resposta"
}
```

**obra**:
```json
{
  "status_obra": "planejada | em_andamento | concluida | paralisada",
  "percentual": 75,
  "foto_antes_url": "...",
  "foto_depois_url": "...",
  "localizacao": { "lat": -23.1896, "lng": -45.8841 }
}
```

**evento**:
```json
{
  "tipo_evento": "audiencia | visita | sessao | evento | reuniao",
  "data_inicio": "2025-06-15T14:00:00",
  "data_fim": "2025-06-15T17:00:00",
  "local": "Câmara Municipal"
}
```

---

### categorias

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| nome | text | |
| slug | text | |
| tipo_conteudo | text | Nullable. Se preenchido, a categoria só aparece para aquele tipo |
| ordem | integer | |
| created_at | timestamptz | |

---

### areas_atuacao

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| nome | text | Ex: Saúde, Educação, Segurança |
| slug | text | |
| icone | text | Nome do ícone (lucide) |
| ordem | integer | |
| created_at | timestamptz | |

---

### bairros

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| nome | text | |
| slug | text | |
| created_at | timestamptz | |

---

### tags

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| nome | text | |
| slug | text | |
| created_at | timestamptz | |

---

### conteudo_areas_atuacao

Relação N:N entre conteúdos e áreas de atuação.

| Coluna | Tipo | Notas |
|---|---|---|
| conteudo_id | uuid | FK → conteudos |
| area_atuacao_id | uuid | FK → areas_atuacao |

PK composta: (conteudo_id, area_atuacao_id).

---

### conteudo_bairros

Relação N:N entre conteúdos e bairros.

| Coluna | Tipo | Notas |
|---|---|---|
| conteudo_id | uuid | FK → conteudos |
| bairro_id | uuid | FK → bairros |

PK composta: (conteudo_id, bairro_id).

---

### conteudo_tags

Relação N:N entre conteúdos e tags.

| Coluna | Tipo | Notas |
|---|---|---|
| conteudo_id | uuid | FK → conteudos |
| tag_id | uuid | FK → tags |

PK composta: (conteudo_id, tag_id).

---

### conteudo_anexos

Arquivos vinculados a um conteúdo.

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| conteudo_id | uuid | FK → conteudos |
| mandato_id | uuid | FK → mandatos (para RLS) |
| nome | text | Nome original do arquivo |
| url | text | URL no Supabase Storage |
| tipo_arquivo | text | pdf, doc, docx, xls, xlsx, zip, imagem, video |
| tamanho | integer | Bytes |
| ordem | integer | |
| created_at | timestamptz | |

---

### conteudo_galeria

Fotos e vídeos vinculados a um conteúdo.

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| conteudo_id | uuid | FK → conteudos |
| mandato_id | uuid | FK → mandatos (para RLS) |
| tipo | text | foto, video |
| url | text | URL do arquivo ou embed |
| thumbnail_url | text | |
| legenda | text | |
| ordem | integer | |
| created_at | timestamptz | |

---

### conteudo_relacionados

Relação N:N bidirecional entre conteúdos.

| Coluna | Tipo | Notas |
|---|---|---|
| conteudo_origem_id | uuid | FK → conteudos |
| conteudo_destino_id | uuid | FK → conteudos |
| created_at | timestamptz | |

PK composta: (conteudo_origem_id, conteudo_destino_id).

Regra: ao inserir (A, B), inserir também (B, A) para garantir bidirecionalidade. Usar trigger ou função no banco.

---

### equipe_membros

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| nome | text | |
| cargo | text | |
| foto_url | text | |
| bio | text | |
| email | text | |
| telefone | text | |
| ordem | integer | |
| ativo | boolean | Default true |
| created_at | timestamptz | |
| updated_at | timestamptz | |

---

### solicitacoes

Mensagens enviadas pelos cidadãos via formulário do portal.

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| nome | text | |
| telefone | text | |
| email | text | |
| bairro | text | |
| categoria | text | |
| mensagem | text | |
| anexo_url | text | |
| consentimento_lgpd | boolean | Obrigatório true |
| status | text | nova, lida, respondida, arquivada |
| respondido_por | uuid | FK → usuarios (nullable) |
| resposta | text | |
| respondido_em | timestamptz | |
| created_at | timestamptz | |

---

### menus

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| label | text | Texto exibido |
| href | text | URL ou path |
| ordem | integer | |
| visivel | boolean | Default true |
| parent_id | uuid | FK → menus (nullable, para submenu) |
| created_at | timestamptz | |

---

### audit_logs

Registro imutável de todas as alterações.

| Coluna | Tipo | Notas |
|---|---|---|
| id | uuid | PK |
| mandato_id | uuid | FK → mandatos |
| usuario_id | uuid | FK → usuarios |
| tabela | text | Nome da tabela alterada |
| registro_id | uuid | ID do registro alterado |
| acao | text | criar, editar, excluir, publicar, arquivar, agendar |
| dados_anteriores | jsonb | Snapshot antes da alteração |
| dados_novos | jsonb | Snapshot depois da alteração |
| created_at | timestamptz | |

---

## Índices recomendados

```sql
-- Busca de conteúdo por mandato e tipo
CREATE INDEX idx_conteudos_mandato_tipo ON conteudos (mandato_id, tipo);

-- Busca de conteúdo por status
CREATE INDEX idx_conteudos_mandato_status ON conteudos (mandato_id, status);

-- Busca por slug
CREATE INDEX idx_conteudos_slug ON conteudos (mandato_id, tipo, slug);

-- Ordenação por data de publicação
CREATE INDEX idx_conteudos_publicado_em ON conteudos (mandato_id, publicado_em DESC);

-- Busca full-text
CREATE INDEX idx_conteudos_busca ON conteudos USING gin(
  to_tsvector('portuguese', coalesce(titulo, '') || ' ' || coalesce(resumo, '') || ' ' || coalesce(conteudo, ''))
);

-- Auditoria
CREATE INDEX idx_audit_mandato ON audit_logs (mandato_id, created_at DESC);
CREATE INDEX idx_audit_registro ON audit_logs (registro_id);

-- Solicitações
CREATE INDEX idx_solicitacoes_mandato ON solicitacoes (mandato_id, status, created_at DESC);

-- Domínio (lookup no middleware)
CREATE UNIQUE INDEX idx_mandatos_dominio ON mandatos (dominio) WHERE dominio IS NOT NULL;
```

---

## Row Level Security (RLS)

Toda tabela que possui `mandato_id` deve ter policy RLS que restringe acesso ao mandato do usuário autenticado.

```sql
-- Exemplo para conteudos
CREATE POLICY "Conteudos pertence ao mandato" ON conteudos
  USING (mandato_id IN (
    SELECT mandato_id FROM usuarios WHERE id = auth.uid()
  ));
```

Para o portal público, endpoints de leitura usam service role com filtro explícito por mandato_id (resolvido a partir do domínio).

---

## Storage (Supabase Storage)

Estrutura de buckets:

```
mandatos/
  {mandato_id}/
    logo/
    favicon/
    fotos/
    hero/
    anexos/
    galeria/
    documentos/
    solicitacoes/
```

Políticas de acesso: leitura pública para fotos, logo, galeria, hero, documentos. Upload restrito a usuários autenticados do mandato.
