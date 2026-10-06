# Architecture

Goal: run 0.0.1 with as little operational work as possible, on free SaaS tiers, while keeping every module swappable (see `docs/releases/0.0.1.md`). Tables are in `docs/data-model.md`; the callable surface is in `docs/api.md`.

## Stack

| Concern | Choice | Why |
|---|---|---|
| Mobile app | React Native with Expo (TypeScript), one codebase for iPhone and Android, feature modules in a monorepo | iPhone first, Android follows without a rewrite (decision 0011); no cost |
| Map | MapLibre (`@maplibre/maplibre-react-native`) with OpenFreeMap tiles | One style on both platforms, no key, no usage billing, full control of the look (decision 0017) |
| Database, auth, realtime, files | Supabase, Canada (Central) region | One free SaaS covers Postgres, Auth, Realtime, Storage, and server functions; Canadian data residency |
| Link pages and deep links | Cloudflare Pages (static) | Free static hosting; serves the apple-app-site-association file (iPhone), the assetlinks.json file (Android), and the landing and expired pages |
| Code and issues | GitHub (this repo, `rdv-garage`) | Already in use |
| Operator tools and scheduled jobs | The owner's hosted Linux server | Already available; runs the operator toolkit, keep-alive, and backups |
| Builds and distribution | EAS Build (Expo), TestFlight, later Google Play testing tracks | Free build allowance; check current limits |
| Crash and diagnostics | App Store Connect and Play Console reports | Free, no SDK |

Not free and unavoidable: Apple Developer Program membership, and a Google Play developer registration when Android ships. A custom domain is optional; links can run on a Cloudflare `pages.dev` address first. Email for confirmation and password reset needs an SMTP provider and a sending domain, both deferred (see Open).

Free-tier limits change. Figures below came from public pricing summaries and must be checked against the provider's pricing page before relying on them.

## Free-tier limits that shape the design (Supabase free plan)

- 200 concurrent realtime connections
- 2 million realtime messages per month
- 500 MB database, 1 GB file storage
- 50,000 monthly active users
- 500,000 server function invocations per month
- Projects pause after a period of inactivity, and the free plan has no backups

## Decision on user data

Decision 0008 says RDV Garage owns its user data. Users live in our own Postgres database on Supabase, which is standard Postgres and fully exportable. Supabase Auth issues sessions but does not hold the only copy of any user. See decisions 0010 and 0013.

## System overview

```
Mobile app (React Native feature modules; iPhone first, Android next)
   |-- HTTPS (RPC, Edge Functions) --> Supabase Postgres (accounts, referral, crews, live, leaderboard schemas)
   |-- Realtime Broadcast + Presence --> per-crew private channels
   |-- Sessions issued by Supabase Auth; accounts created only by the register function
   |-- Storage --> avatars
   '-- Universal link (iPhone) / App Link (Android) --> Cloudflare Pages (landing, expired page, AASA, assetlinks)
Operator server (Linux): operator toolkit, scheduled keep-alive and database export
```

The app talks to Supabase through one wrapper in the core package (the Backend contract, see `docs/features/app-shell.md`). No module imports the Supabase SDK directly, so the provider can change without touching feature modules.

## Backend modules

One Postgres schema per app module. Schemas share only identity (`accounts.profiles`) and crew membership (`crews.members`).

| Schema | Owns | Notes |
|---|---|---|
| accounts | profiles | A profile row is the account. No profile, no access to anything |
| referral | invites | Invite validity and creation live in Postgres functions |
| crews | crews, members | Owner and member roles |
| live | sessions, session_crews, segments | Ephemeral positions are never stored; only session checkpoints |
| leaderboard | functions only | Read-only over live.segments; dropping it affects nothing else |

### Row Level Security

Privacy is enforced in the database, not the app:

- A user reads another user's profile only if they share a crew and both profiles are active.
- A suspended or deleted profile has no access to anything; policies check for an active profile.
- Invite checks and invite creation run as SECURITY DEFINER functions. Registration and deletion run as Edge Functions with the service role, since the caller has no profile or session yet. Operator tools use the service role from the operator server only.
- A segment is readable only by members of the crews in `session_crews`.
- Realtime channels are private, one per crew; authorization checks `crews.members`.
- Everything requires an existing active profile.

Decision 0004 (location only within crews) is a database rule, not a convention.

## Key flows

### Register

