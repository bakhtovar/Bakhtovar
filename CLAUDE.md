# Bakhtovar

Основатель — Бахтовар Умаров, пишет по-русски; отвечать по-русски, коротко и по делу.
Репозиторий хранит перенос контекста проекта Blast Pay с другого аккаунта.

Перед работой прочитай `docs/blast-transfer/START_HERE.md`, затем нужные файлы из
`docs/blast-transfer/memory/` (индекс — `MEMORY.md` в каждой папке). Карта доступов — `ACCESS.md`.
Секретов в репозитории нет, не добавляй.

## graphify
Граф знаний по `docs/` лежит в `graphify-out/` (241 узел, 47 сообществ).
Вопрос о проекте Blast Pay → сначала `graphify query "<вопрос>" --graph graphify-out/graph.json`,
потом чтение файлов. Обзор: `graphify-out/GRAPH_REPORT.md`. Новые документы → `/graphify docs --update`.
