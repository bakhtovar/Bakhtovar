---
name: blast-push-quiet-hours
description: "Пуши Blast после 22:00 по Душанбе не уходят — enqueue_push ставит send_after на 09:00 следующего дня"
metadata:
  type: project
---

`enqueue_push` прогоняет время через `push_send_after`: нетранзакционные виды
(`personal_offer`, `offer_starts`, `offers_nearby`, `news`, …) вне окна
**с 09:00 до 22:00 по Азии/Душанбе** не отбрасываются, а переносятся на 09:00
следующего дня. В `notification_outbox` запись висит `pending` с будущим
`send_after`, и это не поломка диспетчера — `dispatch-push` крутится каждую
минуту и просто её не берёт.

**Why:** дважды принял отложенный тест за несработавший cron.

**How to apply:** проверять `send_after ... at time zone 'Asia/Dushanbe'`
перед тем, как искать причину в cron или в edge-функции. Чтобы протестировать
пуш ночью — `update notification_outbox set send_after = now() where id = ...`;
исправлять сам `push_send_after` ради теста не нужно.

Связано: [[blast-provider-user-scope]] (логи Supabase в UTC, Душанбе +5).
