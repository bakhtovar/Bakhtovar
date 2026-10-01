# Graph Report - docs  (2026-10-01)

## Corpus Check
- Corpus is ~48,489 words - fits in a single context window. You may not need a graph.

## Summary
- 241 nodes · 234 edges · 47 communities (15 shown, 32 thin omitted)
- Extraction: 80% EXTRACTED · 20% INFERRED · 0% AMBIGUOUS · INFERRED: 47 edges (avg confidence: 0.8)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- Community 0
- Community 1
- Community 2
- Community 3
- Community 4
- Community 5
- Community 6
- Community 7
- Community 8
- Community 9
- Community 10
- Community 11
- Community 12
- Community 13
- Community 14
- Community 15
- Community 16
- Community 17
- Community 18
- Community 19
- Community 20
- Community 21
- Community 22
- Community 23
- Community 24
- Community 25
- Community 26
- Community 27
- Community 28
- Community 29
- Community 30
- Community 31
- Community 32
- Community 33
- Community 34
- Community 35
- Community 36
- Community 37
- Community 38
- Community 39
- Community 40
- Community 41
- Community 42
- Community 43
- Community 44
- Community 45
- Community 46

## God Nodes (most connected - your core abstractions)
1. `Blast Pay app` - 11 edges
2. `Supabase project Blast App Dev` - 9 edges
3. `SmartPay gateway` - 8 edges
4. `Blast docs memory index` - 8 edges
5. `Supabase project Blast App Dev (lfudcjwkyctodeqaommv)` - 8 edges
6. `Early access queue` - 7 edges
7. `Blast app env config (SUPABASE_URL, Google OAuth, Firebase, geofence)` - 7 edges
8. `Design tokens pipeline (JSON to tokens.dart)` - 6 edges
9. `Early access queue blocks App Store reviewer` - 5 edges
10. `Gift/combo subtracted from payment (since 2026-09-22)` - 5 edges

## Surprising Connections (you probably didn't know these)
- `Blast app repo location` --references--> `Blast Pay app`  [EXTRACTED]
  blast-transfer/memory/blast-docs/blast-app-repo.md → blast-pay/01-продукт.md
- `Blast Pay financial model v2` --conceptually_related_to--> `Blast Pay app`  [INFERRED]
  blast-transfer/memory/blast-docs/blast-finmodel-v2.md → blast-pay/01-продукт.md
- `No legal entity; individual Apple account` --conceptually_related_to--> `SmartPay gateway`  [INFERRED]
  blast-transfer/memory/blast-docs/blast-legal-entity.md → blast-pay/06-оплата-smartpay.md
- `admin_grant_user_access RPC` --conceptually_related_to--> `Early access queue`  [INFERRED]
  blast-transfer/memory/blast-n1/blast-access-grant-invites.md → blast-pay/reference/EARLY_ACCESS.md
- `SmartPay production: webhook, signature, Telegram` --references--> `SmartPay gateway`  [EXTRACTED]
  blast-transfer/memory/blast-docs/blast-smartpay-prod.md → blast-pay/06-оплата-smartpay.md

## Import Cycles
- None detected.

