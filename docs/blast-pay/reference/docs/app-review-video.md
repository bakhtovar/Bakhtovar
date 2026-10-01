# Демо-видео для App Review

3 сентября Apple отклонила сборку по **Guideline 2.1 — Information Needed**.
Отказ не про приложение: они не смогли начать проверку и просят видео, где
приложение работает **на настоящем iPhone, а не в симуляторе**, и ссылку на
это видео в Review Notes.

Отклонена сборка **iOS 0.1.0 (1)** от 1 сентября — та, что была до всей
работы последних двух недель. Отправлять видео по ней нет смысла: ревьюер
увидит одно, а откроет другое.

---

## Порядок

1. Собрать и загрузить свежую сборку (`make ipa` → Transporter).
2. Завести демо-аккаунт — `docs/app-review-account.md`, шаги 2–5. **Без
   пятого шага активация на видео не пройдёт**: миграция
   `20260911120000_dev_tester_skips_presence.sql` снимает проверку
   присутствия, а без неё приложение честно скажет «подойдите ближе».
3. Поставить эту сборку на свой iPhone — из Xcode или из TestFlight, когда
   она там появится.
4. Записать видео по сценарию ниже.
5. Выложить и получить ссылку, открывающуюся без входа.
6. Вписать ссылку в Review Notes и ответить на сообщение в App Store Connect.

## Чем снимать

Apple написала прямо: **physical device, not a simulator**. Надёжнее всего
снять телефон второй камерой — тогда вопрос «а не симулятор ли это» не
возникает вовсе. Экранной записи с самого iPhone (Пункт управления →
запись экрана) тоже достаточно, но тогда первые пять секунд покажите
телефон в руке: Настройки → Основные → Об этом устройстве, где видно модель
и версию iOS, и уже потом запускайте приложение.

Без музыки и монтажа. Две-четыре минуты. Говорить не обязательно — если
говорите, то по-английски.

## Что показать

| # | Кадр | Зачем |
|---|---|---|
| 1 | Настройки → Об этом устройстве: модель iPhone, версия iOS | Доказательство, что это не симулятор |
| 2 | Значок Blast Pay на домашнем экране, запуск | Приложение установлено на этом телефоне |
| 3 | Экран входа → **«Sign in with a password»** → почта и пароль демо-аккаунта → вход | Ровно тот путь, который описан в Review Notes |
| 4 | Главная: категории, «Сейчас работает», «Рядом с вами» | Каталог живой, данные настоящие |
| 5 | Открыть заведение: часы, адрес, меню, список акций | Экран заведения целиком |
| 6 | Открыть акцию: условия, лимиты, окно действия | Правила показаны до активации |
| 7 | Слайдер активации → экран подтверждения | **Главная функция.** Работает благодаря демо-доступу |
| 8 | Оплата: сумма, выбор позиции, выбор банка | Дальше банк Таджикистана — сказать, что за пределами страны он не откроется |
| 9 | «Мои визиты», оценка визита | Что происходит после |
| 10 | Профиль → Settings: язык (три), тема, **Delete account** | Удаление аккаунта внутри приложения — требование 5.1.1(v) |

Восьмой пункт довести до экрана выбора банка и остановиться. Оплату не
завершать: приложения таджикских банков вне страны недоступны, и это
объяснено в тексте ответа.

## Где выложить

Ссылка должна открываться **без входа и без запроса доступа**. Проверьте её
в приватном окне браузера перед отправкой.

- YouTube, доступ «по ссылке» (unlisted) — самый привычный ревьюеру вариант.
- Google Drive: «Доступ по ссылке — все, у кого есть ссылка». Проверить
  обязательно: по умолчанию Drive ставит доступ только по приглашению.
- Dropbox или iCloud с публичной ссылкой.

Срок жизни ссылки — минимум до конца проверки, лучше месяц.

## Ответ в App Store Connect

Вставить в Review Notes рядом с логином и паролем, и тем же текстом
ответить на сообщение ревью.

```
Hello,

Thank you for the review. A demo video recorded on a physical iPhone is here:

<ссылка>

The video shows the device and iOS version first, then the full flow: signing
in with the demo account, browsing the catalog and the map, opening a venue
and an offer, activating a discount, the payment hand-off, visit history and
the settings, including in-app account deletion.

Demo account (also in Beta App Review Information):

User Name: review@blastpay.tj
Password:  <пароль>

On the first screen, tap "Sign in with a password" under the email button and
enter the credentials above. This account skips our early-access waitlist and
opens every screen of the app.

Two notes so nothing looks broken:

1. Redeeming a discount normally requires the guest to be physically inside a
   partner venue in Dushanbe — the app scans a QR sticker on the table and
   checks GPS within 150 metres. This is anti-fraud design: the discount is
   applied by restaurant staff at the table. Both checks are lifted for the
   demo account, so the activation in the video and on your device works from
   anywhere.

2. Payment hands off to a Tajik bank application (Alif, Eskhata, Amonatbonk)
   through the SmartPay gateway. Those apps are not available outside
   Tajikistan, so the flow ends at the bank selection screen. No payment is
   charged, and the discount is applied by the venue regardless.

The build in this submission is newer than the one you reviewed on September 3
and matches the video.

Thank you,
Blast Pay team
```

## Перед отправкой — проверить

- [ ] Сборка загружена и обработана в App Store Connect
- [ ] Демо-аккаунт заведён, `has_early_access` возвращает true
- [ ] Миграция про присутствие применена, активация проходит с телефона
- [ ] Видео снято на живом устройстве, слышно и видно, что это iPhone
- [ ] Ссылка открывается в приватном окне без входа
- [ ] Логин и пароль вписаны в Beta App Review Information, Sign-in required включён
- [ ] Ответ отправлен ответом на сообщение ревью, а не новым обращением
