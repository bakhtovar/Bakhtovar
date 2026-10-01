---
name: blast-db-cloud-only-objects
description: 45 функций и 4 таблицы живой базы не описаны ни в одном SQL на диске — база не воспроизводится из main
metadata:
  node_type: memory
  type: project
  originSessionId: 1f6302e1-6fdf-47b9-a8b5-7136e1807246
  modified: 2026-09-29T10:23:03.632Z
---

Проверено 29.09.2026: из 244 наших функций в `public` у 45 нет определения
ни в одном из 263 SQL-файлов на диске (приложение, Blast-Internal-Admin,
лендинг, сайт). Это почти вся серверная часть teamcave (`admin_*`,
`my_admin`, `sync_my_admin`), веб-очередь и Telegram (`waitlist_*_web`,
`waitlist_notify_*`, `telegram_send`, `set_telegram_notify`, `web_client_ip`,
`cleanup_waitlist_attempts`, `phone_pretty`) и функции самого приложения:
`email_login_kind`, `venue_visible` (на ней стоит каталог), `loc_text`,
`report_device`. Таблиц без CREATE TABLE четыре: `admin_notes`,
`password_accounts`, `pending_admins`, `waitlist_web_attempts`.

Их накатывали в облако через MCP, не сохраняя файлов. `supabase db reset` из
main даст базу без входа по паролю, без каталога и без админки.

**Why:** облако — единственная копия; потеря проекта = потеря этих объектов.

**How to apply:** текст миграций лежит в
`supabase_migrations.schema_migrations.statements` (есть у 300 из 304) —
восстанавливать оттуда или снимать `pg_get_functiondef`. Каждую новую
миграцию через apply_migration сохранять файлом в
`blast/supabase/migrations/`. Связано с [[blast-sql-function-overloads]].
