# Webhooks — единственный способ узнать об оплате

Настраиваются в личном кабинете: Танзимот (Settings) → Webhooks. Два типа:
HTTPS и Telegram.

Опроса статуса нет. Если webhook не настроен, приложение никогда не узнает,
что счёт оплачен.

## Создание HTTPS webhook

1. Вкладка «Webhooks» → «Add Webhook» → «HTTPS Webhook».
2. Заполнить Target URL, Description, Headers (JSON).
3. «Create HTTPS Webhook».

Поля формы:

| Поле | Назначение |
|---|---|
| Target URL | URL вашего сервера, например `https://yourstore.com/webhook` |
| Description | Описание назначения |
| Headers (JSON) | HTTP-заголовки, которые SmartPay добавит к запросу |

При создании автоматически генерируется 64-символьный hex-токен в заголовке
`X-Api-Token`. Проверяйте его на своей стороне: это и есть аутентификация
входящего запроса.

```json
{
  "Content-Type": "application/json",
  "X-Api-Token": "64-символьный hex токен (генерируется автоматически)"
}
```

## Формат уведомления

POST с JSON-телом при успешной оплате:

```json
{
  "payment_date": "2026-01-15T14:30:00",
  "amount": 1500000,
  "order_id": "ORD-123456",
  "bank_id": 1,
  "invoice_number": "001-000042",
  "invoice_uuid": "a1b2c3d4-e5f6-...",
  "payment_id": 78901,
  "bank": "Амонатбонк"
}
```

| Поле | Описание |
|---|---|
| `payment_date` | Дата и время оплаты, ISO 8601 |
| `amount` | **Сумма в дирамах, integer.** 1 сомони = 100 дирам |
| `order_id` | ID заказа, из поля `bill_to` инвойса |
| `bank_id` | ID банка |
| `invoice_number` | Номер инвойса, формат `XXX-XXXXXX` |
| `invoice_uuid` | UUID инвойса |
| `payment_id` | ID платежа. В примере с подписью приходит как UUID, в базовом примере как число — не завязывайся на тип |
| `bank` | Название банка |

## Ответ и ретраи

Сервер обязан ответить `200 OK`. Если ответа 200 нет, SmartPay повторяет
доставку с нарастающим интервалом **в течение 2 суток**, пока не получит
подтверждение.

Отсюда требование идемпотентности: одно и то же событие придёт несколько раз.
Уникальные ключи для дедупликации — `payment_id` и `invoice_uuid`.

## Электронная подпись HMAC-SHA256

Включается при создании webhook. SmartPay генерирует секретный ключ, каждый
запрос получает поле `signature` в теле.

```json
{
  "payment_date": "2026-01-15T14:30:00",
  "amount": 1500000,
  "order_id": "ORD-123456",
  "bank_id": 1,
  "invoice_number": "001-000042",
  "invoice_uuid": "a1b2c3d4-e5f6-...",
  "payment_id": "uuid-...",
  "bank": "Амонатбонк",
  "signature": "a3f2b8c1d4e5...64-символьный hex хеш"
}
```

Строка для подписи — поля через `|`:

```
payment_date|amount|order_id|bank_id|invoice_uuid
```

**Ловушка формата даты.** В подписи `payment_date` форматируется как
`YYYY-MM-DD HH:MM:SS` — пробел вместо `T`, без микросекунд. В payload при этом
приходит `2026-03-05T03:31:51.879779`, а в строку подписи идёт
`2026-03-05 03:31:51`. Остальные значения берутся из payload как есть,
`amount` — в дирамах.

Секретный ключ показывается один раз при создании. Потерян — удалить webhook и
создать новый.

Проверка на TypeScript / Deno (для Supabase Edge Function):

```ts
async function verifySignature(payload: Record<string, unknown>, secret: string) {
  const received = String(payload.signature ?? "");
  const raw = String(payload.payment_date ?? "");
  // "2026-03-05T03:31:51.879779" -> "2026-03-05 03:31:51"
  const dateForSign = raw.replace("T", " ").split(".")[0];
  const signString = [
    dateForSign,
    payload.amount ?? "",
    payload.order_id ?? "",
    payload.bank_id ?? "",
    payload.invoice_uuid ?? "",
  ].join("|");

  const key = await crypto.subtle.importKey(
    "raw",
    new TextEncoder().encode(secret),
    { name: "HMAC", hash: "SHA-256" },
    false,
    ["sign"],
  );
  const mac = await crypto.subtle.sign("HMAC", key, new TextEncoder().encode(signString));
  const expected = [...new Uint8Array(mac)]
    .map((b) => b.toString(16).padStart(2, "0"))
    .join("");

  // сравнение постоянного времени
  if (received.length !== expected.length) return false;
  let diff = 0;
  for (let i = 0; i < expected.length; i++) diff |= received.charCodeAt(i) ^ expected.charCodeAt(i);
  return diff === 0;
}
```

## Telegram-уведомления

Уведомления о платежах в Telegram-чат.

1. Создать групповой чат (лучше отдельный, только под платежи).
2. Добавить бота `@smartpay_ecomm_bot`, дать право писать сообщения.
3. Получить Chat ID и передать его менеджеру SmartPay — сам мерчант это не
   включает.

Годится как канал наблюдения для команды, не как источник истины для системы.

## Расхождение в документации

На странице «Веб-хуки» таблица параметров уведомления другая: там `payment_id`
описан как UUID, `amount` как Decimal без указания дирам, а в примере заголовков
фигурирует `api_token` вместо `X-Api-Token`. Страница «Настройка Webhooks»
новее и подробнее — ориентируйся на неё, но при первом боевом webhook
залогируй сырое тело целиком и сверься с фактом.
