# Доступы Blast — где что лежит и как получить на новом компьютере

В архиве **нет ни одного ключа и пароля** — только карта. Файлы с ключами
переносятся руками (AirDrop или флешка, не через чат и не через облако), а
входы в сервисы делаются заново.

## 1. Файлы с ключами — перенести руками

Их нет в git, поэтому `git clone` их не привезёт. Положить по тем же путям.

| Файл | Что внутри | Без него |
|---|---|---|
| `~/Desktop/blast pay/blast n1/blast/.env.local` | `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `GOOGLE_IOS_CLIENT_ID`, `GOOGLE_WEB_CLIENT_ID` | приложение — белый экран, `make run` падает |
| `~/Desktop/blast pay/blast n1/blast/.env` | ключи для других целей Makefile | часть команд не работает |
| `~/Desktop/blast pay/blast n1/blast/.env.smartpay` | тестовый ключ SmartPay и `x-diag-token` | нельзя проверить шлюз |
| `~/Desktop/blast pay/blast n1/blast/android/key.properties` + файл ключа `.jks`, на который он указывает | подпись Android-сборок (CN=Bakhtovar Umarov) | **нельзя выпустить обновление в Google Play**. Хранить копию ещё где-то |
| `.env` в `Blast-Pay-Admin-Panel`, `Blast-Internal-Admin`, `Blast-Pay-Landing` | `VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY` | панель открывается с «Supabase не настроен» |
| `~/blast docs/.env.cloudflare` | старый DNS-токен Cloudflare | не нужен: с 27.09 не работает, деплой идёт через вход wrangler |

Публичные ключи Supabase (`URL` и `anon`) можно заново взять в Supabase →
Project Settings → API (проект `lfudcjwkyctodeqaommv`).

## 2. Входы — сделать заново на новом компьютере

| Сервис | Аккаунт / где | Как войти |
|---|---|---|
| GitHub | `bakhtovar` (все репозитории приватные) | при первом `git clone` (логин + токен) или `brew install gh && gh auth login` |
| Supabase CLI | твой аккаунт Supabase | `supabase login`, затем в папке приложения `supabase link --project-ref lfudcjwkyctodeqaommv` |
| Cloudflare (деплой сайта) | `bakhtovar.github@gmail.com` | `cd ~/blast\ docs && npx wrangler login` |
| Apple (подпись iOS) | Team ID `LL8FZUAY98`, аккаунт Individual | Xcode → Settings → Accounts → добавить Apple ID; сертификаты Xcode скачает сам |
| App Store Connect, Transporter | тот же Apple ID | в браузере / в Transporter |
| Google Play Console | твой Google-аккаунт разработчика | в браузере |
| SmartPay | dashboard.smartpay.tj | в браузере. Боевой ключ лежит в секретах Supabase. **Его надо сменить** |
| Почта support@blastpay.tj | Zoho Mail, европейский дата-центр | mail.zoho.eu или приложение Zoho Mail |
| Домен blastpay.tj | регистратор navjucloud.tj, DNS в Cloudflare | менять ничего не нужно |

## 3. Что живёт в облаке и переезжать не должно

- **Секреты Edge Functions** в Supabase: `SMARTPAY_*`, `SMARTPAY_WEBHOOK_SECRET`,
  `APNS_*` (ключ `.p8`, `APNS_TOPIC=com.blastpay.app`), `CRON_SECRET`.
  Меняются через `supabase secrets set … --project-ref lfudcjwkyctodeqaommv`.
- **Telegram-бот** листа ожидания: ключи в таблице `secure_config` в базе.
- **Подписи сайтов** (AASA, assetlinks) лежат в `~/blast docs/site/.well-known` и
  приезжают с git.

## 4. Подключения Claude

- **Коннекторы claude.ai** (Settings → Connectors): Supabase и Cloudflare. Войти под
  своими аккаунтами.
- **Claude в Chrome** — расширение, если нужно работать в твоём браузере.
- **Плагины Claude Code** (набрать в Claude Code):
  ```
  /plugin marketplace add remotion-dev/claude-code-plugin
  /plugin install remotion@remotion
  /plugin marketplace add cloudflare/skills
  /plugin install cloudflare@cloudflare
  ```
- Права и разрешения (`settings.json`) не переносятся: на новом месте Claude
  спросит заново, разреши то, что нужно.
