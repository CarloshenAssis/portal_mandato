# Portal do Mandato — Design System

## Filosofia

O Portal do Mandato não deve parecer um site político, um portal governamental antigo, nem um blog. É um produto SaaS premium de prestação de contas.

A sensação deve ser: "Estou acessando uma plataforma séria de prestação de contas."

### Princípios

- Extremamente limpo
- Elegante
- Atemporal (deve sobreviver anos sem parecer datado)
- Institucional
- Moderno sem ser modinha
- Sem exageros visuais
- Sem elementos decorativos desnecessários
- Organização da informação acima da estética

### Referências conceituais

A inspiração vem de empresas que organizam informação excepcionalmente bem, não de campanhas políticas:

- Linear (limpeza, hierarquia)
- Notion (organização de conteúdo)
- Stripe (tipografia, espaçamento)
- Vercel (minimalismo, performance)
- GOV.UK (clareza institucional)
- Apple (simplicidade, foco)
- Arc Browser (navegação elegante)
- GitHub (densidade informacional bem resolvida)
- Medium (leitura confortável)

### O que a interface transmite

- Transparência
- Organização
- Credibilidade
- Simplicidade
- Institucionalidade

### O que a interface NUNCA transmite

- Marketing político
- Propaganda
- Campanha eleitoral

---

## Paleta de cores

### Cores neutras (fixas, nunca mudam)

| Token | Valor | Uso |
|---|---|---|
| `--background` | `#FFFFFF` | Fundo principal |
| `--surface` | `#FAFAFA` | Fundo de seções alternadas, cards |
| `--border` | `#E5E7EB` | Bordas, divisores |
| `--text` | `#111827` | Texto principal |
| `--text-secondary` | `#6B7280` | Texto secundário, labels, metadados |

### Cor primária (configurável por mandato)

Cada mandato define sua cor primária. Valor padrão: `#2563EB` (azul).

| Token | Cálculo |
|---|---|
| `--primary` | Cor do mandato |
| `--primary-light` | Primary com 10% de opacidade sobre branco |
| `--primary-dark` | Primary escurecido 15% |
| `--primary-foreground` | Branco ou preto (contraste automático) |

A cor primária é usada apenas em: botões de ação principal, links, badges de status, indicadores ativos no menu, ícones de destaque. Nunca em fundos grandes, headers ou seções inteiras.

### Proibições

- Sem gradientes chamativos
- Sem glassmorphism
- Sem skeuomorphism
- Sem neon ou cores vibrantes em excesso

---

## Tipografia

### Fonte

**Inter** (primeira opção) ou **Geist** (alternativa).

Fallback: `system-ui, -apple-system, sans-serif`.

### Escala

| Nível | Tamanho | Peso | Line-height | Uso |
|---|---|---|---|---|
| Display | 36px / 2.25rem | 700 | 1.2 | Título da home, hero |
| H1 | 30px / 1.875rem | 700 | 1.3 | Título de página |
| H2 | 24px / 1.5rem | 600 | 1.35 | Título de seção |
| H3 | 20px / 1.25rem | 600 | 1.4 | Subtítulo |
| H4 | 16px / 1rem | 600 | 1.5 | Título de card ou item |
| Body | 16px / 1rem | 400 | 1.7 | Texto de parágrafo |
| Body Small | 14px / 0.875rem | 400 | 1.6 | Metadados, labels |
| Caption | 12px / 0.75rem | 400 | 1.5 | Rodapé, timestamps |

### Regras

- Peso forte (600-700) apenas em títulos. Corpo sempre 400.
- Line-height generoso em textos longos (1.6 a 1.7).
- Nunca comprimido. Sempre confortável de ler.
- Máximo de 70-75 caracteres por linha em textos de conteúdo.

---

## Espaçamento

O branco faz parte do design.

### Escala de espaçamento (base 4px)

| Token | Valor | Uso |
|---|---|---|
| `space-1` | 4px | Espaçamento mínimo interno |
| `space-2` | 8px | Gap entre ícone e texto |
| `space-3` | 12px | Padding interno de badges |
| `space-4` | 16px | Padding de inputs, gap de lista |
| `space-6` | 24px | Padding de cards |
| `space-8` | 32px | Gap entre blocos |
| `space-12` | 48px | Separação entre seções |
| `space-16` | 64px | Separação entre seções grandes |
| `space-24` | 96px | Separação entre áreas da página |

### Regras

- Cada seção deve "respirar". Muito padding, muito espaço.
- Nunca "encher a tela".
- Margens laterais mínimas: 16px (mobile), 24px (tablet), 32px (desktop).
- Container máximo: 1200px, centralizado.

---

## Grid

### Layout principal

```
Container: max-width 1200px, centralizado
Colunas: 12 colunas
Gutter: 24px (desktop), 16px (mobile)
```

### Breakpoints

| Nome | Largura | Colunas |
|---|---|---|
| Mobile | < 640px | 4 |
| Tablet | 640px – 1024px | 8 |
| Desktop | > 1024px | 12 |

---

## Componentes

### Hierarquia de componentes (do mais usado ao menos usado)

1. **Listas** — Componente principal. Notícias, projetos, documentos, eventos: tudo é lista por padrão.
2. **Tabelas** — Para dados estruturados (ofícios, indicações, requerimentos). Elegantes, com hover, sem bordas pesadas.
3. **Timelines** — Para linha do tempo do mandato e histórico de tramitação de projetos.
4. **Blocos editoriais** — Para seções de conteúdo rico (sobre, biografia, resultados).
5. **Acordeões** — Para FAQ e seções colapsáveis.
6. **Painéis informativos** — Para "Mandato em números" e estatísticas.
7. **Cards** — Uso mínimo. Apenas quando necessário (galeria, destaque na home). Nunca dezenas de cards na mesma página.

