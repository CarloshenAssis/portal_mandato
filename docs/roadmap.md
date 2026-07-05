# Portal do Mandato — Roadmap

## Estratégia

Duas linhas paralelas:

- **Versão Comercial (30 dias):** tudo necessário para vender imediatamente.
- **Roadmap do Produto (6–12 meses):** evolução contínua da plataforma.

O objetivo imediato é ter um produto que pareça completo, seja estável e possa ser implantado em poucas horas para cada cliente. A janela de oportunidade eleitoral exige velocidade.

---

## Fases de desenvolvimento

### Sprint -1 — Documentação (antes de qualquer código)

Objetivo: produzir documentos completos como fonte de verdade.

Entregas:

- `vision.md` — Visão do produto
- `business-rules.md` — Regras de negócio
- `database.md` — Schema do banco de dados
- `multi-tenant.md` — Estratégia de multi-tenancy
- `design-system.md` — Design System completo
- `roadmap.md` — Este documento

Regra: nenhuma funcionalidade é implementada sem especificação aprovada na pasta `/docs`.

---

### Fase 0 — Fundação conceitual

Objetivo: decisões de arquitetura antes de código.

- Definir a visão do produto.
- Definir arquitetura de módulos.
- Criar Design System.
- Definir regras de negócio.
- Definir schema do banco.
- Definir multi-tenancy.
- Definir permissões.
- Definir estrutura dos módulos.

Status: concluído via documentação.

---

### Fase 1 — Fundação técnica (Sprint 1)

Objetivo: plataforma pronta para crescer, sem conteúdo ainda.

Entregas:

- Projeto Next.js com TypeScript
- Tailwind CSS configurado
- shadcn/ui instalado e customizado com Design System
- Supabase configurado (banco, auth, storage)
- Schema do banco aplicado (migrations)
- Row Level Security em todas as tabelas
- Multi-tenant: resolução por domínio (middleware)
- Sistema de autenticação (login, logout, sessão)
- Sistema de permissões (papéis: admin, assessor, editor, somente leitura)
- Upload de arquivos para Supabase Storage
- Tema configurável (cor primária via CSS variable)
- Layout base do portal público (header, footer, container)
- Layout base do painel administrativo (sidebar, content area)
- Componentes do Design System implementados
- Navegação do portal e do painel
- SEO base (meta tags, sitemap, robots.txt)
- Auditoria (trigger de log em todas as tabelas)
- Estrutura de pastas do projeto

Critério de conclusão: é possível fazer login, ver o layout base do portal e do painel, trocar cor primária, fazer upload de arquivo. Nenhum conteúdo existe ainda.

---

### Fase 2 — CMS (Sprint 2)

Objetivo: painel administrativo funcional com CRUD genérico.

Entregas:

- Dashboard com indicadores básicos
- CRUD de usuários (convidar, editar papel, desativar)
- CRUD genérico de conteúdo (criar, editar, excluir, duplicar)
- Editor rich text (TipTap ou similar)
- Upload de imagens e arquivos dentro do editor
- Gestão de categorias
- Gestão de tags
- Gestão de áreas de atuação
- Gestão de bairros
- Status de conteúdo (rascunho, publicado, agendado, arquivado)
- Preview de conteúdo
- Agendamento de publicação
- Histórico de alterações (baseado em audit_logs)
- Campos de SEO por conteúdo
- Gestão de conteúdos relacionados (selecionar via busca)

Critério de conclusão: é possível criar, editar, publicar e agendar qualquer tipo de conteúdo. O CRUD é genérico e reutilizável para todos os módulos.

#### Mecanismo de retenção: incentivo à publicação contínua

Ver `vision.md` — a motivação de manter o portal atualizado tende a ser mais forte perto de eleições, com risco de abandono nos períodos intermediários do mandato. Para mitigar isso, o painel deve incluir, ainda nesta fase:

