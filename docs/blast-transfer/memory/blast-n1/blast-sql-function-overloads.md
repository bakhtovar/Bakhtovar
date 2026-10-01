---
name: blast-sql-function-overloads
description: Правку RPC в Blast начинать с pg_get_functiondef живой версии — иначе рядом встаёт второй оверлоад
metadata:
  type: project
---

В Blast чинить серверную функцию «по памяти» нельзя: `create or replace`
с другим типом аргумента не заменяет функцию, а создаёт **вторую** рядом.
Так уже дважды: `hold_coupon(uuid)` мимо настоящей `(uuid, text)` и
`set_visit_amount(uuid, bigint)` мимо `(uuid, integer)`. Обе копии теряли
половину тела — лимиты, запись в лог, реферальную награду — и PostgREST
мог позвать любую из двух.

**Почему:** локальные миграции отстают от облака (часть применена через
MCP и на диск не попала), поэтому файл в `supabase/migrations/` — не
источник правды о том, что сейчас живёт в базе.

**How to apply:** перед любым `create or replace` сначала
`select pg_get_functiondef(p.oid) from pg_proc p ...` (или
`'public.fn(типы)'::regprocedure`), скопировать тело дословно и вставить
только свою правку. После применения — проверить
`select oid::regprocedure from pg_proc where proname = '...'`: строк
должно остаться столько же, сколько было. Связано с
[[blast-supabase-projects]].