## Hyperedges (group relationships)
- **Design token system** — design_tokens, depth_layers, brand_color, no_literal_colors [EXTRACTED 0.95]
- **Early access data model** — user_access_table, founding_emails_table, invite_ledger_table, waitlist_table [EXTRACTED 0.95]
- **Release pipeline** — release_rule, build_version, app_store_release, google_play_release [EXTRACTED 0.95]
- **Payment discount calculation** — docs_blast_transfer_memory_blast_n1_blast_gift_offers_payment_payment_quote, docs_blast_transfer_memory_blast_n1_blast_gift_offers_payment_confirm_by_venue_code, docs_blast_transfer_memory_blast_n1_blast_gift_offers_payment_paymentoffer_mirror, docs_blast_transfer_memory_blast_n1_blast_gift_offers_payment_discount_fixed_combo_trap [EXTRACTED 1.00]
- **Blast docs memory notes** — admin_panels, site_infra, supabase_projects, smartpay_prod [INFERRED 0.75]
- **App Review submission materials** — docs_blast_pay_reference_docs_app_review_account_app_review_account_md, docs_blast_pay_reference_docs_app_review_video_app_review_video_md, docs_blast_pay_reference_docs_app_review_notes_app_review_notes_txt, docs_blast_pay_reference_docs_app_review_reply_app_review_reply_md [INFERRED 0.85]
- **Early access gating and bypass** — docs_blast_transfer_memory_blast_n1_blast_app_review_early_access_queue, docs_blast_transfer_memory_blast_n1_blast_owner_bypass_early_access_bypass, docs_blast_transfer_memory_blast_n1_blast_owner_bypass_has_early_access, docs_blast_transfer_memory_blast_n1_blast_app_review_app_store_review [INFERRED 0.85]
- **Push delivery pipeline** — docs_blast_transfer_memory_blast_n1_blast_push_quiet_hours_enqueue_push, docs_blast_transfer_memory_blast_n1_blast_push_quiet_hours_notification_outbox, docs_blast_transfer_memory_blast_n1_blast_send_push_verify_jwt_dispatch_push_cron, docs_blast_transfer_memory_blast_n1_blast_send_push_verify_jwt_send_push_function, docs_blast_transfer_memory_blast_n1_blast_simulator_bundle_id_apns_topic [INFERRED 0.85]
- **Push delivery channels via send-push** — docs_blast_pay_reference_docs_android_push_send_push_edge_function, docs_blast_pay_reference_docs_android_push_fcm_android_push_delivery, docs_blast_pay_reference_docs_apple_push_apns_direct_delivery [INFERRED 0.85]

## Communities (47 total, 32 thin omitted)

### Community 0 - "Community 0"
Cohesion: 0.10
Nodes (17): Blast Pay payment prototype, Bank selection decision, Blast Pay UX rationale, GIFT_ITEM promotion type, Offer ticket card, Server-side calculation, State matrix, Admin panel (+9 more)

### Community 1 - "Community 1"
Cohesion: 0.12
Nodes (20): Blast app env config (SUPABASE_URL, Google OAuth, Firebase, geofence), blast_flutter pubspec v1.0.3+10, Blast Pay app, Access and secrets map for new computer, START_HERE handoff to new Claude, Different bundle ids iOS com.blastpay.app vs Android com.blastpay, Blast Pay financial model v2, Firebase FCM and Crashlytics (+12 more)

### Community 2 - "Community 2"
Cohesion: 0.12
Nodes (17): Two admin panels: merchant cabinet and internal teamcave, Blast app repo location, Bank deeplinks (13 Tajik banks), Blast docs memory index, Cloudflare Workers deploy via wrangler, Server-side discount calculation mirror, Supabase Edge Functions on Deno, Should gift reduce payment amount (open question) (+9 more)

### Community 3 - "Community 3"
Cohesion: 0.12
Nodes (12): Demo account for App Review, Password sign-in for reviewer, App Review correction message, App Review Notes, Guideline 2.1 sign-in rejection reply, Beta App Review reply, Guideline 2.1 rejection Sept 3, Demo video on real iPhone (+4 more)

### Community 4 - "Community 4"
Cohesion: 0.12
Nodes (15): App Store Review Guidelines audit, delete_my_account RPC, In-app account deletion (5.1.1v), Privacy policy URL blocker (5.1.1i), report_review RPC, UGC report review (1.2), Payment lifecycle, Apex domain redirect breaks deep links (+7 more)

### Community 5 - "Community 5"
Cohesion: 0.15
Nodes (12): android_push_service.dart, FCM Android push delivery, FIREBASE_SERVICE_ACCOUNT secret, send-push Edge Function, APNs direct delivery, APNs secrets, push_tokens table, send-push Edge Function (+4 more)

### Community 6 - "Community 6"
Cohesion: 0.16
Nodes (14): 45 functions and 4 tables only in cloud DB, supabase_migrations.schema_migrations.statements, confirm_by_venue_code, Combo trap: use discount_fixed for fixed-price sets, Gift/combo subtracted from payment (since 2026-09-22), payment_quote (SmartPay), PaymentOffer.previewDiscount/valueAtVenue (app mirror), Merchant panel deploy at blastpay.tj/admin (+6 more)

