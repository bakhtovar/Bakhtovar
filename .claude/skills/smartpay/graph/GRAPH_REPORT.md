# Graph Report - corpus  (2026-09-08)

## Corpus Check
- Corpus is ~6,115 words - fits in a single context window. You may not need a graph.

## Summary
- 171 nodes · 359 edges · 9 communities
- Extraction: 78% EXTRACTED · 21% INFERRED · 1% AMBIGUOUS · INFERRED: 74 edges (avg confidence: 0.88)
- Token cost: 133,240 input · 0 output

## Community Hubs (Navigation)
- Инвойс: параметры и статусы
- Ключи, среды и роли доступа
- Webhooks и электронная подпись
- Эндпоинты и флоу интеграции
- Банки и диплинки
- Суммы, единицы и HTTP-ошибки
- Субагенты и переводы
- Blast: правила Apple и продукт
- Поле result и бизнес-статус

## God Nodes (most connected - your core abstractions)
1. `POST /merchant/invoices` - 32 edges
2. `GET /merchant/banks` - 18 edges
3. `Webhook-уведомление об оплате` - 16 edges
4. `SmartPay — платёжный шлюз для Blast` - 14 edges
5. `deeplink_bank_id` - 14 edges
6. `Таблица DEEPLINK_BANK_ID → банк` - 13 edges
7. `x-app-token (заголовок аутентификации)` - 11 edges
8. `Таблица платежей в Supabase` - 11 edges
9. `Edge Function smartpay-webhook` - 10 edges
10. `Раздел Платежи (Ҳисобномаҳо)` - 10 edges

## Surprising Connections (you probably didn't know these)
- `Путь интеграции: ключ → health → invoices → ссылка → webhook` --semantically_similar_to--> `Пошаговый Flow интеграции (5 шагов)`  [INFERRED] [semantically similar]
  skill/SKILL.md → docs-pdf/Введение.txt
- `Форма добавления субагента и данные платформы` --semantically_similar_to--> `agent_id`  [INFERRED] [semantically similar]
  docs-pdf/Субагенты.txt → skill/api.md
- `№ инвойса формата XXX-XXXXXXXXX` --semantically_similar_to--> `smartpay_id`  [INFERRED] [semantically similar]
  docs-pdf/Платежи.txt → skill/api.md
- `Статус инвойса open` --semantically_similar_to--> `Статус open`  [INFERRED] [semantically similar]
  skill/api.md → docs-pdf/Платежи.txt
- `Статус инвойса Charged (с заглавной C)` --semantically_similar_to--> `Статус Charged`  [INFERRED] [semantically similar]
  skill/api.md → docs-pdf/Платежи.txt

## Import Cycles
- None detected.

## Hyperedges (group relationships)
- **Подтверждение оплаты: webhook как единственный источник истины** — corpus_skill_webhooks_notification, corpus_skill_webhooks_x_api_token, corpus_skill_webhooks_hmac_sha256, corpus_skill_webhooks_retry_policy, corpus_skill_webhooks_dedup, corpus_skill_blast_integration_fn_smartpay_webhook, corpus_skill_skill_rule_no_polling, corpus_skill_blast_integration_supabase_realtime [EXTRACTED 1.00]
- **White-Label диплинк-флоу от GET /banks до приложения банка** — corpus_skill_api_get_banks, corpus_skill_api_field_deeplink_bank_id, corpus_skill_api_post_invoices, corpus_skill_api_field_deeplink_url, corpus_skill_api_field_payment_link, corpus_skill_deeplinks_url_launcher_fallback, corpus_docs_pdf_white_label_deeplinks_bank_table [EXTRACTED 1.00]
- **Splitting accounts: субагент, комиссия и сверка переводов** — corpus_docs_pdf_subagenty_splitting_accounts, corpus_docs_pdf_subagenty_commission_model, corpus_docs_pdf_subagenty_subagent_form, corpus_skill_dashboard_subagents, corpus_skill_blast_integration_splitting_model, corpus_docs_pdf_perevody_consolidation, corpus_docs_pdf_perevody_memorial_order [INFERRED 0.85]

## Communities (9 total, 0 thin omitted)

### Community 0 - "Инвойс: параметры и статусы"
Cohesion: 0.09
Nodes (31): Четыре карточки статистики, Детальный реестр и выгрузка в Excel, Сводная таблица по сервисам (Хизматрасонӣ), Раздел Платежи (Ҳисобномаҳо), Действия по инвойсу: ссылка, PDF, чек, webhook, отмена hold, Статус Authorized, Статус Charged, Статус open (+23 more)

### Community 1 - "Ключи, среды и роли доступа"
Cohesion: 0.11
Nodes (29): Вкладка API Keys (Танзимот → Settings), Production API-ключ, Test API-ключ, Sandbox-ключ не работает в production, x-app-token, Панель мониторинга (Dashboard), Вкладка Доступы (управление пользователями), Оператор (+21 more)

### Community 2 - "Webhooks и электронная подпись"
Cohesion: 0.13
Nodes (28): Электронная подпись (HMAC-SHA256), Создание HTTPS Webhook, Telegram Webhook, X-Api-Token — 64-символьный hex-токен, № инвойса формата XXX-XXXXXXXXX, invoice_uuid, smartpay_id, Edge Function smartpay-webhook (+20 more)

### Community 3 - "Эндпоинты и флоу интеграции"
Cohesion: 0.14
Nodes (24): GET /merchant/health, merchant_unit_uuid, payment_link, POST /merchant/invoices, GET /order/status/{order_id}, Пошаговый Flow интеграции (5 шагов), White-Label: банковские диплинки на сайте, deeplink_url (+16 more)

