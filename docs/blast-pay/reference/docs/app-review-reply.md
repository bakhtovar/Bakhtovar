# Ответ Apple Beta App Review

Готовый текст для поля Notes лежит отдельным файлом, чтобы копировать без
разметки: `docs/app-review-notes.txt` (2841 знак из 4000).

Ниже — та же мысль с пояснениями по-русски.

Перед отправкой:
1. Завести демо-аккаунт — `docs/app-review-account.md`.
2. Снять демо-видео на живом iPhone — `docs/app-review-video.md`.
   3 сентября сборку отклонили по Guideline 2.1 именно из-за его отсутствия.

---

Hello,

Thank you for the review. A demo account is set up for you:

**Demo video (physical iPhone):** *(ссылка — см. app-review-video.md)*

**User Name:** `review@blastpay.tj`
**Password:** *(из App Store Connect — сюда не выписываем)*

Open the app, tap **"Войти по паролю" / "Sign in with a password"** under the
email button on the first screen, and enter the credentials above. The account
skips our early-access waitlist and opens every screen of the app.

Sign in with Apple and Google work too, but they create a fresh account that
lands in the waitlist, so please use the demo credentials.

**What the account shows.** Five partner venues in Dushanbe, Tajikistan with
about forty live offers: the catalog, the map with venue pins, venue pages with
opening hours and terms, offer details with limits and time windows, coupon
booking, visit history, the profile and settings. We are still onboarding
venues, so these numbers grow week to week.

**Redeeming a discount.** Normally the guest must be physically inside the
partner venue: the app scans a QR sticker on the table and checks GPS within a
150-metre radius of that venue in Dushanbe. This is deliberate anti-fraud
design — the discount is applied by restaurant staff at the table, not in the
app. **Both checks are lifted for the demo account**, so you can open any
offer, swipe the slider and complete an activation from anywhere in the world.

**Payment.** After activation the app opens the payment step, which hands off
to a Tajik bank application (Alif, Eskhata, Amonatbonk and others) through the
SmartPay gateway. Those apps are not available outside Tajikistan, so the
hand-off screen is as far as the flow goes for you. No payment is charged, and
the discount itself is applied by the venue regardless of how the guest pays.

**Language.** The app follows the device language and ships with English,
Russian and Tajik. If it opens in Russian, English is under
Profile → Settings → Language.

**Account deletion** is in the app: Profile → Settings → Delete account. It
removes the account and all guest data.

Thank you,
Blast Pay team

---

## Перед отправкой

Очередь раннего доступа **снимать больше не нужно**: демо-аккаунт проходит
мимо неё через `dev_testers`. Остальные новые аккаунты по-прежнему встают в
очередь — так и задумано.

Что сделать по шагам — `docs/app-review-account.md`. Коротко:

1. Завести пользователя с паролем (Supabase → Authentication → Add user,
   Auto Confirm включить).
2. Проставить ему `display_name` в метаданных — чтобы не спрашивать имя.
3. Добавить в `dev_testers` со сроком на время ревью.
4. Выполнить миграцию `20260911120000_dev_tester_skips_presence.sql` —
   без неё активация из Купертино не пройдёт.
5. Проверить вход с чистого устройства и вписать почту с паролем в
   Beta App Review Information.
