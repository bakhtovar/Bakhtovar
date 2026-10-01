---
name: blast-supabase-projects
description: "Два Supabase-проекта Blast, какой из них основная база приложения, и что большинство security-warnings там ложные"
metadata:
  node_type: memory
  type: project
  originSessionId: e03a1e9f-971f-419b-9255-1a10d15908a4
  modified: 2026-09-29T17:42:55.728Z
---

У Blast два Supabase-проекта в орге `kwhodniyoofekgjbdmka`:
- `lfudcjwkyctodeqaommv` — **Blast App Dev** (eu-central-1), основная база приложения: offers/venues/activations/visits/waitlist, ~160 RPC-функций, бакеты catalog-assets, venue-photos, review-photos.
- `ddbkjhnbqdmdwqvivzhu` — **Blast Pay** (ap-south-1), отдельная маленькая база: merchant-аккаунты, схема `identity`.

Проверено 2026-09-13: почти все ~200 warnings линтера вида `*_security_definer_function_executable` в App Dev — ложные срабатывания. Линтер только перечисляет SECURITY DEFINER функции, доступные через RPC, и не смотрит внутрь; внутри guard'ы на месте (`is_admin()` во всех `admin_*`, `is_member()` во всех `merchant_*`/`venue_*`, фильтр по `auth.uid()` в остальных), у всех задан явный `search_path`.

**Why:** без этой проверки список из 200 WARN выглядит как катастрофа и провоцирует массовые revoke, которые сломают рабочие RPC.

**Грабли с грантами (важно):** EXECUTE у RPC-функций там роздан не только ролям, но и `PUBLIC` (ACL показывает `=X/postgres`). Поэтому `revoke execute ... from anon` — бесполезен, доступ остаётся через PUBLIC; снимать надо `from public`. И перед этим проверять, есть ли у функции **явный** грант `authenticated`: если его нет (доступ шёл только через PUBLIC), снятие PUBLIC отберёт доступ и у залогиненных — сначала `grant ... to authenticated`.

13.09.2026 применены две миграции: `close_public_bucket_listing` (сняты SELECT-политики с публичных бакетов — листинг анонимом закрыт, отдача картинок по /object/public/ не зависела от них) и `revoke_dead_anon_grants` (anon отрезан от payment_context, payment_status, admin_password_changed, is_dev_tester; roll_word_of_day отрезана от authenticated — она крутится в pg_cron). `my_admin` и `my_referrals` намеренно оставлены с anon-грантом: они могут вызываться клиентом до подъёма сессии, и 403 вместо пустого ответа мог бы сломать старт приложения.

**How to apply:** при новом прогоне advisors сверяться с этим фактом и проверять только дельту — функции без `is_admin`/`is_member`/`auth.uid` внутри. Связано с [[blast-site-infra]].

**Миграции App Dev ↔ репозиторий (с 29.09.2026).** `supabase/migrations` в репозитории приложения — точная выгрузка `supabase_migrations.schema_migrations` рабочей базы (304 шт. на 29.09), плюс три правки для сборки с нуля (PostGIS в public, префикс в task4_hardening, терпимый revoke). До этого репозиторий разошёлся с базой на ~90 миграций, применённых через MCP без файлов. Правило: после `apply_migration` через MCP класть файл **под тем версией, которую присвоила база** (`select version from supabase_migrations.schema_migrations order by version desc limit 1`), а не под своим круглым номером. Выгрузить историю без пароля от БД: `supabase db query --project-ref lfudcjwkyctodeqaommv --linked --output-format json "select version, name, statements from supabase_migrations.schema_migrations"`. Неприменённое лежит в `supabase/unapplied/`.

Второй путь (с 29.09.2026): файл кладётся в `supabase/migrations` под своим номером заранее, а применяет его `python3 tool/apply_migrations.py <версия>…` — одной транзакцией вместе со строкой истории под тем же номером. Им же пользуется Бахтовар, когда запись в рабочую базу из сессии заблокирована: изменения схемы, расписания pg_cron и правки функций система пропускает не всегда. Проверять правку до применения — `supabase db query --linked -f` с `begin … rollback`.