1. The app opens an invite link or reads the invite code, then shows the account form: handle, email, password, optional avatar, acceptance of the disclaimers (`docs/disclaimers.md`), and confirmation of being 18 or older.
2. The app calls the `register` Edge Function with the invite code and form values. Public signup is disabled in Supabase Auth, so this is the only way an account is created.
3. The function validates the invite (see invites.md), creates the Supabase Auth user through the admin API with the email and password, creates the profile with the referral and terms acceptance, and sends the confirmation email. If any step fails it undoes the earlier ones, so a failure leaves neither an auth user nor a profile.
4. The user confirms the email, then signs in. The app stores the session in the platform secure store.
5. Database scheduled jobs (pg_cron, no external secret) remove any auth user without a profile one hour after it was created, and remove accounts whose email is still unconfirmed 24 hours after creation, freeing their handle. Invites have no limits, so retrying costs nothing.

Confirmed by the build: with public signup disabled, an admin-created unconfirmed user plus a resend of the signup confirmation sends the confirmation email, and the confirmation link works. The Auth hook fallback was not needed.

Confirmation and password reset emails go through an SMTP provider. The provider and sending domain are deferred (see Open).

### Test mode

For testing playthroughs the `register` function runs in test mode, set by a server setting (`AUTO_CONFIRM_EMAIL`). The function honors it only when the project's environment name is `development` or `test`; in a project named `production` it ignores the setting and logs an error, so a copied setting cannot take effect. In test mode the function creates the user already confirmed and sends no email. The app learns the mode from the response: `register` returns `confirmed` in test mode and `check_email` otherwise, and the app goes straight to sign-in on `confirmed` or shows the Confirm email screen on `check_email`. In test mode a registration with an already-used email returns an `email_in_use` error, because test projects are private and use throwaway data, so the anti-enumeration rule applies to production only. Playthroughs therefore need no SMTP provider. Password reset, the Resend button, and the 24-hour expiry of unconfirmed accounts are not exercised in test mode and are tested in production mode once the provider exists. The setting lives only on the server; the app cannot turn it on, and the release checklist verifies it is off in production.

Real testers outside the development project need the SMTP provider chosen first.

Email links go to the link domain on Cloudflare Pages. The confirmation link completes confirmation on the page, which then says the email is confirmed and offers Open the app (universal link or App Link) and store links. The reset link opens the app's Reset password screen through the same deep links, with a web form on the page as a fallback for people without the app.

### Sign in, recovery, and devices

