---
name: blast-provider-user-scope
description: Провайдеры Blast не autoDispose и переживают вход — кэш надо ронять подпиской на currentUserIdProvider
metadata: 
  node_type: memory
  type: project
  originSessionId: f30be1db-8a75-4624-9fbf-73dd06005e4d
  modified: 2026-09-07T15:20:19.806Z
---

Почти все `FutureProvider` в `lib/features/**/presentation/*_providers.dart` объявлены без `autoDispose`, поэтому их состояние (включая ошибку) живёт весь запуск приложения. `settingsSyncProvider` из корня слушает `myProfileProvider` с `fireImmediately`, то есть запускает его даже без сессии.

**Why:** отсюда баг «профиль иногда просит войти» (7 сентября 2026): запрос падал с UNAUTHENTICATED до входа, ошибка оставалась в провайдере, и после входа экран показывал её снова — лечило только перезапуск. Тот же механизм показал бы новому вошедшему чужие визиты и избранное.

**How to apply:** любой новый репозиторий с данными человека должен делать `ref.watch(currentUserIdProvider)` в своём `Provider` — ровно как там уже сделано с `uiLanguageProvider`. Тест-сторож: `test/features/profile/profile_cache_scope_test.dart`.

Логи Supabase — в UTC, Душанбе UTC+5: время на скриншотах симулятора надо сдвигать на −5 часов, иначе окно в `query_logs` окажется пустым. См. [[blast-supabase-projects]].
