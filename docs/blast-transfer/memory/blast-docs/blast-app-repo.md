---
name: blast-app-repo
description: Где лежит код гостевого приложения Blast и что оно из себя представляет
metadata:
  type: project
---

Гостевое приложение Blast (Flutter) живёт в `/Users/bakhtovarumarov/Desktop/blast pay/blast n1/blast` — **не** в рабочей папке `~/blast docs`, где только сайт, видео и юридические документы.

- Flutter 3.47 stable, 142 dart-файла, `version: 1.0.2+2`, applicationId `com.blastpay`.
- Ходит в Supabase-проект App Dev (`lfudcjwkyctodeqaommv`) через `supabase_flutter`.
- В репозитории уже есть `TODO-STORES.md` — осмысленный чеклист по Apple и Google, написанный до появления платных аккаунтов.
- Ловушки: `~/Downloads/blast_flutter` — пустой скелет на 2 файла, привязанный к другому проекту (`ddbkjhnbqdmdwqvivzhu`); `~/Desktop/blast pay/blast2*` — просто россыпь отдельных файлов. Это не приложение.

**Why:** код приложения не ищется из рабочей директории, а похожих папок с именем blast на диске около десятка — легко открыть не ту.

**How to apply:** для любой правки приложения сначала переключать рабочую директорию туда (там свой CLAUDE.md). Инфраструктура сайта — [[blast-site-infra]], базы — [[blast-supabase-projects]].

**CI (GitHub Actions, `.github/workflows/ci.yml`)** гоняет на каждый пуш в main две джобы: Flutter (gen_tokens → build_runner → `dart format --set-exit-if-changed` → analyze → test) и Supabase (`supabase db reset` на чистой базе + pgTAP). До 29.09.2026 `pubspec.lock` был в .gitignore и CI каждый раз брал свежие версии — ломался «сам». Теперь lock в git, в CI закреплён Flutter 3.47.0, analyzer 7.3 закреплён в паре с custom_lint_visitor (dependency_overrides в pubspec.yaml). При обновлении Flutter — обновлять и `flutter-version` в ci.yml, и lock. `lib/app/theme/tokens.dart` правится руками: `design-tokens.json` отстал (там Manrope, в приложении Inter), `tool/gen_tokens.dart` на нём падает и, если починить, затрёт ручные правки — не запускать `make tokens`. Проверка генератора из CI убрана 29.09. PostGIS в рабочей базе в schema public, первая миграция приведена к тому же. Логов CI отсюда не видно: репозиторий приватный, `gh` нет, Docker для локального `supabase start` тоже нет — ошибку джобы просить у Бахтовара скриншотом.
