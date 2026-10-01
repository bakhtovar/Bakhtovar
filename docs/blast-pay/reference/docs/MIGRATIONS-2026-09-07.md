# Миграции 7 сентября 2026

Ветка `feature/kaffeine-zaytun-real-data`. Порядок важен: каждая следующая
опирается на предыдущую.

| Файл | Что делает |
|---|---|
| `20260907100000_promotion_kinds_enum.sql` | `offer_kind` += `buy_x_get_y`, `free_item`, `cashback`, `combo`. Отдельной миграцией: Postgres не даёт использовать значение enum в той же транзакции, где оно создано |
| `20260907100100_merchant_promotion_model.sql` | колонки `venues` (`merchant_type`, `email`, `logo_path`, `google_place_id`, `google_maps_url`) и `offers` (`description`, `buy_quantity`, `get_quantity`, `reward_item_id`, `cashback_percent`, `daily_limit`, `total_limit`, `cooldown_hours`); view `merchants`, `menu_categories`, `menu_items`, `promotions`, `promotion_redemptions` |
| `20260907100200_offers_kind_constraint.sql` | снимает `offers_kind_consistent`, перечислявшее типы поимённо и не пускавшее ни один новый; его смысл переезжает в `offers_kind_shape` |
| `20260907101000_promotion_limits.sql` | `check_promotion_limits(offer, user, at)` |
| `20260907101100_activate_offer_limits.sql` | вызов проверки внутри `activate_offer` |
| `20260907102000_merchant_dashboard.sql` | `venue_menu_items.is_available`, `updated_at` на обеих таблицах меню, десять функций `merchant_*` |
| `20260907103000_admin_merchants.sql` | `admin_create_merchant`, `admin_update_merchant`, `admin_set_merchant_member`, `admin_review_promotion` |
| `20260907104000_rls_merchant_scope.sql` | RLS: меню перестаёт быть общедоступным, мерчант получает право писать; `venue_menu` фильтрует скрытые позиции |
| `20260907104100_rls_menu_anon_split.sql` | политики чтения меню разделены на `anon` и `authenticated` — единая падала на `permission denied for function is_member` |
| `20260907105000_archive_demo_venues.sql` | пятнадцать выдуманных заведений в архив, их акции следом, живые коды погашены |
| `20260907106000_onboard_kaffeine_zaytun.sql` | KAFFEINE и ZAYTUN, часы работы, QR-наклейки |
| `20260907107000_starter_promotions_draft.sql` | семь черновиков, по одному на тип. Цифры подобраны мной — заменены следующей миграцией |
| `20260907108000_catalog_promotions_draft.sql` | `offers.catalog_code`; удаляет придуманные черновики и заводит десять акций из «Программы акций v1.0» заказчика с их окнами, лимитами и cooldown |
| `20260907109000_activate_catalog_promotions.sql` | переводит десять акций каталога из `draft` в `active` по решению владельца; пишет причину в `admin_actions`. Окна и лимиты не тронуты |

## Применение

`supabase db push` из этого репозитория **не работает** — см. §9 в
[MERCHANTS.md](MERCHANTS.md). Миграции применены через MCP
`apply_migration` в проект `lfudcjwkyctodeqaommv` («Blast App Dev»).

## Что ломается при откате

- Снятие `20260907100200` вернёт `offers_kind_consistent` — четыре типа
  акций перестанут заводиться, а семь черновиков станут невалидными.
- Снятие `20260907104100` вернёт ошибку `permission denied` у анонимного
  чтения меню.
- Откат `20260907105000` вернёт пятнадцать выдуманных заведений в каталог.
- `20260907108000` удаляет черновики предыдущей миграции безвозвратно —
  но только те, у которых пусто `catalog_code` и нет ни одной активации.
