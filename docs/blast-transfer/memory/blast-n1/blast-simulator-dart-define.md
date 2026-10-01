---
name: blast-simulator-dart-define
description: Сборка Flutter-приложения Blast без --dart-define даёт пустой белый экран без единой ошибки в логах; запускать только через make run
metadata: 
  node_type: memory
  type: project
  originSessionId: f30be1db-8a75-4624-9fbf-73dd06005e4d
  modified: 2026-09-07T10:47:53.222Z
---

`flutter build ios --simulator` и `flutter run` без `--dart-define`
собираются успешно и ставятся на симулятор, но приложение показывает
**пустой белый экран** — без исключений, без строк в `log show`, только
логи компиляции шейдеров Metal.

Причина: `lib/core/env/env.dart` читает `SUPABASE_URL` и
`SUPABASE_ANON_KEY` через `String.fromEnvironment`. Без них они пустые,
бутстрап Supabase не поднимается, и Flutter не рисует ничего.

**How to apply:** запускать только `make run DEVICE=<udid>` — цель читает
`.env.local` (не `.env`: `ENV_FILE := .env.local`) и передаёт четыре
`--dart-define`, включая `GOOGLE_IOS_CLIENT_ID` и `GOOGLE_WEB_CLIENT_ID`,
без которых цель падает с понятной ошибкой. В `.env` этих двух ключей нет,
он для другого.

**Why:** 7 сентября 2026 я собрал через `flutter build ios --simulator`,
получил белый экран и полторы минуты искал причину в логах симулятора —
там её нет. Молчание тут значит не «работает», а «нечем работать». Тот же
класс ошибки, что и в [[blast-simulator-bundle-id]]: команда завершилась
с нулевым кодом, а результата нет.
