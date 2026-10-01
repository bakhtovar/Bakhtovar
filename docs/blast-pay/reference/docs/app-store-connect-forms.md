# Формы App Store Connect: что отвечать

Три анкеты, которые сейчас не заполнены: Content Rights, Age Ratings и App
Privacy. Ответы ниже — не догадки: они сверены с тем, что приложение
действительно шлёт на сервер и что лежит в базе.

Главное правило: **App Privacy должна совпадать с
`ios/Runner/PrivacyInfo.xcprivacy`**. Apple сверяет их автоматически, и
расхождение — повод для отказа. Ниже они приведены к одному виду.

---

## 1. Content Rights

> Does your app contain, show, or access third-party content?

**Ответ: «Yes, it contains, shows, or accesses third-party content, and I have
the necessary rights».**

Приложение показывает чужое: названия заведений, их адреса, часы работы,
меню с ценами, фотографии, условия акций. Всё это принадлежит партнёрам, и
право показывать это даёт договор с заведением. Плюс отзывы и фотографии,
которые оставляют сами гости.

Ответ «No» здесь был бы неправдой: каталог чужих заведений — это ровно
третья сторона.

---

## 2. Age Ratings

Анкета из семи шагов. Первый — про возможности приложения, остальные про
содержимое.

### Шаг 1. Features

| Пункт | Ответ | Почему |
|---|---|---|
| Parental Controls | **No** | Родительских настроек в приложении нет |
| Age Assurance | **No** | Возраст нигде не проверяется |
| Unrestricted Web Access | **No** | Вебвью в приложении нет вовсе: пакета нет в зависимостях, все ссылки уходят в Safari через `LaunchMode.externalApplication`. Внутри приложения свободно бродить по вебу нельзя |
| User-Generated Content | **Yes** | Отзывы с фотографиями видны всем гостям заведения |
| Social Media | **No** | Ленты нет, подписок нет, чужой контент не расходится по пользователям: отзыв виден на странице своего заведения, и только там |
| Social Media Disabled for Users Under 13 | **No** | Вопрос про смягчение социальных функций, а их нет |
| Messaging and Chat | **No** | Гости между собой не общаются никак |
| Advertising | **No** | Рекламных SDK нет, платных размещений тоже: заведения платят комиссию с визита, а не за место в выдаче |

На «Yes» у User-Generated Content Apple спросит про модерацию. Отвечать
честно: жалоба на отзыв с четырьмя причинами, автоматическое скрытие после
трёх жалоб, удаление аккаунта внутри приложения.

### Остальные шаги — про содержимое

Ставится **12+** из-за одного пункта: в каталоге есть акция «Винный вечер» —
закрытая дегустация с сомелье. Упоминание алкоголя в приложении есть, и
занижать рейтинг нельзя: это отдельная причина для отказа.

Что отвечать в анкете:

| Вопрос | Ответ |
|---|---|
| Alcohol, Tobacco, or Drug Use or References | **Infrequent/Mild** |
| Sexual Content or Nudity | None |
| Profanity or Crude Humor | None |
| Violence — Cartoon, Fantasy, Realistic, Prolonged Graphic | None |
| Horror/Fear Themes | None |
| Mature/Suggestive Themes | None |
| Medical/Treatment Information | None |
| Gambling (simulated или real) | None |
| Contests | None |
| Unrestricted Web Access | **No** — вебвью нет, ссылки уходят в Safari |
| User-Generated Content | **Yes** — отзывы с фото |

---

## 3. App Privacy

### Privacy Policy URL — обязательное поле

Сейчас пусто, и заполнить нечем: `blastpay.tj` не отдаёт сайт. **Это
блокер**: без живой ссылки анкету не опубликовать. Нужна A-запись и три
страницы — см. `docs/TODO.md`.

### Data Types: снять три галочки

Сейчас в анкете стоит восемь типов, три из них приложение не собирает:

| Тип | Что делать | Почему |
|---|---|---|
| **Physical Address** | **Снять** | Адреса гостя нет нигде: в `profiles` только имя, почта и город-район, и город клиент даже не заполняет |
| **Other User Contact Info** | **Снять** | Телефона не спрашиваем — вход по почте, Apple или Google |
| **Search History** | **Снять** | Запрос уходит на сервер, чтобы выполнить поиск, и нигде не сохраняется: в `analytics_events` семь событий, поиска среди них нет |

### Data Types: добавить пять

Их нет в анкете, но приложение их собирает — и они уже объявлены в
манифесте приватности:

| Тип | Где в приложении |
|---|---|
| **Purchase History** | Сумма чека и сэкономленное в «Моих визитах» |
| **Photos or Videos** | Фото к отзыву о визите |
| **Other User Content** | Текст отзыва и теги |
| **Product Interaction** | Своя аналитика: открытие приложения, сканирование QR, попытка и успех активации, ввод суммы, отправка отзыва |
| **Crash Data** | Отчёты о падениях, Firebase Crashlytics — см. `docs/CRASHLYTICS.md` |

### Как отвечать по каждому типу

Ответы одинаковые почти везде: **Linked to the user — Yes**, **Used for
tracking — No**. Трекинга в приложении нет: ни рекламных SDK, ни атрибуции,
ни аналитики Firebase — поэтому и запроса ATT нет.

Исключение одно — **Crash Data**: **Linked — No**. Firebase Crashlytics в
сборке есть, но идентификатор гостя в отчёт не передаётся, и связать падение
с аккаунтом нельзя. Появится `setUserIdentifier` — ответ меняется на Yes,
заодно и в `ios/Runner/PrivacyInfo.xcprivacy`.

| Тип данных | Purpose | Linked | Tracking |
|---|---|---|---|
| Email Address | App Functionality | Yes | No |
| Name | App Functionality | Yes | No |
| Precise Location | App Functionality | Yes | No |
| User ID | App Functionality, Analytics | Yes | No |
| Device ID | App Functionality | Yes | No |
| Purchase History | App Functionality | Yes | No |
| Photos or Videos | App Functionality | Yes | No |
| Other User Content | App Functionality | Yes | No |
| Product Interaction | Analytics | Yes | No |
| Crash Data | App Functionality | No | No |

Пояснения, если Apple спросит:

- **Precise Location** — подтверждение присутствия в заведении при
  активации скидки (150 метров) и расстояние до заведений в каталоге.
- **Device ID** — токен APNs для пуш-уведомлений, хранится привязанным к
  аккаунту и гасится при выходе.
- **Purchase History** — сумму чека вводит сам гость, чтобы приложение
  посчитало экономию. Платёжных реквизитов приложение не видит: оплата
  уходит в приложение банка.
- **Product Interaction** — своя аналитика в своей же базе, наружу ничего
  не уходит.

### Итог: в анкете должно остаться девять типов

Email Address · Name · Precise Location · User ID · Device ID ·
Purchase History · Photos or Videos · Other User Content ·
Product Interaction

Ровно те же девять — в `PrivacyInfo.xcprivacy`. Совпадение обязательно.