### Community 4 - "Банки и диплинки"
Cohesion: 0.29
Nodes (17): Графики: распределение по банкам и ежедневные платежи, Таблица DEEPLINK_BANK_ID → банк, GET /merchant/banks, deeplink_bank_id, GET /merchant/banks, Алиф Банк (deeplink_bank_id 8), Амонатбонк (deeplink_bank_id 2), Арванд (deeplink_bank_id 10) (+9 more)

### Community 5 - "Суммы, единицы и HTTP-ошибки"
Cohesion: 0.18
Nodes (15): Ошибки аутентификации (401 API Key is required / Invalid API key, 403 Not authenticated), Формат HTTP-ошибки {"detail": ...}, HTTP 400 Bad Request, HTTP 401 Unauthorized, HTTP 403 Forbidden, HTTP 404 Not Found, HTTP 409 Conflict, HTTP 500 Server Error (+7 more)

### Community 6 - "Субагенты и переводы"
Cohesion: 0.26
Nodes (12): Бухгалтер, Консолидация оплаченных инвойсов в один перевод, Мемориальный ордер, 20-значный расчётный счёт получателя, Раздел Переводы (Гузоришҳо), Комиссионная модель 97.5% + 1.5% + 1%, Субагенты (Splitting Accounts), Blast — платформа, заведение — субагент (+4 more)

### Community 7 - "Blast: правила Apple и продукт"
Cohesion: 0.28
Nodes (9): Методы оплаты: кошельки, Visa/Mastercard/AMEX, Apple Pay, Google Pay, TODO(оплата) в activation_screen.dart:139, Риск 2.3.1(a): название «Blast Pay» без оплаты, Правило Apple 3.1.1 (только In-App Purchase), Правило Apple 3.1.3(e) (физические товары и услуги), Blast Pay (мобильное приложение), Подписка «Плюс», StoreKit / In-App Purchase (+1 more)

### Community 8 - "Поле result и бизнес-статус"
Cohesion: 0.47
Nodes (6): HTTP 200 OK, Поле result в теле ответа, result (код бизнес-результата), result = 200 (инвойс создан), result = 400 (данные не найдены / исключение БД), Правило: HTTP 200 не значит успех

## Ambiguous Edges - Review These
- `GET /merchant/health` → `GET /order/status/{order_id}`  [AMBIGUOUS]
  docs-pdf/Введение.txt · relation: conceptually_related_to
- `POST /merchant/invoices` → `TODO(оплата) в activation_screen.dart:139`  [AMBIGUOUS]
  skill/blast-integration.md · relation: calls
- `bill_to (хранилище order_id, agent_id, id_tag)` → `Форма добавления субагента и данные платформы`  [AMBIGUOUS]
  skill/api.md · relation: conceptually_related_to
- `bank_id` → `Графики: распределение по банкам и ежедневные платежи`  [AMBIGUOUS]
  docs-pdf/Dashboard.txt · relation: shares_data_with

## Knowledge Gaps
- **17 isolated node(s):** `Bank API — вне мерчантской интеграции`, `Границы известного: что не задокументировано`, `description`, `lifetime (время жизни инвойса)`, `customer_phone (SMS со ссылкой)` (+12 more)
  These have ≤1 connection - possible missing edges or undocumented components.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **What is the exact relationship between `GET /merchant/health` and `GET /order/status/{order_id}`?**
  _Edge tagged AMBIGUOUS (relation: conceptually_related_to) - confidence is low._
- **What is the exact relationship between `POST /merchant/invoices` and `TODO(оплата) в activation_screen.dart:139`?**
  _Edge tagged AMBIGUOUS (relation: calls) - confidence is low._
- **What is the exact relationship between `bill_to (хранилище order_id, agent_id, id_tag)` and `Форма добавления субагента и данные платформы`?**
  _Edge tagged AMBIGUOUS (relation: conceptually_related_to) - confidence is low._
- **What is the exact relationship between `bank_id` and `Графики: распределение по банкам и ежедневные платежи`?**
  _Edge tagged AMBIGUOUS (relation: shares_data_with) - confidence is low._
- **Why does `POST /merchant/invoices` connect `Инвойс: параметры и статусы` to `Ключи, среды и роли доступа`, `Webhooks и электронная подпись`, `Эндпоинты и флоу интеграции`, `Банки и диплинки`, `Суммы, единицы и HTTP-ошибки`, `Blast: правила Apple и продукт`, `Поле result и бизнес-статус`?**
  _High betweenness centrality (0.342) - this node is a cross-community bridge._
- **Why does `SmartPay — платёжный шлюз для Blast` connect `Ключи, среды и роли доступа` to `Инвойс: параметры и статусы`, `Webhooks и электронная подпись`, `Эндпоинты и флоу интеграции`, `Банки и диплинки`, `Blast: правила Apple и продукт`, `Поле result и бизнес-статус`?**
  _High betweenness centrality (0.181) - this node is a cross-community bridge._
- **Why does `Webhook-уведомление об оплате` connect `Webhooks и электронная подпись` to `Инвойс: параметры и статусы`, `Ключи, среды и роли доступа`, `Эндпоинты и флоу интеграции`, `Банки и диплинки`, `Суммы, единицы и HTTP-ошибки`?**
  _High betweenness centrality (0.129) - this node is a cross-community bridge._