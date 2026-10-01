---
name: blast-finmodel-v2
description: "Где лежит финансовая модель Blast Pay v2, как она устроена и какие открытые вопросы основателям её определяют"
metadata: 
  node_type: memory
  type: project
  originSessionId: 67c1819d-8445-4935-b81a-763790a3bd0d
  modified: 2026-09-13T20:29:48.557Z
---

Финмодель Blast Pay v2 (сентябрь 2026) — `~/blast docs/finmodel/BLAST_PAY_model_v2.xlsx`
плюс отчёт `BLAST_PAY_finmodel_report.md` рядом. Скрипты-построители (openpyxl) —
в scratchpad сессии, файл собирается программно: `build_a/b/c/d.py`, `assumptions.py`,
`snapshot.py` (9 комбинаций сценарий × план через LibreOffice). Исходник основателя —
`~/Documents/blast_unit_economics.numbers` (экспорт в xlsx через Numbers.app + osascript).

Ключевые факты, которые не выводятся из кода:
- Питч заведениям (`~/Documents/blast pitch deck/blast-sapporo.pdf`): 3–4 месяца бесплатно,
  «нет абонплаты», комиссия 4–8% с чеков через приложение, за постоянника 0%.
  Подписка заведений 150–250 TJS из исходной модели противоречит питчу.
- Подписка пользователя в БД: Plus 10 TJS/нед (34,80/мес), Premium 62,60/мес; flag выключен.
  «9 TJS» из брифа основателя — не расшифровано (ВОПРОС).
- Налоги РТ (НК ред. 17.12.2025): упрощёнка 6% валового дохода до 1 млн TJS, но не для
  посредничества по договорам комиссии (ст. 375 ч.2) — квалификация договора критична.
  Соцналог 20%/2%, НДФЛ 12% (вычет 156 TJS), дивиденды 12%, НДС 14% (13% с 2027).
- Курс НБТ 14.09.2026: 9,2521.
- LibreOffice стоит в /Applications (brew cask); recalc.py из skill xlsx требует патча
  `ignore_cleanup_errors` под Python 3.9 (копия в scratchpad `rc/`).

**Why:** модель пересобирается скриптом, а не правится руками; без этих фактов при
следующем обновлении легко вернуть ошибки исходника.

**How to apply:** правки допущений — в `assumptions.py` → пересборка → recalc →
`snapshot.py`. Связано с [[blast-legal-entity]], [[blast-supabase-projects]].
