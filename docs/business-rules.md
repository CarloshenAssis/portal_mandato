# Portal do Mandato — Regras de Negócio

## Regra fundamental

O conteúdo pertence ao mandato. O sistema apenas estrutura, organiza e distribui.

O sistema nunca gera, altera ou sugere conteúdo político. Tudo é inserido pelo gabinete.

---

## Entidade central: Mandato

O sistema não cadastra políticos. Cadastra mandatos.

Cada mandato possui:

- Nome do titular
- Foto institucional
- Cargo (vereador, prefeito, deputado estadual, deputado federal, senador, secretário)
- Cidade
- Estado
- Partido
- Legislatura
- Período do mandato
- Biografia
- Valores
- Missão
- Equipe
- Redes sociais
- Domínio próprio
- Logotipo
- Tema (cor primária)

O tipo de cargo determina quais módulos ficam disponíveis no painel e no portal.

---

## Módulos

### Núcleo (sempre presente em todo mandato)

- Portal público
- Painel administrativo
- Gestão de usuários
- Notícias
- Agenda / Eventos
- Galeria (fotos e vídeos)
- Biblioteca (documentos)
- Contato
- Equipe
- Prestação de Contas
- Solicitações (formulário do cidadão)
- SEO
- Analytics
- Identidade visual configurável

### Módulos legislativos (vereador, deputado, senador)

- Projetos de Lei
- Indicações
- Moções
- Requerimentos
- Ofícios
- Comissões
- Frentes Parlamentares
- Emendas Parlamentares
- PEC (deputado federal, senador)

### Módulos executivos (prefeito, secretário, governador)

- Obras
- Programas de Governo
- Secretarias
- Metas
- Decretos
- Convênios
- Licitações (links externos)
- Plano de Governo
- Execução orçamentária

### Ativação de módulos

Cada mandato ativa apenas os módulos de que precisa. Módulos inativos não aparecem no menu do painel nem no portal público. O menu é gerado dinamicamente a partir dos módulos ativos.

---

## Estrutura padrão de conteúdo

Toda entidade publicada (notícia, projeto, indicação, ofício, obra, evento, documento) segue exatamente a mesma estrutura de dados:

| Campo | Descrição |
|---|---|
| Título | Obrigatório |
| Resumo | Texto curto para listagens e SEO |
| Conteúdo | Rich text completo |
| Categoria | Classificação principal |
| Área de atuação | Saúde, Educação, Segurança etc. |
| Bairros relacionados | Vinculação geográfica |
| Tags | Palavras-chave livres |
| Anexos / Arquivos | PDF, DOC, XLS, ZIP |
| Galeria | Fotos e vídeos vinculados |
| Conteúdos relacionados | Links para outros conteúdos do mandato |
| SEO | Título, descrição, slug, imagem, Open Graph, schema |
| Autor | Quem publicou |
| Data de publicação | Data visível ao cidadão |
| Status | Rascunho, Publicado, Agendado, Arquivado |

### Campos específicos por tipo

Além da estrutura padrão, cada tipo de conteúdo possui campos próprios:

**Projetos de Lei**: número, tipo (PL, PR, Moção, Emenda), status (em tramitação, aprovado, rejeitado, sancionado), linha do tempo legislativa.

**Indicações**: número, bairro, status, data.

**Ofícios**: número, destinatário, assunto, situação.

**Requerimentos**: número, destinatário, assunto, situação.

**Obras**: status (planejada, em andamento, concluída), antes/depois, mapa, percentual de execução.

**Eventos**: data/hora de início, data/hora de término, local, tipo (audiência, visita, sessão, evento).

---

## Motor de relacionamento

Todo conteúdo pode ser vinculado a qualquer outro conteúdo do mesmo mandato. Os relacionamentos são bidirecionais: se o Projeto A está relacionado à Notícia B, a Notícia B automaticamente mostra o Projeto A como relacionado.

Exemplo de cadeia:

```
Projeto de Lei
  → Notícia sobre a apresentação
  → Galeria da audiência pública
  → Ofício enviado ao executivo
  → Evento da audiência
  → Notícia sobre a aprovação
  → Documento da lei sancionada
```

Tudo aparece na página do projeto como "Conteúdos relacionados", e em cada um dos itens relacionados também.

---

## Áreas de atuação

Todo conteúdo pode ser vinculado a uma ou mais áreas de atuação:

- Saúde
- Educação
- Obras / Infraestrutura
- Segurança
- Esporte
- Cultura
- Mobilidade
- Meio Ambiente
- Assistência Social

As áreas são configuráveis pelo mandato (pode adicionar ou renomear). No portal público, o cidadão pode navegar por área de atuação e ver todos os conteúdos relacionados àquele tema, independentemente do tipo.

---