- **Indicador de inatividade**: se o mandato não publicar nada há 30 dias, o dashboard exibe um aviso visível ("Você não publica há X dias").
- **Lembrete por email**: notificação automática para usuários com papel `administrador` ou `assessor` quando a inatividade passar de 30 dias.
- **Resumo mensal** ("Mandato em números do mês"): visualizações, novas publicações, solicitações recebidas — enviado por email, reforçando o hábito de checar o painel mesmo fora de período eleitoral.

Esses itens não bloqueiam o critério de conclusão da Fase 2, mas devem estar implementados antes do fechamento do MVP comercial (Fase 4), pois atacam diretamente o risco de churn fora do ciclo eleitoral.

---

### Fase 3 — Portal público (Sprint 3)

Objetivo: site público visível ao cidadão.

Entregas:

- Página inicial completa (hero, mandato em números, últimas atualizações, destaques, rodapé)
- Página Sobre (biografia, missão, valores, bandeiras)
- Página Equipe
- Página Contato
- Formulário de Solicitações (com consentimento LGPD)
- Página Agenda (calendário de eventos)
- Pesquisa global
- Responsividade completa (mobile, tablet, desktop)
- Compartilhamento de conteúdo (redes sociais, copiar link)
- Tema aplicado via cor primária do mandato
- Rodapé com contato, redes, LGPD

Critério de conclusão: o portal público está funcional e navegável. Não há conteúdo de módulos específicos ainda (apenas home, sobre, equipe, contato, agenda, pesquisa).

---

### Fase 4 — Módulos de conteúdo (Sprint 4)

Objetivo: todos os módulos do núcleo funcionando.

Entregas:

- Módulo Notícias (listagem com filtros + página individual)
- Módulo Projetos (listagem + página com timeline e relacionados)
- Módulo Eventos (listagem + página individual)
- Módulo Galeria (fotos e vídeos, álbuns)
- Módulo Biblioteca (documentos com filtro por tipo)
- Módulo Resultados (organização por área de atuação)
- Módulo Solicitações no painel (visualizar, responder, exportar)
- Conteúdos relacionados funcionando em todas as páginas
- Filtros por categoria, área de atuação, bairro, data

Critério de conclusão: o portal exibe notícias, projetos, eventos, galeria, documentos. Tudo com filtros e relacionados. **Este é o MVP comercial.**

---

### Fase 5 — Módulos específicos (Sprint 5)

Objetivo: módulos por tipo de cargo.

Entregas:

- Módulo Indicações (vereador)
- Módulo Ofícios (vereador)
- Módulo Moções (vereador)
- Módulo Requerimentos (vereador)
- Módulo Obras (prefeito)
- Módulo Programas de Governo (prefeito)
- Módulo Secretarias (prefeito)
- Módulo Decretos (prefeito)
- Módulo Emendas (deputado)
- Módulo Comissões (deputado)
- Módulo PEC (deputado federal, senador)

Todos utilizam o CRUD genérico da Fase 2 + campos específicos via metadata.

---

### Fase 6 — Produto SaaS (Sprint 6)

Objetivo: transformar em produto comercial escalável.

Entregas:

- Domínio personalizado por mandato (configuração via painel)
- Customização completa (logo, cor, favicon, redes)
- Analytics integrado (Google Analytics + Plausible)
- Backup automático
- Deploy automático (Vercel)
- Painel de administração da plataforma (superadmin)
- Gestão de tenants (criar, desativar, configurar)
- Planos e limites (free, basic, premium)

---

### Fase 7 — Escala (Sprint 7)

Objetivo: suportar centenas de mandatos.

Entregas:

- Cache (ISR, SWR, Redis se necessário)
- CDN para assets
- Monitoramento (uptime, erros, performance)
- Rate limiting
- Fila para tarefas pesadas (geração de PDF, processamento de imagem)
- Otimização de queries
- Testes de carga

---

### Fase 8 — Produto premium (Sprint 8)

