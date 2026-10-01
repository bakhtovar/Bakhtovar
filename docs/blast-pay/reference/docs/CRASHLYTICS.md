# Отчёты о падениях (Crashlytics)

Падения и пойманные ошибки уезжают в Firebase Crashlytics проекта, в котором
уже живут пуши Android. Работает на обеих платформах.

## Как устроено

| Где | Что |
| --- | --- |
| `lib/core/crash_reporter.dart` | Прослойка над `FirebaseCrashlytics`. Пока Firebase не поднят — пустышка, ничего не бросает. |
| `lib/core/logger.dart` | Единственный вход: `log.e` — пережитая ошибка, `log.fatal` — сломанный экран или приложение. |
| `lib/main.dart` | `FlutterError.onError` и `PlatformDispatcher.instance.onError` — оба на `log.fatal`. |
| `lib/core/firebase_bootstrap.dart` | Поднимает Firebase и включает сбор. |
| `Makefile`, цель `ipa` | После архива выгружает dSYM, чтобы нативные кадры читались именами. |

Разделение `e` / `fatal` не косметическое: по `fatal` считается «процент
сессий без сбоев» — цифра, на которую в консоли смотрят первой. Пойманная
ошибка сети, после которой гость нажал «повторить», к ней отношения не имеет.

В Crashlytics уходит исходная ошибка, а не обёртка `AppException`: иначе
отчёты сгруппировались бы в одну кучу с одинаковым заголовком. Код ошибки
(`ErrorCode`) идёт отдельным полем `reason` и виден в списке первой строкой.

## Почему в репозитории нет `google-services.json` и `GoogleService-Info.plist`

Потому что Crashlytics они не нужны. Идентификатор приложения он берёт из
`FirebaseOptions`, а те собираются из `--dart-define` — ровно так же, как
ключи Supabase и всё остальное в этом проекте.

Файлы нужны не отчётам, а расшифровке **нативных** кадров — движка,
Objective-C, Java/Kotlin. Со стеками Dart, а это подавляющее большинство того,
что здесь падает, ничего делать не нужно: `--obfuscate` в сборках нет, и стеки
приезжают читаемыми.

Что с нативными кадрами по платформам:

- **iOS — закрыто, и без всякого plist.** `upload-symbols` умеет принимать
  идентификатор приложения флагом `-ai`, не разбирая `GoogleService-Info.plist`.
  Выгрузка встроена в `make ipa` отдельной строкой рецепта, сразу после
  архива; идентификатор берётся из `.env.local`. Нет ключей или не удалось —
  печатается предупреждение, релиз не страдает.
- **Android — открыто.** R8 здесь включён (в сборке есть
  `minifyReleaseWithR8` и `build/app/outputs/mapping/release/mapping.txt`),
  значит кадры Java и Kotlin в отчётах будут обфусцированы. Своего `-ai` для
  mapping нет: его загружает gradle-плагин, а он тянет `google-services` и,
  следом, сам `google-services.json`. Полумеры не работают — плагин
  Crashlytics без `google-services` падает на `UploadMappingFileTask`
  («googleServicesResourceRoot»), а `google-services` без файла — на самом
  файле.

Пока это осознанный долг, а не недосмотр: своего кода на Kotlin в приложении
почти нет, падать там может разве что внутри плагинов. Станет видно, что
Android-отчёты упираются в нечитаемые кадры, — решение известно и стоит одного
файла в репозитории.

## Ключи на новой машине

Оба приложения в консоли Firebase заведены: Android (`com.blastpay`) и iOS
(`com.blastpay.app`), проект `blast-pay-27d4b`. Скачивать
`GoogleService-Info.plist` и `google-services.json` не нужно — нужны только
значения, по четыре на Android и по два на iOS, все из Project settings →
Your apps. В `.env.local`:

```
FIREBASE_API_KEY=...            # Android
FIREBASE_APP_ID=1:...:android:...
FIREBASE_SENDER_ID=...          # общий для проекта
FIREBASE_PROJECT_ID=...         # общий для проекта
FIREBASE_IOS_API_KEY=...
FIREBASE_IOS_APP_ID=1:...:ios:...
```

Без них сборка проходит, просто падения никуда не уезжают; `make ipa` и
`make aab` скажут об этом отдельной строкой.

## «Could not locate configuration file: GoogleService-Info.plist»

Эту ошибку iOS пишет в системный лог на каждом запуске, следом за ней —
«The default Firebase app has not yet been configured». Всё в порядке: так и
должно быть при инициализации из Dart. Нативная часть Firebase просыпается
раньше, чем `main()` доходит до `Firebase.initializeApp`, не находит файла и
жалуется — а через долю секунды приложение настраивает её из
`FirebaseOptions`. Проверять надо не эти строки, а те, что идут после:

```
(FirebaseCrashlytics) [Firebase/Crashlytics] Version 12.18.0
... firebase-settings.crashlytics.com/spi/v2/platforms/ios/gmp/1:...:ios:.../settings
```

Вот они и означают, что Crashlytics поднялся с нужным приложением.

## Что это меняет в App Store

Отчёты о падениях — собираемые данные, их полагается объявить. Уже сделано:

- `ios/Runner/PrivacyInfo.xcprivacy` — тип `CrashData`, `Linked = false`;
- `docs/app-store-connect-forms.md` — строка **Crash Data** в анкете
  App Privacy (App Functionality, Linked — No, Tracking — No).

`Linked = false` держится ровно на одном: идентификатор гостя в отчёт не
передаётся. Появится `setUserIdentifier` — оба ответа меняются на Yes.

## Как проверить, что работает

Из отладочной сборки падения намеренно не отправляются: они мешались бы с
настоящими и портили ту самую цифру. Чтобы проверить связку, не собирая
релиз:

```bash
make run CRASHLYTICS_IN_DEBUG=true
```

Дальше уронить приложение (например, `throw Exception('проверка')` по кнопке)
и перезапустить: Crashlytics отправляет отчёт не в момент падения, а при
следующем запуске. В консоли он появляется в течение нескольких минут.

`FirebaseAppDelegateProxyEnabled` в `ios/Runner/Info.plist` должен оставаться
`false`. На нём держится выдача токена APNs: с включённым прокси
`firebase_messaging` перехватывает делегата приложения, и собственная работа
с APNs в `AppDelegate.swift` ломается.
