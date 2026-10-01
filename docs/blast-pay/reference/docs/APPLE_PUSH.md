# Apple push notifications

Blast Pay sends iOS notifications directly through APNs. Supabase owns the
queue, preferences, quiet hours, delivery log and the Edge Function. Firebase
is not involved.

## Apple Developer setup

1. Open **Certificates, Identifiers & Profiles**.
2. Confirm that App ID `com.blastpay.app` has **Push Notifications** enabled.
3. Under **Keys**, create a key with **Apple Push Notifications service
   (APNs)** enabled and download the `.p8` file. Apple permits downloading it
   only once.
4. Record the Key ID and the Apple Developer Team ID.
5. Refresh the iOS provisioning profiles after enabling the capability.

## Supabase secrets

In the **Blast App Dev** project, add these Edge Function secrets:

- `APNS_KEY_ID` — Apple key ID
- `APNS_TEAM_ID` — Apple Developer team ID
- `APNS_PRIVATE_KEY` — complete contents of the `.p8` file
- `APNS_TOPIC` — `com.blastpay.app` (ровно bundle id, иначе APNs отвечает `DeviceTokenNotForTopic`)

Keep `CRON_SECRET` as already configured. Never put the `.p8` key in Git,
Dart defines, Info.plist, or a database table.

After the secrets are present, redeploy `send-push`. Debug builds register a
sandbox token; TestFlight and App Store builds register a production token.

## Verification

Use a real iPhone: APNs registration does not work in the iOS Simulator.

1. Sign in and continue through the permissions screen.
2. Accept the notification prompt.
3. Confirm an active row exists in `push_tokens` with platform `ios` and
   the correct environment.
4. Enqueue a transactional test notification for that user.
5. Confirm the outbox row becomes `sent` and a matching row appears in
   `notification_log`.
6. Tap the notification and confirm its internal `deep_link` opens.
