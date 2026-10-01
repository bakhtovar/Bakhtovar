---
name: blast-site-infra
description: "Где живёт сайт blastpay.tj, чем деплоится и где почта — Cloudflare Workers, Zoho EU, регистратор navjucloud"
metadata:
  node_type: memory
  type: project
  originSessionId: e03a1e9f-971f-419b-9255-1a10d15908a4
  modified: 2026-09-29T12:56:16.065Z
---

**Сайт.** `blastpay.tj` — статика на Cloudflare Workers (проект `blastpay`,
аккаунт `bakhtovar.github@gmail.com`). Исходники и всё, что уходит на хостинг:
`~/blast docs/site/`. Деплой из `~/blast docs`:

```
npx wrangler deploy
```

Конфиг `wrangler.jsonc`: `assets.directory = site`, `html_handling =
auto-trailing-slash` (поэтому `/privacy` отдаётся из `privacy.html`),
`workers_dev: true` и два custom domain — apex и www. **Без `workers_dev: true`
запасной адрес `blastpay.blast-docs.workers.dev` отключается**: wrangler гасит
его, как только в конфиге появляются `routes`.

`_redirects` и `_headers` — в формате Netlify, Workers читает их как есть;
`netlify.toml` остался для запасного варианта и игнорируется. Внутренние файлы
(`README.md`, `netlify.toml`) исключены из публикации через `.assetsignore`.

**Лендинг** — `site/index.html`, 11 МБ самораспаковывающийся бандл: шаблон лежит
JSON-строкой внутри `<script type="__bundler/template">`, ресурсы — base64 рядом.
Править его руками нельзя, только скриптом `~/blast docs/patch-landing.py`
(распаковать → заменить → упаковать, экранируя `</` как `</`).

**Страницы заведений** (с 29.09.2026) — `site/venue/<slug>.html`, ссылки вида
`blastpay.tj/venue/mazza`. Их пишет `python3 tools/venue_pages.py` из публичного
каталога (только active); он же ведёт старые `/venue/<uuid>` на slug через
размеченный блок в `_redirects`. **Новое заведение или смена slug — перезапустить
скрипт и выкатить**, иначе его ссылка отдаёт 404. Приложение принимает в
`/venue/:id` и uuid, и slug (`VenueLinkScreen`).

**DNS.** Зона в Cloudflare, активна с 11 сентября 2026. Регистратор —
navjucloud.tj; смена NS там идёт через государственного регулятора и заняла
двое суток. Токен для API лежит в `~/blast docs/.env.cloudflare` (голый токен,
без имени переменной), права только `Edit zone DNS` — настройки зоны и
redirect rules им недоступны, там 403.

**Почта.** Zoho Mail, бесплатный план, **европейский дата-центр**: `mailadmin.zoho.eu`,
MX на `mx.zoho.eu` / `mx2` / `mx3`, SPF `include:zohomail.eu`, DKIM в
`zmail._domainkey`. Основной ящик — `support@blastpay.tj`. На free-плане
**нет IMAP, POP и пересылки** — почту можно читать только в вебе и приложении
Zoho; Gmail забрать её не может (Google убрал POP-импорт в январе 2026).

**Why:** всё это снаружи не видно, а восстанавливать по крупицам долго — на одну
только смену NS ушло два дня ожидания.

**How to apply:** перед правкой сайта смотреть `site/README.md`; домены и почту
трогать через API токеном из `.env.cloudflare`. См. [[blast-legal-entity]].