Objetivo: diferenciais de longo prazo.

Entregas:

- Mapa da cidade com ações por bairro
- Linha do tempo visual do mandato
- Motor de relacionamento avançado (grafos de conteúdo)
- Área do cidadão (favoritos, acompanhamento de conteúdos)
- Newsletter automática
- Relatórios em PDF (geração automática)
- Integração com Instagram/Facebook (republicar conteúdos)
- API pública para dados do mandato
- Aplicativo móvel (PWA ou nativo)

---

## MVP Comercial — Escopo de 30 dias

O MVP é a soma das Fases 1 a 4. Ao final, o produto permite:

1. Fechar contrato com um mandato.
2. Criar o cliente no sistema.
3. Configurar logo, cor, domínio.
4. Importar/cadastrar conteúdo.
5. Publicar o portal em poucas horas.

### Checklist do MVP

**Infraestrutura:**

- [ ] Multi-tenant funcionando
- [ ] Auth funcionando
- [ ] Usuários com papéis
- [ ] Tema configurável
- [ ] Upload de arquivos
- [ ] SEO base

**Painel administrativo:**

- [ ] Dashboard
- [ ] CMS genérico (CRUD)
- [ ] Editor rich text
- [ ] Categorias
- [ ] Tags
- [ ] Áreas de atuação
- [ ] Bairros
- [ ] Biblioteca

**Portal público:**

- [ ] Home completa
- [ ] Sobre
- [ ] Equipe
- [ ] Notícias
- [ ] Projetos
- [ ] Agenda
- [ ] Galeria
- [ ] Biblioteca
- [ ] Contato
- [ ] Solicitações
- [ ] Pesquisa
- [ ] Responsivo

**Personalização:**

- [ ] Logo
- [ ] Cor primária
- [ ] Favicon
- [ ] Domínio
- [ ] Redes sociais

**Deploy:**

- [ ] Vercel configurado
- [ ] Supabase em produção
- [ ] Storage configurado
- [ ] Domínio com SSL

---

## Regras de desenvolvimento

### Disciplina por sprint

Cada sprint deve terminar com:

- Código limpo e revisado
- Documentação atualizada na pasta `/docs`
- Testes básicos passando
- Refatoração feita
- Checklist da sprint concluído
- Nenhuma pendência crítica

### Regra de ouro

Nunca avançar para a próxima sprint sem concluir a anterior. A tentação de "fazer tudo junto" é o maior risco do projeto.

### Documentação primeiro

Toda nova funcionalidade só é implementada depois de existir uma especificação aprovada na pasta `/docs`. Isso transforma o desenvolvimento em um processo previsível.

---

## Arquitetura de pastas

```
apps/
    portal-publico/        # Next.js — site público
    painel-admin/          # Next.js — painel administrativo

packages/
    ui/                    # Design System (componentes shadcn customizados)
    database/              # Schema, migrations, types, queries
    auth/                  # Autenticação e sessão
    editor/                # Editor rich text
    upload/                # Upload de arquivos
    analytics/             # Tracking
    seo/                   # Meta tags, sitemap, schema
    permissions/           # Papéis e permissões
    tenant/                # Resolução de tenant
    shared/                # Tipos, utils, constantes

modules/
    noticias/              # CRUD + componentes de notícias
    projetos/              # CRUD + componentes de projetos
    documentos/            # CRUD + componentes de documentos
    eventos/               # CRUD + componentes de eventos
    agenda/                # Calendário
    galeria/               # Fotos e vídeos
    biblioteca/            # Documentos do mandato
    equipe/                # Membros do gabinete
    solicitacoes/          # Formulário + gestão
    resultados/            # Áreas de atuação

docs/                      # Fonte de verdade do produto
    vision.md
    business-rules.md
    database.md
    multi-tenant.md
    design-system.md
    roadmap.md
```

Nenhum módulo depende diretamente do outro. Todos plugam no core via interfaces padronizadas.