- Sign in is email and password through Supabase Auth. Sessions stay until sign out or revoke.
- Forgot password sends a reset link by email. Supabase does not sign out other devices on a reset, so right after a successful reset the app calls `revoke_other_sessions`.
- Change password goes through the `change_password` Edge Function, which verifies the current password, sets the new one, signs the caller in again (Supabase can end the caller's own session on a password change), returns that fresh session to the app, and revokes every other session.
- Settings, Devices lists active sessions with platform and last seen time; any can be revoked, or all others.
- Suspension bans the auth user in Supabase Auth, which revokes sessions and blocks sign-in; the sign-in screen shows the `suspended` message. Policies also require an active profile.
- Unconfirmed accounts can resend the confirmation email from the Confirm email screen.
- Rate limits and lockout on sign-in and reset are Supabase Auth's own.

### Live location

1. User taps Go live and picks crews. The app creates a `live.sessions` row and `live.session_crews` rows.
2. The app joins each chosen crew's private channel, announces itself with Presence, and broadcasts position about every 3 seconds while moving and every 15 seconds while stationary. A stationary session also sends a heartbeat every 30 seconds.
3. Positions go over Broadcast only. They are never written to the database.
4. About once a minute the app writes a checkpoint to `live.segments` and updates the session's `last_seen_at`. Each segment is one session within one Toronto-time week. Max speed and distance are tracked per segment and start at zero when a new week begins, so a session crossing Monday 00:00 never carries last week's max into the new week.
5. Stop sets `ended_at`. If the app is killed or loses signal, one database sweep (pg_cron) marks sessions ended when `last_seen_at` is more than 5 minutes old. That 5-minute value is the single staleness constant used by every query and policy, so the map, leaderboard, and policies agree. Once a session has ended, it no longer counts as live anywhere.
6. `live.session_crews` grants read access to segments only for the crews chosen at Go live. Segments are kept while the account exists.

Message budget, worked example: a driver broadcasting every 3 seconds sends about 1,200 messages per hour. In a five-person crew with everyone live, that is 6,000 sent and 24,000 received per hour, about 30,000 messages per crew-hour, assuming each delivery counts as a message. The free 2 million per month then covers roughly 66 crew-hours. The assumptions, the 3-second cadence and counting deliveries, must be checked against Supabase's current rules. Cadence is the lever; moving to the paid plan is the fallback.

### Weekly top speed leaderboard

- A SQL function returns, per crew, each member's highest `max_speed_kmh` from segments whose `week_start` is the requested Toronto-time week, restricted by `live.session_crews`.
- The app reads it on demand. No cron job, no stored aggregate.
- There are no speed limits or plausibility checks. Speeds are stored as the device measured them (decision 0007). Disclaimers carry the safety message.

### Delete account

The `delete-account` Edge Function (service role key in function secrets) runs the full accounts.md deletion path in order:

1. Transfer each owned crew to its longest-standing member, or dissolve it and kill its link if it has no other members.
2. Revoke the user's active invites and keep the invite records.
3. Delete sessions, session crews, segments, and avatar files.
4. Turn the profile into a tombstone (status deleted, personal fields cleared) so the referral chain stays intact. The profile row is not deleted.
5. Delete the auth user.

No database cascade is relied on for these steps.

## Operations

- The operator server (the owner's hosted Linux server) runs the operator toolkit and the scheduled jobs (`docs/ops.md`).
- Keep-alive: a scheduled job on the server calls a database function (`ping`) so there is real database activity. Supabase does not guarantee this prevents a pause; check the current rule.
- Backups: the free plan has none, so a scheduled job on the server exports the database using a read-only Postgres role (not the service key), encrypts the dump, rotates it, and copies it to free object storage off the server (Cloudflare R2 is the plan, since Cloudflare is already in use). The release checklist verifies that a backup restores and that the off-server copy exists.
- Database-side jobs (orphan cleanup, unconfirmed-account cleanup, stale session sweep) use pg_cron and need no external secrets.
- The service key lives only in Supabase function secrets and a root-readable environment file on the operator server. It is never in CI, git, or the app.
- Operator tools: a command line toolkit on the server, no admin UI and no network endpoint (decision 0015).
- Build secrets live in EAS environment secrets, never in the repo.
- Environments: one Supabase project for development and one for production, within the two free projects.

## Modularity mapping

| App module (workspace package) | Backend schema |
|---|---|
| referral | referral |
| accounts | accounts |
| crews | crews |
| map | none (reads LocationStream) |
| live-location | live, realtime channels |
| leaderboard | leaderboard |

Removing the leaderboard means removing one module registration and dropping the `leaderboard` schema. Nothing else depends on it.

## Cross-platform notes

- Background location: `expo-location` with a background task, in a development build (not Expo Go). iPhone needs When In Use first, then the upgrade to Always. Android needs foreground and background location permission and shows a persistent notification while live, which doubles as the live indicator.
- Deep links: iPhone uses universal links; Android uses App Links. Android carries link codes through install with the Play Install Referrer, so the clipboard fallback is iPhone-only.
- Push notifications, when added, go through Expo's push service over APNs and FCM.
- iPhone-only capabilities (Live Activity and Dynamic Island, CarPlay) and Android equivalents (ongoing notification, Android Auto) are platform-specific modules added later behind the same module registry.
- Maps handoff offers each platform's default maps app plus Google Maps and Waze.

## Known risks

- Free-tier pause and missing backups, mitigated by the scheduled jobs above.
- Realtime message ceiling, mitigated by adaptive cadence and a paid plan if usage grows.
- Client-reported speed can be spoofed or wrong; accepted, with disclaimers.
- Email delivery depends on an SMTP provider that is not chosen yet; confirmation and recovery do not work for real users until it is.
- Vendor dependence on Supabase, limited by the Backend wrapper and plain Postgres.
- Background location through cross-platform plugins is the hardest part to get reliable; it is built and tested first, with a native module as the fallback for a platform that misbehaves.
- Anonymous endpoints (`check_invite`, `register`) are the attack surface; they are rate limited and use long random codes (`docs/api.md`). Sign-in and reset are protected by Supabase Auth's own limits.

## Open

- Custom domain for links (pages.dev works to start)
- Free SMTP provider and sending domain: deferred by the owner; needed before confirmation and password reset work for real users. Resend is the leading candidate.