## Bairros

Todo conteúdo pode ser vinculado a um ou mais bairros. Os bairros são cadastrados pelo mandato. No portal público, o cidadão pode filtrar conteúdo por bairro.

---

## Linha do tempo

A linha do tempo do mandato é gerada automaticamente a partir de todos os conteúdos publicados, ordenados cronologicamente. Não requer cadastro manual. Cada item da linha do tempo exibe: data, tipo, título e link para o conteúdo completo.

---

## Portal público

### Página inicial

Responde quatro perguntas: Quem é? O que faz? O que já entregou? Como entrar em contato?

Seções:

1. **Hero**: foto, nome, cargo, partido, cidade, frase institucional, botão "Conheça o Mandato", botão "Fale com o Gabinete".
2. **Mandato em números**: indicadores automáticos (projetos apresentados, projetos aprovados, leis sancionadas, indicações, ofícios, recursos conquistados, emendas, audiências, eventos, bairros visitados). Calculados automaticamente a partir do conteúdo publicado.
3. **Últimas atualizações**: lista cronológica de notícias, projetos, eventos.
4. **Próximos eventos**: agenda futura.
5. **Destaques**: conteúdos fixados manualmente pelo gabinete.
6. **Rodapé**: contato, redes sociais, LGPD, política de privacidade.

### Seções do portal

- **Sobre**: biografia, história, formação, experiência, missão, valores, bandeiras, como funciona o mandato.
- **Equipe**: membros do gabinete.
- **Notícias**: listagem com filtros por categoria, área de atuação, bairro, data.
- **Projetos** (e demais módulos legislativos): listagem com filtros, página individual com linha do tempo e relacionados.
- **Agenda**: calendário com eventos, audiências, visitas, sessões.
- **Galeria**: fotos e vídeos organizados em álbuns.
- **Biblioteca**: todos os documentos do mandato, com filtros por tipo (PDF, DOC, imagem, lei, projeto, ata).
- **Solicitações**: formulário para o cidadão enviar mensagem (nome, telefone, email, bairro, categoria, mensagem, anexo, consentimento LGPD). Sem promessa de atendimento ou prazo.
- **FAQ**: perguntas frequentes administráveis pelo gabinete.
- **Contato**: WhatsApp, telefone, email, Instagram, Facebook, localização.
- **Pesquisa**: busca global em todo o conteúdo, inclusive em PDFs.

### Navegação do cidadão

O cidadão pode acessar qualquer informação por: menu principal, pesquisa, categoria, bairro, área de atuação, tipo de conteúdo, data.

---

## Painel administrativo

### Dashboard

Exibe: total de conteúdos publicados, visualizações recentes, novas mensagens (solicitações), próximos eventos, documentos recentes.

### Gestão de usuários

Papéis:

| Papel | Permissões |
|---|---|
| Administrador | Acesso total, gerencia usuários e configurações |
| Assessor | Cria, edita, publica e exclui conteúdo |
| Editor | Cria e edita conteúdo, não publica nem exclui |
| Somente leitura | Visualiza o painel sem alterar nada |

### Gestão de conteúdo

Operações disponíveis para todo tipo de conteúdo:

- Criar
- Editar
- Salvar como rascunho
- Publicar
- Agendar publicação
- Arquivar
- Duplicar
- Excluir
- Visualizar preview

### Configurações do mandato

- Logotipo
- Nome do mandato
- Cor primária
- Tipografia
- Favicon
- Domínio
- SEO global (título, descrição, imagem)
- Menu (editar itens, ordem, links)
- Banner / Hero
- Redes sociais

### Auditoria

Todo conteúdo possui histórico completo: quem criou, quem editou, quando, o que mudou. O log de auditoria é imutável.

### Formulários / Solicitações

O gabinete pode: visualizar mensagens recebidas, responder (por email), exportar lista, marcar como lida/respondida.

---

## Regras de upload

Tipos aceitos: PDF, JPG, PNG, GIF, WEBP, MP4, DOC, DOCX, XLS, XLSX, ZIP.

Imagens são comprimidas automaticamente para otimizar performance. Vídeos podem ser embedados via URL (YouTube, Vimeo) ou uploaded diretamente.

---

## SEO

Cada conteúdo possui campos de SEO individuais: título, descrição, slug, imagem Open Graph, schema markup. A plataforma gera automaticamente sitemap.xml e robots.txt.

---

## Analytics

Suporte a Google Analytics e Plausible. Estatísticas internas de visualização por conteúdo. No portal público, seção de "mais acessados" e "conteúdos populares" calculada automaticamente.

---

## LGPD

Formulário de solicitações exige consentimento explícito. Portal exibe política de privacidade configurável. Dados de solicitações podem ser exportados e excluídos pelo administrador.
