---
name: blast-merchant-panel-deploy
description: "Кабинет заведения живёт на blastpay.tj/admin и выкладывается вместе с сайтом через wrangler, а не с Netlify"
metadata: 
  node_type: memory
  type: project
  originSessionId: 1f6302e1-6fdf-47b9-a8b5-7136e1807246
  modified: 2026-09-27T17:27:47.061Z
---

Панель заведения (репозиторий `bakhtovar/Blast-Pay-Admin-Panel`, ветка
`main`) больше не живёт на `blastpayadmin.netlify.app`. Она
собирается Vite с `base: "/admin/"` и кладётся в `site/admin/` проекта
`/Users/bakhtovarumarov/blast docs`, который целиком выкатывает
Cloudflare Worker `blastpay`:

```
npx vite build                      # в рабочей копии панели
rm -rf site/admin && cp -R dist/. site/admin/   # в «blast docs»
npx wrangler deploy
```

**Why:** `blastpay` — это worker с assets, отдельного деплоя у панели нет.
Без `base: "/admin/"` страница открывается белой: файлы ищутся в корне
домена. Апекс `blastpay.tj` уводит на `www` с двойным слэшем, но
`/admin` переживает это сам — отдельного правила, как для `/q/*`, не
нужно.

**How to apply:** рабочая копия панели теперь
`/Users/bakhtovarumarov/Desktop/blast pay/Blast-Pay-Admin-Panel` (старая
`/tmp/bpadmin` была временной). `npx wrangler deploy` запускать без
переменных: работает OAuth-вход wrangler. Токен из `blast docs/.env.cloudflare`
(голая строка без `KEY=`, никогда не выводить — даже с маской через sed)
с 2026-09-27 отвечает Authentication error 10000. Проверять выкладку через
`https://www.blastpay.tj/admin/` — апекс отвечает 301, и curl без `-L`
ничего не найдёт.
После выката страница у уже открытой вкладки остаётся старой до
перезагрузки: `_headers` держит `/admin/index.html` с
`max-age=0, must-revalidate`, но вкладку всё равно нужно перезагрузить.
Данные панель берёт серверными функциями `merchant_*` — см.
[[blast-supabase-projects]].
