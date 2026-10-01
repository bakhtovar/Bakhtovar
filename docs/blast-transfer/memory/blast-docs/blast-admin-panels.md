---
name: blast-admin-panels
description: "Две админки Blast — кабинет заведения и внутренняя teamcave: где исходники, как собираются и выкатываются"
metadata: 
  node_type: memory
  type: project
  originSessionId: b88d84a6-40a4-4c70-876b-b960c1249e26
  modified: 2026-10-01T11:32:55.015Z
---

У Blast две веб-панели, обе на blastpay.tj и обе ходят в App Dev
(`lfudcjwkyctodeqaommv`):

- **Кабинет заведения** — `blastpay.tj/admin`. Исходники: GitHub
  `bakhtovar/Blast-Pay-Admin-Panel`, локально `~/Desktop/blast pay/Blast-Pay-Admin-Panel`.
  `base: "/admin/"` в vite.config.
- **Внутренняя админка** — `blastpay.tj/teamcave` (с 22.09.2026). Исходники: `bakhtovar/Blast-Internal-Admin`,
  локально `~/Documents/blast waiting list/Blast-Internal-Admin`,
  `base: "/teamcave/"`.

**Выкатка одинаковая:** `npm run build` в репозитории панели → содержимое
`dist/` скопировать в `~/blast docs/site/<admin|teamcave>/` → из `~/blast docs`
`npx wrangler deploy`.

**Ловушки.**
- В `~/blast docs` лежит только **собранный бандл** панелей, исходников там нет.
  По коду в `site/admin/assets/*.js` репозиторий не ищется — искать по имени на GitHub.
- `~/Documents/blast pitch deck/Blast-Merchant-Dashboard-fixed` — **старый прототип**
  другой панели: смотрит в `ddbkjhnbqdmdwqvivzhu` и зовёт несуществующие RPC
  (`create_merchant_account`, `merchant_dashboard_summary`). Это не кабинет заведения.
- `.env` в репозиториях панелей в git не входит. Без него сборка проходит, но
  панель открывается с «Supabase не настроен»: после свежего клона его надо
  создать (`VITE_SUPABASE_URL` + `VITE_SUPABASE_ANON_KEY`).
- У внутренней админки есть стенд без входа: `vite --config vite.preview.config.js`
  с заглушками в `preview/`. Запускать надо **из каталога репозитория** — tailwind
  ищет content-шаблоны относительно текущей директории, иначе страница будет
  без стилей. Конфиг для этого лежит в `~/blast docs/.claude/launch.json`
  под именем `teamcave-preview`.

**Состояние на 23.09.2026.** Обе панели выкачены с `main`, все три репозитория
на GitHub (раньше сайт `Blast-Pay-Site` лежал только на диске с одним старым
коммитом). Тестовое заведение `blast-test` — draft, привязано к обоим личным
адресам Бахтовара; в приложении он ходит с `bakhtovar.umarov02@gmail.com`
(там выдан доступ и устройства), а не с `iambakhtovar@gmail.com`. Заведение
вне каталога видно только своему персоналу — это правило добавлено в
`search_venues`, `catalog_home_offers`, `catalog_map_venues`, `is_offer_live`,
`venue_offers_here` и `venue_detail` через `public.venue_visible()`.

**Акции без проверки (с 01.10.2026, решение Бахтовара).** Заведение само
публикует акцию кнопкой «Опубликовать» и правит условия живой без снятия с
витрины (миграция `20261001120000_offers_published_by_venue`). Статус
`moderation` и одобрение в teamcave остались только для старых записей — не
возвращать проверку в кабинет без его слова.

**Сканы меню** лежат в закрытом бакете `venue-menus` (в комментарии к таблице
`venue_menu_pages` ошибочно написан `venue-photos`). Прямых ссылок
`/object/public/` у них нет — нужны подписанные, панель делает это через
`menuService.signMenuPages`.

**Третий фронтенд — страница раннего доступа.** `blastpay.tj/waiting`. Исходники:
`bakhtovar/Blast-Pay-Landing`, локально `~/Documents/blast waiting list/Blast-Pay-Landing`.
Собирается **с флагом**: `npx vite build --base=/waiting/` (в `vite.config.js` base не
задан — остаток времён, когда эта сборка жила в корне другого хостинга).

**Ловушка base.** Этот репозиторий живёт на двух адресах сразу, и пути к файлам,
записанные от корня (`/img/…`, `/blast-logo.svg`), на blastpay.tj молча уходят
в 404 — Vite переписывает только index.html и импортированные ассеты, но не
строки в JSX и JSON. Так фон и логотип на `/waiting` были битыми с самого
переезда. Правильно — `import.meta.env.BASE_URL` (хелперы `asset`/`assetSet`
в `WaitlistPage.jsx`).

**Netlify не используется** (сказано 29.09.2026). Всё живёт только на
blastpay.tj через Cloudflare Workers; старые адреса *.netlify.app не
упоминать и на них не ссылаться.

**Why:** исходники панелей лежат в трёх разных местах вне рабочей папки, и
один из каталогов — похожий по названию мёртвый прототип.

**How to apply:** правки панелей вести в их репозиториях ветками и PR
([[blast-git-workflow]]), деплой — только через `~/blast docs`
([[blast-site-infra]]). Схема и RPC — [[blast-supabase-projects]].
