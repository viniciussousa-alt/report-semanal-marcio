# Atualização do Report Semanal (Vinicius → Márcio)

Este repositório guarda o report semanal de BI & Produto do Vinicius Carvalho (Shippify) para o Márcio Seixas.

- `index.html` — página (layout, filtros, gráficos, logos). **Não alterar** numa atualização de rotina.
- `data.js` — todos os dados (`SNAPSHOT`, `TODAY`, `DATA`). **É o único arquivo que a atualização edita.**

## Rotina de atualização

1. Ler `SNAPSHOT` em `data.js`: é o momento da última atualização. Buscar só o que mudou desde então.
2. Buscar nas 4 fontes (fuso America/Sao_Paulo):
   - **Jira** (cloudId `1ddff5fa-f323-4c34-be36-1ffd36da6b03`): `project in (BI, PROD) AND assignee = "712020:1c181567-8817-4945-ba51-6cc058e5f09b" AND issuetype != Epic AND updated >= "<SNAPSHOT>"`, com status, comentários, horas (`timespent`), criação, conclusão.
   - **Google Agenda**: eventos de `TODAY - 7 dias` até `TODAY + 14 dias`. Atualizar horários, incluir reuniões novas, remover canceladas.
   - **Gmail**: `after:<data do SNAPSHOT>` excluindo `notifications@shippify.co`, `jira@shippify.atlassian.net`, `executiveassistant@e.read.ai`, `gemini-notes@google.com`, `drive-shares-dm-noreply@google.com`, `noreply@jelou.ai`, `calendar-notification@google.com`, promoções, social, respostas automáticas de ausência e confirmações de convite.
   - **Slack** (usuário `U08RJSNG5C2`): mensagens enviadas por ele e para ele desde o SNAPSHOT.
3. Atualizar `DATA` seguindo as regras abaixo.
4. Definir `SNAPSHOT` = agora (`AAAA-MM-DDTHH:MM`) e `TODAY` = hoje (`AAAA-MM-DD`). Se a agenda for além de `DATA.fim`, ampliar `DATA.fim`.
5. Validar que `data.js` é JavaScript válido (`node -e "eval(require('fs').readFileSync('data.js','utf8')+';console.log(DATA.jira.length)')"`).
6. Commit e push na `main`.

## Regras de conteúdo (obrigatórias)

- **Só assuntos de trabalho.** NUNCA incluir: almoço, conversas pessoais, fofoca, risadas (kkk, haha, rsrs), aniversários, happy hour, confraternização, palestras internas, eventos sociais (ex.: "Bate Papo com RH", "Kid's Day"), academia/Gympass, alertas de segurança de conta.
- Agenda: só reuniões aceitas, organizadas pelo Vinicius ou "talvez". Fora: recusadas, standups que ele recusa (Daily - Ecosystem, Operations Experience Stand-up, Daily - Apps Experience), almoço.
- Todo item precisa do campo `info`: 1 frase em português explicando **o que aquilo significa na prática** (aparece na tela). Sem jargão.
- Textos em português, diretos, sem nomes de terceiros desnecessários e sem dados pessoais (telefone, CPF).

## Esquema de `DATA`

- `jira[]`: `key, board ("BI"|"PROD"), frente ("dash"|"jelou"|"ops"), titulo, status ("todo"|"doing"|"blocked"|"done"), prio, created, since (data em que entrou no status atual), updated, horas (timespent/3600), done (data de conclusão ou null), proxy (true se a data de conclusão veio da mudança de status), due, ctx, next, updates[] ({d, h, t} — 1 por comentário novo, resumido), info`.
  - Status Jira: BLOCKED → `blocked`; Concluído/Done → `done`; Tarefas pendentes/To Do → `todo`; demais → `doing`.
  - `frente`: Jelou/WhatsApp/CRM → `jelou`; dashboards/BI/dados → `dash`; resto → `ops`.
- `agenda[]`: `d, s, e, t, fr, com (participantes resumidos), marcio (true se o Márcio participa), destaque, info, rel[] (chaves Jira relacionadas à reunião)`.
- `comms[]`: `d, h, src ("slack"|"email"), t, det, tag ("Entrega"|"Pedido"|"Decisão"|"Bloqueio"|"Pendência"|"Custo"|"Aprovação"|"Info"), fr, marcio, link (Gmail: https://mail.google.com/mail/u/0/#all/<threadId>), info`.
- `riscos[]`: não é exibido; pode ficar vazio.
- `ferias`: período de férias (não alterar).

Não duplicar itens: atualizar o existente pela `key` (Jira), por `d+s+t` (agenda) ou por `d+h+t` (comms).
