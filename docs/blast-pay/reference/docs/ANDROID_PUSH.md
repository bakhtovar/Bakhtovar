# Android push notifications

На Android удалённые пуши идут только через FCM. Постоянное соединение с
серверами Google держит сервис Google Play, и другого пути нет: своё фоновое
соединение система усыпит. Сторонние сервисы (OneSignal, Pushy, Expo) —
обёртки над тем же FCM.

Firebase здесь используется ровно для одного: получить токен устройства и
отдать сообщение в FCM. Очередь, тексты, настройки, тихие часы и суточный
лимит остаются в Postgres — так же, как для APNs, см. `APPLE_PUSH.md`.

## Что заводится в Firebase

1. Создать проект (можно пустой, без Analytics) на том же Google-аккаунте.
2. Добавить Android-приложение с пакетом `com.blastpay`.
3. `google-services.json` скачивать **не нужно**: параметры приходят через
   `--dart-define`, как и остальные ключи. Взять их можно на том же экране —
   Project settings → General → Your apps → Android:

   | dart-define | откуда |
   |---|---|
   | `FIREBASE_API_KEY` | Web API Key |
   | `FIREBASE_APP_ID` | App ID (вида `1:123…:android:abc…`) |
   | `FIREBASE_SENDER_ID` | Project number |
   | `FIREBASE_PROJECT_ID` | Project ID |

4. Service account для отправки: Project settings → Service accounts →
   Generate new private key. Скачанный JSON целиком уходит в секрет
   `FIREBASE_SERVICE_ACCOUNT`.

Отпечатки SHA-1 и SHA-256 здесь не нужны — они нужны Google-входу, это
отдельная история (`google-play-readiness.md`).

## Секреты Edge Function

В проекте **Blast App Dev** добавить:

- `FIREBASE_SERVICE_ACCOUNT` — содержимое JSON сервисного аккаунта целиком
- `FIREBASE_PROJECT_ID` — тот же Project ID

`CRON_SECRET` и ключи APNs остаются как были. После добавления секретов
передеплоить `send-push`.

Каналы независимы: пока настроен только APNs, функция работает для iOS, а
строки для Android помечает пропущенными с причиной
`no configured channel for android`. Обратное тоже верно — отсутствие
ключей Firebase рассылку на iOS не останавливает.

## Что уже сделано в коде

- `lib/features/notifications/data/android_push_service.dart` — получение
  токена, разрешение, маршрут из открытого уведомления.
- `lib/features/notifications/data/push_service.dart` — общий интерфейс:
  экраны не знают, APNs под ними или FCM.
- `lib/core/firebase_bootstrap.dart` — поднимает Firebase только на Android
  и только когда заданы все четыре значения. Пустые — приложение работает
  как прежде, с локальными напоминаниями.
- В `send-push` добавлена ветка FCM HTTP v1. Legacy server key Google
  отключил, поэтому там OAuth2: JWT подписывается ключом сервисного
  аккаунта и меняется на access token.

## Проверка

Подготовить сборку с ключами и поставить на устройство:

```bash
make aab   # или flutter run с теми же --dart-define
```

Токен должен появиться в базе:

```sql
select platform, locale, app_build, created_at
from push_tokens where is_active and platform = 'android';
```

Отправить тестовое уведомление себе:

```sql
insert into notification_outbox (user_id, kind, payload, state)
values (auth.uid(), 'test_push', '{}'::jsonb, 'pending');
```

Cron `dispatch-push` заберёт его в течение минуты. Если не пришло:

```sql
select state, attempts, last_error from notification_outbox
order by created_at desc limit 5;
```

## Грабли

- **`channel_id`.** Сервер шлёт `blast_offers` — тот же канал, что у
  локальных напоминаний. Канал раньше заводился лениво, в момент показа
  первого напоминания, и на свежей установке его не существовало: пуш с
  незнакомым каналом Android выбрасывает молча, при том что и база, и FCM
  отчитываются об успешной доставке. Теперь `Reminders.ensureChannel()`
  создаёт его при настройке пушей, до первого сообщения.

  Проверить, что канал на месте:

  ```bash
  adb shell dumpsys notification --noredact | grep blast_offers
  ```

  Сервис называется `notification`, а не `notification_manager` — второго
  на устройстве нет, и запрос к нему молча возвращает пустоту, что легко
  принять за отсутствующий канал.
- **Телефоны без сервисов Google** (Huawei после 2019 года). FCM там не
  работает вовсе: `getToken` вернёт null, регистрации не будет, приложение
  продолжит работать с локальными напоминаниями. Нужен отдельный Huawei
  Push Kit, и для Душанбе это не абстракция.
- **`POST_NOTIFICATIONS`.** На Android 13+ без него не покажется ни одно
  уведомление, включая локальные. Объявлено в манифесте явно.