### Regras de componentes

- Todo componente é reutilizável e independente.
- Nunca criar componente exclusivo para uma única página.
- Componentes seguem a mesma API de props (consistência).
- Usar shadcn/ui como base, customizando com os tokens do Design System.

---

## Bordas e sombras

| Propriedade | Valor |
|---|---|
| Border radius | 6px (padrão), 8px (cards), 4px (badges, inputs) |
| Border | 1px solid var(--border) |
| Sombra | Nenhuma ou `0 1px 2px rgba(0,0,0,0.05)` — muito sutil |

### Regras

- Raio pequeno. Nada arredondado demais.
- Pouca sombra. Sem cards "flutuando".
- Sem efeitos exagerados (drop shadows, glows).
- Elevação visual por bordas, não por sombra.

---

## Ícones

- Biblioteca: **Lucide** (outline)
- Tamanho padrão: 20px
- Peso consistente em todos os ícones
- Cor: `var(--text-secondary)` — nunca coloridos
- Nunca gigantes
- Alinhados verticalmente com o texto

---

## Animações

Somente microinterações:

| Tipo | Propriedade | Duração |
|---|---|---|
| Hover em links/botões | opacity, color | 150ms |
| Hover em cards/linhas | background-color | 150ms |
| Fade in de conteúdo | opacity | 200ms |
| Slide de menus | transform | 200ms |
| Acordeão expandir | height | 200ms |

### Regras

- `ease-out` para entradas, `ease-in` para saídas.
- Nada além de hover, fade e slide.
- Sem parallax, sem scroll animations, sem efeitos de entrada complexos.
- Respeitar `prefers-reduced-motion`.

---

## Navegação

### Menu superior (portal público)

- Simples, horizontal, poucos itens (máximo 7).
- Logo à esquerda.
- Itens ao centro ou à direita.
- Pesquisa sempre disponível (ícone de lupa).
- Botão "Fale com o Gabinete" em destaque (cor primária).
- Mobile: hamburger menu com drawer lateral.

### Menu lateral (painel administrativo)

- Vertical, fixo à esquerda.
- Itens com ícone + label.
- Gerado dinamicamente a partir dos módulos ativos.
- Seção de configurações separada no final.
- Colapsável (ícone-only) em telas menores.

---

## Hero (página inicial)

Sem banners gigantes. Sem slogans enormes. Estrutura simples:

```
[Foto institucional]  [Nome]
                      [Cargo — Partido]
                      [Cidade — UF]
                      [Frase institucional curta]
                      [Botão: Conheça o Mandato]  [Botão: Fale com o Gabinete]
```

- Foto em tamanho moderado, alta qualidade.
- Background limpo (branco ou surface).
- Sem overlay de cor, sem gradiente atrás da foto.

---

## Páginas de listagem

Todas seguem o mesmo padrão:

```
[Título da página]
[Filtros: categoria | área de atuação | bairro | data]
[Lista de itens]
[Paginação]
```

Cada item da lista exibe: título, resumo (2 linhas), categoria, data. Sem imagem de capa por padrão (apenas se o módulo exigir, como galeria).

---

## Páginas de conteúdo individual

Todas seguem o mesmo padrão:

```
[Breadcrumb]
[Título]
[Metadados: autor, data, categoria, área de atuação]
[Conteúdo rich text]
[Galeria (se houver)]
[Anexos / Downloads]
[Conteúdos relacionados]
[Compartilhamento]
```

Largura de leitura: máximo 720px para texto. Imagens e galeria podem expandir além.

---

## Painel administrativo

### Princípios

- Tão bom quanto o portal público.
- Aprendizado em menos de 5 minutos.
- Sem menus escondidos.
- Sem configurações complexas.
- Sem excesso de opções.

### Padrão de formulário

Todo cadastro de conteúdo segue o mesmo layout:

```
[Título do formulário]
[Campo: Título]
[Campo: Resumo]
[Editor: Conteúdo]
[Select: Categoria]
[Multi-select: Áreas de atuação]
[Multi-select: Bairros]
[Tags input]
[Upload: Anexos]
[Upload: Galeria]
[Select: Conteúdos relacionados]
[Campos de SEO (colapsável)]
---
[Sidebar: Status | Data de publicação | Autor]
[Botões: Salvar rascunho | Publicar | Agendar]
```

### Padrão de listagem no painel

```
[Título da seção]
[Botão: Novo + tipo]
[Filtros: status, categoria, busca]
[Tabela: título | status | data | ações]
[Paginação]
```

---

## Responsividade

| Elemento | Desktop | Tablet | Mobile |
|---|---|---|---|
| Menu | Horizontal | Horizontal compacto | Hamburger + drawer |
| Hero | Lado a lado | Lado a lado | Empilhado |
| Listas | 2-3 colunas | 2 colunas | 1 coluna |
| Conteúdo | 720px centrado | Full width com padding | Full width |
| Painel admin | Sidebar + conteúdo | Sidebar colapsada | Menu bottom ou drawer |

---

## Acessibilidade

- Contraste mínimo WCAG AA em todo texto.
- Navegação por teclado completa.
- Labels em todos os inputs.
- Alt text em todas as imagens.
- Estrutura semântica (headings, landmarks, nav).
- `prefers-reduced-motion` respeitado.
- `prefers-color-scheme` suportado (futuro: dark mode).
- Foco visível em todos os elementos interativos.

---

## Performance

- Imagens otimizadas (next/image com lazy loading).
- Fontes com `font-display: swap`.
- CSS mínimo (Tailwind purge).
- Componentes com lazy loading onde possível.
- Core Web Vitals como meta: LCP < 2.5s, FID < 100ms, CLS < 0.1.
