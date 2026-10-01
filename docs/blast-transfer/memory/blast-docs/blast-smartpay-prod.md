---
name: blast-smartpay-prod
description: Боевой SmartPay в Blast — как устроены вебхук, подпись и Telegram, и что было не очевидно при переходе 29.09.2026
metadata:
  type: project
---

С 29.09.2026 Blast принимает реальные деньги через боевой SmartPay (`ecomm.smartpay.tj`) в проекте **App Dev** (`lfudcjwkyctodeqaommv`) — отдельного прод-проекта нет. Функции `smartpay-invoice`, `smartpay-webhook`, RPC `smartpay_apply_webhook`.

Неочевидное из документации SmartPay (кабинет dashboard.smartpay.tj → Танзимот → Webhooks):
- **Секрет HMAC — третий ключ**, не токен вебхука и не API-ключ. Показывается один раз при создании вебхука с галочкой подписи; потерян — вебхук удалять и создавать заново. Строка подписи `payment_date(пробел, без микросекунд)|amount|order_id|bank_id|invoice_uuid`.
- Токен `X-Api-Token` генерируется формой «Add Webhook» заранее — у уже созданных вебхуков он другой.
- Застрявший платёж закрывается не SQL-ом, а кнопкой в списке инвойсов «Webhooks → View Status → Повторная отправка Webhook».
- Telegram-вебхук SmartPay шлёт через своего бота `@smartpay_ecomm_bot` в группу по Chat ID. Наш бот (`telegram_send`, ключи в `secure_config`) об оплатах не пишет — только лист ожидания и флуд RPC.
- `payment_date` приходит по Душанбе без смещения; `amount` в вебхуке в дирамах, а в счёт отправляется в сомони.

**Грабли 29.09.2026 (стоили полдня):** вебхук в кабинете был создан на адрес *старого* проекта `ddbkjhnbqdmdwqvivzhu` — SmartPay слал туда и получал 404, а у нас было тихо, без единого отказа. Карточка инвойса «Webhook → попыток 0» при этом врёт — считает только текущий вебхук. Если у нас пусто, первым делом смотреть логи `ddbkjhnbqdmdwqvivzhu` (function_edge_logs) и URL в списке вебхуков. Редактирования у вебхука нет — только удалить и создать заново (новый токен → `supabase secrets set`, CLI на Mac залогинен).
Подпись HMAC проверена на реальных уведомлениях 052/054 и включена (`SMARTPAY_WEBHOOK_SECRET`) — формула в `smartpay-webhook` совпадает с документацией.

В teamcave отдельного экрана оплат нет: `smartpay_payments` видны только в карточке гостя (`admin_client_portrait`, счётчик по банкам).

**Why:** половина провалов первого дня (bad_token, bad signature, «какой ключ подписи») — от незнания этих деталей.

**How to apply:** при проблемах с приёмом сначала смотреть `analytics_events` с `name = 'smartpay_webhook_rejected'`. Код приложения — [[blast-app-repo]], базы — [[blast-supabase-projects]], админки — [[blast-admin-panels]].
