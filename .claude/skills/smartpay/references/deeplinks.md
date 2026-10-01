# White-Label: банковские диплинки

Кнопки банков в вашем интерфейсе. Клиент жмёт «Алиф» — открывается мобильное
приложение Алиф Банка с уже готовым платежом. Промежуточной страницы SmartPay
нет, поэтому и White-Label.

## Схема

```
Frontend ──1 onClick──▶ ваш Backend ──2 POST /invoices──▶ SmartPay
                              ◀──3 deeplink_url────────────┘
Frontend ◀──4 response──┘
Frontend ──5 location.href──▶ приложение банка
ваш Backend ◀──6 webhook после оплаты── SmartPay
```

Ключевое: фронтенд никогда не ходит в SmartPay напрямую. Только через свой
бекенд, потому что там лежит `x-app-token`.

## Шаг 1. Список банков

```bash
curl https://sandbox.smartpay.tj/api/merchant/banks \
  -H "x-app-token: YOUR_API_KEY"
```

```json
{
  "banks": [
    { "id": 8,  "name": "Алиф Банк" },
    { "id": 9,  "name": "Эсхата" },
    { "id": 2,  "name": "Амонатбонк" },
    { "id": 4,  "name": "ICB" },
    { "id": 7,  "name": "CBT/Фаврӣ" },
    { "id": 10, "name": "Арванд" },
    { "id": 13, "name": "ССБ" },
    { "id": 15, "name": "Vasl Pay" }
  ]
}
```

Вызвать один раз и закэшировать. У элемента может быть поле `logo` с путём
относительно `https://smartpay.tj`.

### Что отдаёт живой sandbox

Проверено 8 сентября 2026: тринадцать банков, все создают инвойс с
диплинком. Документация и этот список расходятся — в PDF десять банков,
другие названия и нет MyBabilon, Ориёнбанка и Душанбе Сити. Кнопки рисуй по
ответу `GET /banks`, не по документации.

| ID | Название из API | Схема диплинка |
|---|---|---|
| −10 | MyBabilon | `https://my1.babilon-m.tj/smartpay?...` |
| 2 | ГСБ РТ «Амонатбонк» | `amonatbonk://resolve?view=pay&payID=khs&number=…&sum=…` |
| 3 | ОАО «Ориёнбанк» | `https://oriyonbonk.tj/pay?...` |
| 4 | ЗАО ИКБ Таджикистана (ICB) | `icbmobile://icb.tj/payment?...` |
| 6 | ЗАО «Душанбе Сити Банк» | `https://pay.dc.tj/?...` |
| 7 | ОАО «Коммерцбанк Таджикистана» | `cbt://resolve?view=pay&...` |
| 8 | ОАО «Алиф Банк» | `https://app.alif.tj/providers?...` |
| 9 | ОАО «Банк Эсхата» | `eskhata://service/<uuid>/<номер>/<сумма>` |
| 10 | ЗАО «Банк Арванд» | `arvand://resolve?view=pay&...` |
| 11 | ЗАО «Хумо Банк» | `humoonline://humo.tj/transfers?...` |
| 13 | ГУП ПЭБТ «Саноатсодиротбонк» | `ssb://resolve?view=pay2&...` |
| 15 | ЗАО «Васл Банк» | `https://dl.vasl.tech/?...` |
| 21 | ЗАО МБТ (IBT) | `https://applink.ibt.tj/payment?...` |

Обратите внимание на `ssb://resolve?view=pay2` — у ССБ `pay2`, а у остальных
трёх банков той же формы `pay`. Это не опечатка в документации, так отдаёт
сам шлюз.

Сумма в диплинке — сомони, а не дирамы: `amount=2` и `sum=2` при инвойсе на
два сомони.

### Схемы надо объявлять в манифестах

Семь банков используют собственные схемы, шесть — обычные https-ссылки.
Кастомную схему нужно объявить, иначе система отвечает «такого приложения
нет», даже когда банк установлен:

- iOS — `LSApplicationQueriesSchemes` в `ios/Runner/Info.plist`
- Android — `<queries>` в манифесте: с API 30 видимость чужих пакетов закрыта

Список схем: `eskhata`, `amonatbonk`, `icbmobile`, `cbt`, `arvand`,
`humoonline`, `ssb`. Банки с https-ссылками объявлять не нужно.

### Таблица из документации

| ID | Банк |
|---|---|
| 2 | Амонатбонк |
| 4 | ICB |
| 7 | CBT / Фаврӣ |
| 8 | Алиф Банк |
| 9 | Эсхата |
| 10 | Арванд |
| 11 | Хумо |
| 13 | ССБ |
| 15 | Vasl Pay |
| 21 | IBT |

## Шаг 2. Инвойс с диплинком

Тот же `POST /merchant/invoices`, плюс `deeplink_bank_id`.

| Параметр | Тип | Описание |
|---|---|---|
| `amount` | number | Сумма в сомони: `1500` = 1500 сомони |
| `description` | string | Описание платежа |
| `order_id` | string | ID заказа в вашей системе |
| `deeplink_bank_id` | integer | ID банка из `GET /banks` |

```bash
curl -X POST https://sandbox.smartpay.tj/api/merchant/invoices \
  -H "x-app-token: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 1500,
    "description": "Заказ #123",
    "order_id": "order-123",
    "deeplink_bank_id": 8
  }'
```

Ответ:

```json
{
  "result": 200,
  "message": "Инвойс создан",
  "invoice_uuid": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "smartpay_id": "723000000010",
  "payment_link": "https://smartpay.tj/invoice/723-000000010",
  "deeplink_url": "https://alif.go.link/..."
}
```

Приходят обе ссылки. `deeplink_url` — в приложение банка, `payment_link` —
обычная страница оплаты. Держи `payment_link` как запасной путь.

## Шаг 3. Переход

Веб-фронтенд: `window.location.href = deeplink_url`, и через 2.5 секунды
сообщение «приложение банка не найдено», если ничего не открылось.

Во Flutter это `url_launcher` с `LaunchMode.externalApplication`. Проверяй
результат `launchUrl` и падай обратно на `payment_link`.

## Ограничения

- **API-ключ только на бекенде.** `x-app-token` во фронтенде недопустим.
- **Диплинки только на мобильных.** На десктопе не сработают — там
  `payment_link` и QR.
- **`amount` в сомони.** `1500` = 1500 сомони. Альтернатива — `price`, целое
  число в дирамах.
- **Статус оплаты — webhook.** Возврат в приложение не означает, что деньги
  прошли.
