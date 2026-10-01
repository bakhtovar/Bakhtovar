---
name: blast-supabase-projects
description: "Живой Supabase-проект Blast — «Blast App Dev» (lfudcjwkyctodeqaommv), а проект с именем «Blast Pay» пустой"
metadata: 
  node_type: memory
  type: project
  originSessionId: 99e156e4-9a30-4ed4-a80f-4ed8c3729431
  modified: 2026-09-02T15:35:21.786Z
---

Вся схема Blast (waitlist, user_access, founding_emails, venues, offers и остальное)
живёт в проекте **«Blast App Dev»** — ref `lfudcjwkyctodeqaommv`, регион eu-central-1.

Проект с именем **«Blast Pay»** (`ddbkjhnbqdmdwqvivzhu`, ap-south-1, создан раньше)
полностью пустой — ни одной таблицы.

**Why:** имена вводят в заблуждение: по названию кажется, что боевой — «Blast Pay»,
а на самом деле это заброшенная заготовка.

**How to apply:** любые миграции, RPC и запросы к данным Blast — в
`lfudcjwkyctodeqaommv`. Перед первой записью в новой сессии стоит убедиться
через `list_tables`, что проект не пустой. Связано с [[blast-owner-bypass]].
