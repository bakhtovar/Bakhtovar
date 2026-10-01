---
name: blast-simulator-bundle-id
description: "Bundle id: iOS — com.blastpay.app, Android — com.blastpay. Они разные: terminate/launch с чужим id молчат и врут, а APNS_TOPIC с чужим id роняет каждый пуш"
metadata:
  node_type: memory
  type: project
  originSessionId: 8100d970-8162-4d38-b270-897367147bec
  modified: 2026-09-01T13:20:00.000Z
---

Идентификаторы у платформ **разные**, и это не ошибка:

- iOS — `com.blastpay.app` (`ios/Runner.xcodeproj/project.pbxproj`,
  `PRODUCT_BUNDLE_IDENTIFIER`; Team ID `LL8FZUAY98`). Изменён 1 сентября 2026
  при регистрации App ID в кабинете разработчика; до этого был `com.blastpay`.
- Android — `com.blastpay` (`android/app/build.gradle.kts`, `applicationId`).
- В `RunnerTests` остался древний `com.example.blastFlutter` — в поиске по
  проекту попадаются все три.

**Why:** 1 сентября 2026 я несколько часов запускал `xcrun simctl terminate` и
`launch` со старым id. Обе команды на несуществующий id завершаются молча, с
нулевым кодом. `install` при этом работает — он читает id из Info.plist, — так
что новые сборки ставились под правильным именем, а на экране оставалось
старое приложение. Несколько правок я «проверил визуально» на бинарнике
двенадцатичасовой давности.

**How to apply:** для симулятора брать id из собранного пакета, а не из
памяти: `/usr/libexec/PlistBuddy -c "Print :CFBundleIdentifier"
build/ios/iphonesimulator/Runner.app/Info.plist`. Перед тем как верить
скриншоту, сверять время бинарника:
`stat -f %Sm "$(xcrun simctl get_app_container <UDID> <id>)/Frameworks/App.framework/App"`.
Молчание `terminate`/`launch` доказательством установки не является.
Тот же класс расхождения — [[blast-owner-bypass]].

**APNs:** `APNS_TOPIC` в секретах Edge Functions обязан быть равен **iOS**
bundle id — `com.blastpay.app`. 9 сентября 2026 там стоял `com.blastpay`
(андроидный), и это тихая поломка: секреты импортируются, `.p8` читается,
`send-push` отвечает `{"ok":true,"sent":0}`, потому что при пустой очереди до
APNs дело не доходит. Расхождение вскрылось бы только на первой реальной
отправке — ответом `DeviceTokenNotForTopic`. Значение уходит в заголовок
`apns-topic` напрямую (`supabase/functions/send-push/index.ts`, `providerToken`
рядом). Ставится через CLI, MCP секреты не пишет:
`supabase secrets set APNS_TOPIC=com.blastpay.app --project-ref lfudcjwkyctodeqaommv`.
