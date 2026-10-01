---
name: blast-send-push-verify-jwt
description: "send-push деплоить только с verify_jwt = false: pg_cron зовёт её без Authorization и при включённой проверке получает 401"
metadata:
  type: project
---

`send-push` вызывается из `cron.schedule('dispatch-push', ...)` через `pg_net`,
и тот шлёт только `x-cron-key`, без `Authorization`. С `verify_jwt = true`
платформа отвечает **401 UNAUTHORIZED_NO_AUTH_HEADER** ещё до кода функции —
очередь копится, `notification_outbox` висит `pending`, а `cron.job_run_details`
показывает `succeeded`, потому что успешным считается сам http-вызов.

Смотреть ответ надо в `net._http_response` (status_code, content).

Функция защищена собственным `CRON_SECRET`: без `x-cron-key` она отвечает 403,
так что выключенная проверка JWT дыры не открывает.

**How to apply:** в `supabase/config.toml` у `[functions.send-push]` держать
`verify_jwt = false`; деплоить `supabase functions deploy send-push
--project-ref lfudcjwkyctodeqaommv` из корня flutter-проекта. Никогда не
деплоить эту функцию MCP-инструментом `deploy_edge_function` — он требует
полный текст файла в аргументе и легко затирает функцию заглушкой.

Связано: [[blast-push-quiet-hours]].
