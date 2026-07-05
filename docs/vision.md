# Portal do Mandato — Visão do Produto

## Missão

Desenvolvemos plataformas digitais para gestão da comunicação institucional e prestação de contas de mandatos públicos.

## O que é

O Portal do Mandato é uma plataforma SaaS multi-tenant de comunicação institucional, prestação de contas e organização de informações públicas para mandatos políticos.

## O que NÃO é

- Não é um site de campanha.
- Não é um portal governamental antigo.
- Não é um blog.
- Não é uma ferramenta de marketing político.

## Filosofia

O sistema nunca gera conteúdo político. Ele organiza, apresenta e distribui o conteúdo produzido pelo mandato.

Todo o conteúdo publicado é inserido exclusivamente pelo político ou por sua equipe através do painel administrativo. A plataforma é uma ferramenta de publicação e prestação de contas — isso evita problemas éticos e legais.

## Público-alvo

### Clientes (quem paga)

- Vereadores (primeiro nicho)
- Prefeitos
- Vice-prefeitos
- Deputados estaduais
- Deputados federais
- Senadores
- Secretários municipais
- Secretários estaduais
- Governadores

### Usuários finais (quem acessa)

- Cidadãos que desejam acompanhar o mandato
- Imprensa
- Outros gabinetes

## Posicionamento

A plataforma compete como empresa de tecnologia, não como agência de sites.

O nome do produto nunca deve conter referência a um cargo específico (nunca "Portal do Vereador"). O nome precisa funcionar para qualquer cargo público.

## Conceito central

O sistema não cadastra um político. Cadastra um **mandato**.

O mandato é a entidade central. Tudo pertence ao mandato: notícias, projetos, documentos, equipe, galeria, eventos, solicitações.

O tipo de mandato (vereador, prefeito, deputado) determina quais módulos ficam disponíveis. A arquitetura é a mesma para todos.

## Diferenciais competitivos

### Motor de relacionamento de conteúdo

Nada existe isolado. Um Projeto de Lei pode estar relacionado a uma Notícia, que pode ter uma Galeria de Fotos, que pode ter um Ofício vinculado, que pode ter um Evento representando a audiência pública. Tudo aparece automaticamente como "Conteúdos relacionados".

O cidadão não vê documentos soltos — acompanha a história completa de uma ação pública.

### Áreas de atuação

Conteúdo organizado por assunto (Saúde, Educação, Segurança), não apenas por tipo de documento. O cidadão pensa em assuntos, não em categorias burocráticas.

### Linha do tempo do mandato

Gerada automaticamente a partir de tudo que é publicado. Mostra a evolução cronológica completa do mandato.

### O site como complemento às redes sociais — não como concorrente

Hoje, manter-se ativo nas redes sociais é praticamente obrigatório para um mandato que pretende se reeleger. O Portal do Mandato não compete com isso — ele resolve o que o Instagram e outras redes não conseguem resolver:

| | Redes sociais | Portal do Mandato |
|---|---|---|
| Alcance imediato | Forte | Não é o foco |
| Histórico organizado | Fraco (feed rola e se perde) | Forte (linha do tempo automática) |
| Credibilidade institucional | Tem cara de campanha/propaganda | Tom de prestação de contas, sem aparência de marketing eleitoral |
| Conexão entre ações | Posts isolados | Motor de relacionamento (projeto → notícia → ofício → evento, tudo conectado) |
| Vida útil do conteúdo | Curta | Permanente, pesquisável, citável |

A rede social anuncia o que está acontecendo agora. O Portal documenta o que já foi feito, de forma permanente e organizada — é o registro que sustenta a narrativa de mandato ao longo de anos, não só no pico de uma campanha.

Resumo da proposta de valor: **o site documenta o que as redes sociais só anunciam.**

### Risco de uso e mitigação: a motivação de cadastrar conteúdo tende a ser mais forte perto de eleições

Como a motivação principal de manter o portal atualizado está ligada à reeleição, existe risco real de queda de uso nos períodos mais distantes do pleito (ex: ano 1-2 de um mandato de 4 anos). A plataforma deve ativamente combater esse risco, e não apenas torná-lo possível — ver `roadmap.md`, mecanismo de incentivo à publicação contínua.

### Módulos ativáveis

Cada mandato liga apenas os módulos que precisa. Um único código-base atende dezenas de perfis diferentes de cliente.

## Sensação que o sistema deve transmitir

- Transparência
- Organização
- Credibilidade
- Simplicidade
- Institucionalidade

A sensação do cidadão ao acessar deve ser: "Estou acessando uma plataforma séria de prestação de contas."

## Stack técnica

- Next.js
- React
- TypeScript
- Tailwind CSS
- shadcn/ui
- Supabase (banco, auth, storage)
- Vercel (deploy)
