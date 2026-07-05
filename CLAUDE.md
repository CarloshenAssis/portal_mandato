# CLAUDE.md — Portal do Mandato

## O que é este projeto

Plataforma SaaS multi-tenant de comunicação institucional e prestação de contas para mandatos políticos (vereadores, prefeitos, deputados, senadores, secretários). Um único código-base atende todos os cargos via módulos ativáveis.

## Documentação (fonte de verdade)

Antes de implementar qualquer coisa, leia os documentos em `/docs`:

- `vision.md` — Visão do produto, missão, posicionamento
- `business-rules.md` — Regras de negócio, módulos, estrutura de conteúdo, permissões, portal público, painel admin
- `database.md` — Schema completo (tabelas, colunas, tipos, constraints, índices, RLS, storage)
- `multi-tenant.md` — Multi-tenancy por coluna, resolução por domínio, isolamento, módulos por cargo
- `design-system.md` — Paleta, tipografia, espaçamento, grid, componentes, navegação, responsividade, acessibilidade
- `roadmap.md` — Fases 0-8, sprints, MVP de 30 dias, checklist, arquitetura de pastas

**Regra: se não está documentado, pergunte antes de implementar.**

## Stack

- Next.js 14+ (App Router) / TypeScript (strict, sem `any`)
- Tailwind CSS / shadcn/ui (customizado com Design System)
- Supabase (PostgreSQL, Auth, Storage)
- Vercel (deploy)
- Lucide React (ícones, outline, 20px)
- TipTap (editor rich text)
- Inter (fonte)

## Estrutura do monorepo

```
apps/portal-publico/     # Site público multi-tenant
apps/painel-admin/       # Painel administrativo
packages/ui/             # Design System (shadcn customizado)
packages/database/       # Schema, migrations, types, queries
packages/auth/           # Autenticação e sessão
packages/editor/         # TipTap
packages/upload/         # Upload para Supabase Storage
packages/seo/            # Meta tags, sitemap
packages/permissions/    # Papéis e permissões
packages/tenant/         # Resolução de tenant
packages/shared/         # Tipos, utils, constantes
modules/*/               # Módulos de conteúdo (noticias, projetos, etc.)
docs/                    # Fonte de verdade — NÃO altere sem pedir
```

## Comandos

```bash
# Instalar dependências
pnpm install

# Dev portal público
pnpm --filter portal-publico dev

# Dev painel admin
pnpm --filter painel-admin dev

# Gerar tipos do Supabase
pnpm --filter database gen:types

# Lint
pnpm lint

# Build
pnpm build
```

## Convenções de código

- Server Components por padrão. `"use client"` só quando necessário.
- Imports com `@/` prefix.
- Nomes de código em inglês. Comentários em português.
- Banco (tabelas, colunas, enums) em português conforme `database.md`.
- Um componente por arquivo (PascalCase).
- Toda query filtra por `mandato_id`. Sem exceção.
- Soft delete: nunca `DELETE FROM`, sempre `status = 'arquivado'`.
- Toda mutação gera registro em `audit_logs`.

## Regras críticas

1. **Multi-tenant**: toda tabela tem `mandato_id`. RLS ativo. Nenhum dado vaza entre mandatos.
2. **Conteúdo polimórfico**: tudo na tabela `conteudos` com campo `tipo`. Campos específicos no `metadata` (JSONB).
3. **Relacionamentos bidirecionais**: inserir (A,B) e (B,A) na tabela `conteudo_relacionados`.
4. **Tema**: apenas `cor_primaria` muda por mandato. Paleta neutra é fixa.
5. **Módulos**: inativos não aparecem em menu, rotas, nem API.
6. **O sistema NUNCA gera conteúdo político.** Apenas organiza e distribui.

## Design System — Resumo rápido

- Background: #FFFFFF | Surface: #FAFAFA | Border: #E5E7EB | Text: #111827 | Secondary: #6B7280
- Primária: `var(--primary)` — configurável por mandato, padrão #2563EB
- Container: max 1200px | Texto de leitura: max 720px
- Componentes prioritários: listas > tabelas > timelines > acordeões > cards (mínimo)
- Sem gradientes, glassmorphism, sombras pesadas, parallax, banners gigantes
- Border radius: 6px padrão | Sombra máxima: shadow-sm
- Animações: só hover/fade/slide, 150-200ms

## Permissões

| Papel | Pode |
|---|---|
| administrador | Tudo |
| assessor | CRUD completo de conteúdo |
| editor | Criar e editar (não publica, não exclui) |
| somente_leitura | Visualizar apenas |

## Fases de desenvolvimento

Fase 1: Fundação (infra, auth, layout, tema)
Fase 2: CMS (painel, CRUD genérico, editor)
Fase 3: Portal público (home, sobre, equipe, contato)
Fase 4: Módulos de conteúdo — **MVP COMERCIAL**
Fase 5: Módulos por cargo
Fase 6: SaaS (domínio, analytics, planos)
Fase 7: Escala
Fase 8: Premium

**Nunca avance de fase sem concluir a anterior.**
