---
name: blast-project-copies
description: Где на диске лежат рабочие копии проектов Blast и какие из них настоящие — есть дубли и заброшенные прототипы
metadata: 
  node_type: memory
  type: project
  originSessionId: 99e156e4-9a30-4ed4-a80f-4ed8c3729431
  modified: 2026-09-02T20:46:02.784Z
---

Рабочие копии, из которых пользователь реально запускает и которые привязаны к GitHub:

- **Страница раннего доступа** — `~/Documents/blast waiting list/Blast-Pay-Landing` (репо `bakhtovar/Blast-Pay-Landing`, приватное). Живёт на `https://blastwaitlist.netlify.app`. Моя рабочая копия того же репо — `~/Desktop/blast pay/blast n1/waitlist`.
- **Внутренняя админка** — `~/Documents/blast waiting list/Blast-Internal-Admin` (репо `bakhtovar/Blast-Internal-Admin`, приватное). Есть второй клон на `~/Desktop/blast pay/blast-internal-admin` — старый, не трогать.
- **Flutter-приложение** — `~/Desktop/blast pay/blast n1/blast`.
- **Кабинет заведения** — `~/Desktop/blast pay/Blast-Pay-Admin-Panel` (репо `bakhtovar/Blast-Pay-Admin-Panel`, ветка main), работает на схеме приложения через `merchant_*` и живёт на blastpay.tj/admin. Клон `~/Documents/blast pitch deck/Blast-Merchant-Dashboard-fixed` того же репо устарел (на 29.09 отстаёт на 49 коммитов, своей работы в нём нет) — не править.

Не наша страница: `~/Desktop/blast pay/blast-waitlist-app` — другой прототип, без git.

**Why:** копий много, названия похожи, `.env` в gitignore и при клонировании не приезжает — пользователь один раз запускал клон без `.env` и получил сломанную форму; и один раз прислал `.env` от мерчантского дашборда вместо нужного.

**How to apply:** править и коммитить только в копиях из Documents; перед запуском проверять наличие `.env`; ключи Supabase брать из [[blast-supabase-projects]], а не из чужих `.env`.