### Community 7 - "Community 7"
Cohesion: 0.15
Nodes (14): currentUserIdProvider, Providers not autoDispose; scope to currentUserIdProvider, enqueue_push / push_send_after, notification_outbox, Push quiet hours 09:00-22:00 Dushanbe, dispatch-push pg_cron job, send-push edge function, send-push verify_jwt=false (+6 more)

### Community 8 - "Community 8"
Cohesion: 0.20
Nodes (11): App Store Review, Early access queue blocks App Store reviewer, Passwordless login (Apple, Google, magic-link), Android versionCode computed from version, make ipa / make aab build targets, App version 1.1.1 scheme, Git branch deletion blocked by auto-mode classifier, Early access bypass (dev_testers, admins) (+3 more)

### Community 9 - "Community 9"
Cohesion: 0.20
Nodes (11): authedRpc session check wrapper, catalog_screens.dart (3700 lines), lib/features map, Feature layering data/domain/presentation, Flutter 3.47 / Dart 3.9 stack, go_router StatefulShellRoute navigation, Riverpod non-autoDispose state, RPC-only access with security definer and RLS (+3 more)

### Community 10 - "Community 10"
Cohesion: 0.18
Nodes (9): Brand color #FF5A2B, ~106 const widgets reading Lx/BlastColors, Four depth layers bg/surface/elevated/sunken, Design tokens pipeline (JSON to tokens.dart), Design system, DESIGN.md color spec, Glass effect usage rule, Lx static localization (+1 more)

### Community 11 - "Community 11"
Cohesion: 0.24
Nodes (10): Raw SQL access grant gives no invites; use admin_grant_user_access, admin_grant_user_access RPC, Early access queue, Email normalization lower(trim()), founding_emails table, invite_ledger journal, Blast n1 memory index, 57 database tables (+2 more)

### Community 12 - "Community 12"
Cohesion: 0.20
Nodes (9): AppException and log funnel with CrashReporter, App Store publication, Apple Developer account, Version 1.0.3, only build number changes, Firebase Crashlytics, Google Play account, Google Play publication, Makefile commands (gen, tokens, analyze, ipa) (+1 more)

### Community 13 - "Community 13"
Cohesion: 0.47
Nodes (6): Product overview, SmartPay payment, Open questions, Code map, Early access audit and plan, Blast Pay knowledge pack README

### Community 15 - "Community 15"
Cohesion: 0.67
Nodes (3): AGENTS.md graphify, CLAUDE.md graphify, graphify skill

## Knowledge Gaps
- **124 isolated node(s):** `Architecture`, `Design system`, `Patterns and conventions`, `Supabase backend`, `SmartPay payment` (+119 more)
  These have ≤1 connection - possible missing edges or undocumented components. (Counts symbols only; 127 node(s) total have ≤1 connection when file, concept and rationale nodes are included.)
- **32 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `Payment lifecycle` connect `Community 4` to `Community 0`?**
  _High betweenness centrality (0.037) - this node is a cross-community bridge._
- **Why does `Supabase project Blast App Dev` connect `Community 1` to `Community 9`, `Community 2`, `Community 11`?**
  _High betweenness centrality (0.036) - this node is a cross-community bridge._
- **Are the 2 inferred relationships involving `Blast Pay app` (e.g. with `Blast Pay financial model v2` and `Supabase project Blast App Dev`) actually correct?**
  _`Blast Pay app` has 2 INFERRED edges - model-reasoned connections that need verification._
- **Are the 2 inferred relationships involving `SmartPay gateway` (e.g. with `No legal entity; individual Apple account` and `Supabase Edge Functions on Deno`) actually correct?**
  _`SmartPay gateway` has 2 INFERRED edges - model-reasoned connections that need verification._
- **What connects `Architecture`, `Design system`, `Patterns and conventions` to the rest of the system?**
  _124 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Community 0` be split into smaller, more focused modules?**
  _Cohesion score 0.1 - nodes in this community are weakly interconnected._
- **Should `Community 1` be split into smaller, more focused modules?**
  _Cohesion score 0.12105263157894737 - nodes in this community are weakly interconnected._