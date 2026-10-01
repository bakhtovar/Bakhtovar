# 05 · Бэкенд: Supabase

## Проект

| | |
|---|---|
| Имя в панели | **Blast App Dev** |
| Ref | `lfudcjwkyctodeqaommv` |
| URL | `https://lfudcjwkyctodeqaommv.supabase.co` |
| Регион | eu-central |

> **Важно:** в организации есть второй проект, который называется «Blast Pay».
> Он **пустой**. Живой — тот, что называется «Blast App Dev». На это уже
> попадались.

Логи Supabase — в UTC. Душанбе — UTC+5.

## Принцип доступа

Приложение **не ходит в таблицы напрямую** почти нигде. Всё через RPC —
SQL-функции с `security definer` и проверками внутри. Прямые `from()` остались
только для восьми таблиц (`activations`, `categories`, `favorites`, `offers`,
`profiles`, `promotions`, `smartpay_payments`, `user_venue_familiarity`), и те
закрыты RLS.

Каждый вызов, требующий человека, идёт через `authedRpc` —
`lib/core/supabase/authed_rpc.dart`. Причина в самом SDK: запрос без сессии он
всё равно отправляет, подставив публичный ключ, PostgREST выполняет его от
имени `anon`, и приходит `42501`. Снаружи это неотличимо от протухшей сессии,
и приложение предлагало «войти заново», а кнопка выкидывала из аккаунта —
лечила не ту болезнь. `authedRpc` проверяет сессию до запроса.

`getSession()`, а не `currentSession`: первый сам обновит просроченную сессию.

## Таблицы (57)

Ядро: `profiles`, `venues`, `offers`, `activations`, `visits`, `favorites`,
`categories`, `cities`, `districts`.

Заведение: `venue_hours`, `venue_photos`, `venue_members`, `venue_menu_items`,
`venue_menu_sections`, `venue_qr_codes`, `venue_billing`.

Акции и купоны: `offer_slots`, `offer_targets`, `offer_products`,
`offer_reminders`, `coupon_pools`, `coupon_holds`.

Оплата: `smartpay_payments`, `visit_amount_log`, `visit_disputes`,
`staff_confirmations`.

Пуши: `push_tokens`, `notification_outbox`, `notification_log`,
`notification_prefs`.

Доступ и рост: `waitlist`, `founding_emails`, `user_access`,
`invite_entitlements`, `invite_ledger`, `referrals`, `dev_testers`.

Подписка: `subscriptions`, `subscription_plans`, `billing_periods`.

Прочее: `admins`, `admin_actions`, `analytics_events`, `app_config`,
`secure_config`, `ui_strings`, `status_rules`, `status_events`, `words_of_day`,
`word_pool`, `savings_equivalents`, `auth_attempts`, `rpc_call_watermark`,
`review_tags`, `review_reports`, `account_deletions`, `booking_penalties`,
`activation_flags`.

## Ключевые RPC (~190 функций)

| Область | Функции |
|---|---|
| Каталог | `catalog_home_offers`, `catalog_map_venues`, `search_venues`, `venue_detail`, `venue_today`, `venue_menu`, `offer_detail`, `venue_offers_here` |
| Активация | `resolve_qr`, `activate_offer`, `activation_payload`, `redeem_activation`, `confirm_by_staff`, `set_activation_choice`, `expire_stale_activations` |
| Оплата | `payment_context`, `payment_quote`, `payment_status`, `smartpay_prepare_payment`, `smartpay_record_invoice`, `smartpay_apply_webhook`, `set_visit_amount` |
| Визиты | `my_visits_feed`, `my_visits_overview`, `my_visits_by_category`, `my_visit`, `rate_visit`, `open_visit_dispute` |
| Профиль | `my_profile`, `my_favorites`, `my_coupons`, `my_reviews`, `my_access`, `my_subscription`, `update_my_settings`, `delete_my_account` |
| Купоны | `coupon_availability`, `hold_coupon`, `consume_hold`, `release_hold`, `release_stale_holds` |
| Доступ | `join_waitlist`, `has_early_access`, `claim_founding_access`, `redeem_invite_code`, `is_dev_tester` |
| Пуши | `register_push_token`, `enqueue_push`, `dispatch_push_queue`, `can_send_push`, `push_toggle` |
| Мерчант | `merchant_promotions`, `merchant_save_promotion`, `merchant_set_hours`, `merchant_save_menu_item`, … |
| Служебное | `app_config`, `track_events`, `nightly_cleanup`, `watch_rpc_flood`, `word_of_day` |

## Edge Functions (Deno)

| Функция | Зачем |
|---|---|
| `smartpay-invoice` | расчёт суммы и создание счёта; здесь и только здесь живёт `x-app-token` |
| `smartpay-webhook` | подтверждение оплаты от шлюза |
| `send-push` | отправка в APNs и FCM |

Токен SmartPay в приложение не попадает ни в каком виде: apk и ipa
распаковываются, строка достаётся за минуту.

## Миграции

139 файлов в `supabase/migrations/`, последние — от 15 сентября 2026.
Плюс один pgTAP-тест (`make db-test`): проверяет RLS, защиту полей и i18n.

### Незакрытая проблема: расхождение истории

`make db-push` **не работает**. На удалённом проекте 186 записей миграций,
локально 139 файлов. 95 удалённых версий не имеют локального файла, 48 файлов
не записаны на сервере. Причина: одни и те же миграции применялись двумя
путями — файлами и через MCP `apply_migration`, который ставит собственную
метку времени.

> **CLI предложит `supabase migration repair --status reverted`. Не запускать.**
> Это сотрёт записи о миграциях, которые реально применены, и следующая
> попытка накатить их вторым заходом сломает прод.

Чинить надо сверкой: для каждой удалённой версии найти локальный файл с тем же
содержанием и переименовать под её метку. Задача открыта.

## Что делать при изменении схемы

```bash
make db-new NAME=что_делаем    # создать файл
# написать SQL
make db-reset                  # накатить локально с нуля
make db-test                   # pgTAP
```

На прод — пока вручную через панель или MCP, потому что `db-push` сломан
(см. выше).
