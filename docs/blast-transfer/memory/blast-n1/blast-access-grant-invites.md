---
name: blast-access-grant-invites
description: "Доступ, выданный сырым SQL в user_access, не начисляет приглашения — их даёт только admin_grant_user_access"
metadata: 
  node_type: memory
  type: project
  originSessionId: 1f6302e1-6fdf-47b9-a8b5-7136e1807246
  modified: 2026-09-24T10:06:04.472Z
---

Приглашения начисляет `admin_grant_user_access` (через `grant_initial_invites`), а не `grant_access`. Если раздать доступ сырым `insert into public.user_access ... access_source = 'super_admin'`, люди войдут, но с нулём приглашений — пригласить друга не смогут, и на вид это «не работает», хотя ошибок нигде нет.

24 сентября 2026 так и вышло: 36 человек с доступом и 0 приглашений. Починили пачкой `select public.grant_initial_invites(id, email, 'early_access_initial', 3)`.

`grant_initial_invites` защищена таблицей `invite_entitlements` (одна строка на адрес навсегда) — повторный вызов вернёт 0, а не начислит второй раз. Чтобы перевыдать, строку из `invite_entitlements` надо сначала убрать.

Связано: [[blast-owner-bypass]], [[blast-sql-function-overloads]]
