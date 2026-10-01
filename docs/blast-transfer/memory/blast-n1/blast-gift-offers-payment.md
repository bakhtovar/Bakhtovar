---
name: blast-gift-offers-payment
description: "Подарок и комбо вычитаются из платежа с 22.09.2026; набор по фикс-цене заводить с discount_fixed, иначе вычтется всё"
metadata:
  node_type: memory
  type: project
  originSessionId: 1f6302e1-6fdf-47b9-a8b5-7136e1807246
  modified: 2026-09-28T09:27:27.164Z
---

С 22 сентября 2026 (миграция `20260922162000_payment_quote_subtracts_gift`)
цена отмеченной позиции у `free_item`, `combo` и `buy_x_get_y` **уходит из
платежа**: официант называет сумму как есть, вычитает приложение. Так
считают и `payment_quote` (SmartPay), и `confirm_by_venue_code` (код кассы),
и зеркала в приложении (`PaymentOffer.previewDiscount`, `valueAtVenue`,
`giftValue`). Кэшбек и `special` из суммы не уходят. Старая версия этой
заметки (15.09, «подарок не уменьшает платёж, решение отложено») устарела —
по ней я 27.09 ошибочно назвал вопрос открытым.

**Ловушка комбо:** вид `combo` = подарок, то есть вычитается полная цена
отмеченных блюд. Набор «суп и чай за 45 вместо 53» так заводить нельзя —
у кассы выйдет «к оплате 0». Выгоду набора пишем в `discount_fixed` (в
сомони): фиксированная скидка берётся раньше подарочной ветки и в
`confirm_by_venue_code`, и в `valueAtVenue`. Так сделаны комбо Chatr
(`20260928100000_chatr_publish_combos.sql`). В `payment_quote` (SmartPay)
подарочная ветка перекрывает fixed — для SmartPay-заведений набор так не
заработает, там нужна правка функции.

**Why:** цена ошибки — недоплата заведению или спор гостя с кассиром.

**How to apply:** новое комбо-набор — всегда с `discount_fixed`; перед
публикацией прогнать `confirm_by_venue_code` в транзакции с откатом. Правка
правил — сначала `payment_quote`, затем зеркало в приложении и
`test/features/payment/quote_mirror_test.dart`.
Связано с [[blast-supabase-projects]].
