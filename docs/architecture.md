# Architecture

Goal: run 0.0.1 with as little operational work as possible, on free SaaS tiers, while keeping every module swappable (see `docs/releases/0.0.1.md`).

## Stack

| Concern | Choice | Why |
|---|---|---|
| Mobile app | React Native with Expo (TypeScript), one codebase for iPhone and Android, feature modules in a monorepo | iPhone first, Android follows without a rewrite (decision 0011); no cost |
| Map | react-native-maps | Apple Maps on iPhone, Google Maps on Android; no map usage billing for display |
| Database, auth, realtime, files | Supabase, Canada (Central) region | One free SaaS covers Postgres, Auth, Realtime, Storage, and server functions; Canadian data residency |
| Invite landing and deep links | Cloudflare Pages (static) | Free static hosting; serves the apple-app-site-association file (iPhone), the assetlinks.json file (Android), and the expired-invite page |
| Auth email | Supabase Auth with a free-tier SMTP provider | Email confirmation and password reset for email and password registration (decision 0012); the built-in email sender is too limited for real use, so a separate sender is needed |
| Code, issues, scheduled jobs | GitHub (this repo, `rdv-garage`, GitHub Actions) | Already in use |
| Builds and distribution | EAS Build (Expo), TestFlight, later Google Play testing tracks | Free build allowance; check current limits |
| Crash and diagnostics | App Store Connect and Play Console reports | Free, no SDK |

Not free and unavoidable: Apple Developer Program membership, and a Google Play developer registration when Android ships. A custom domain is optional; deep links can run on a Cloudflare `pages.dev` address first.

Free-tier limits change. Figures below came from public pricing summaries and must be checked against the provider's pricing page before relying on them.

## Free-tier limits that shape the design (Supabase free plan)

- 200 concurrent realtime connections
- 2 million realtime messages per month
- 500 MB database, 1 GB file storage
- 50,000 monthly active users
- 500,000 server function invocations per month
- Projects pause after a period of inactivity, and the free plan has no backups

## Decision on user data

Decision 0008 says RDV Garage owns its user database. That still holds: users live in our own Postgres database on Supabase, which is standard Postgres and fully exportable. Supabase Auth manages credentials but does not hold the only copy of any user. Hosting is a managed service; ownership is the schema and the data. See decision 0010.

## System overview

```
Mobile app (React Native feature modules; iPhone first, Android next)
   |-- HTTPS (REST / RPC) --> Supabase Postgres  (accounts, referral, crews, live, leaderboard schemas)
   |-- Realtime Broadcast + Presence --> per-crew private channels
   |-- Auth (email and password; accounts created by the register function) --> Supabase Auth
   |-- Storage --> avatars
   '-- Universal link (iPhone) / App Link (Android) --> Cloudflare Pages (landing, expired page, AASA, assetlinks)
GitHub Actions: scheduled keep-alive and database export
```

The app talks to Supabase through one wrapper in the Core package (the Backend contract). No module imports the Supabase SDK directly, so the provider can change without touching feature modules.

## Backend modules

One Postgres schema per app module. Schemas share only identity (`accounts.profiles`) and crew membership (`crews.members`).

| Schema | Owns | Notes |
|---|---|---|
| accounts | profiles | A profile row is the account. No profile, no access to anything |
| referral | invites, redemptions | Invite record fields per accounts.md; slot reservation and atomic checks live in one Postgres function |
| crews | crews, members, crew links | Owner and member roles |
| live | live_sessions, session_segments, session_crews | Ephemeral positions are never stored; only session checkpoints |
| leaderboard | views and functions only | Read-only over live.session_segments; dropping it affects nothing else |

### Row Level Security

Privacy is enforced in the database, not the app:

- A user reads another user's profile only if they share a crew and both profiles are active.
- A suspended or deleted profile (`accounts.status`) has no access to anything; policies check for an active profile.
- Invite checks and invite creation run as SECURITY DEFINER functions, and registration and deletion run as Edge Functions with the service role, since the invitee has no profile and no crew yet. Operator tools use the service role from the operator's machine only.
- A session segment is readable only by members of the crews in `session_crews`.
- Realtime channels are private, one per crew; authorization checks `crews.members`.
- Everything requires an existing profile.

This is the main reason for choosing Postgres with RLS: decision 0004 (location only within crews) is a database rule, not a convention.

## Key flows

### Register (referral and accounts)

1. The app opens an invite link or reads the code, then shows the registration form: handle, email, password.
2. The app calls the `register` Edge Function with the invite code and form values. Public signup is disabled in Supabase Auth, so this is the only way an account is created.
3. The function validates the invite (active, not expired unless already redeemed, not full, inviter active), reserves or consumes a slot, creates the auth user through the admin API, and creates the profile and referral record, all as one operation. If any step fails it undoes the earlier ones, so a failure leaves neither an auth user nor a profile.
4. The app signs in with the email and password. Email confirmation and password reset go through the SMTP provider.
5. A database scheduled job (pg_cron, no external secret) removes any auth user that has no profile, as a safety net, after a grace period (value to be decided).

The `register` function runs with the service role key held in Supabase function secrets, never in CI or the app.

The invite landing page is static on Cloudflare Pages. To show the expired page it calls one anonymous-callable function, `check_invite(code)`, that returns only a status (valid, expired, revoked, full) and no inviter or user data. It is rate limited and, with `register`, is one of only two anonymous entry points.

### Live location

1. User taps Go live and picks crews. The app creates a `live_sessions` row and `session_crews` rows.
2. The app joins each chosen crew's private channel, announces itself with Presence, and broadcasts position at an adaptive rate: faster while moving, slower when stationary, with low-rate heartbeats when parked.
3. Positions go over Broadcast only. They are never written to the database.
4. About once a minute the app writes a checkpoint to `live.session_segments`. Each segment is one session within one Toronto-time week. Max speed and distance are tracked per segment and start at zero when a new week begins, so a session crossing Monday 00:00 never carries last week's max into the new week.
5. Stop sets `ended_at`. If the app is killed or loses signal, one database sweep (pg_cron) marks sessions ended when their last checkpoint is stale. Staleness is one shared constant (value to be decided) used by every query and policy, so the map, leaderboard, and policies agree. Once a session has ended, it no longer counts as live anywhere.
6. `session_crews` grants read access to segments only for the crews chosen at Go live. Retention follows privacy.md.

Message budget, worked example: a driver broadcasting every 3 seconds sends about 1,200 messages per hour. In a five-person crew with everyone live, that is 6,000 sent and 24,000 received per hour, about 30,000 messages per crew-hour, assuming each delivery counts as a message. The free 2 million per month then covers roughly 66 crew-hours. The assumptions, the 3-second cadence and counting deliveries, must be checked against Supabase's current rules. Cadence is the lever; moving to the paid plan is the fallback.

### Weekly top speed leaderboard

- A SQL function returns, per crew, each member's maximum `max_speed` from segments whose `week_start` is the requested Toronto-time week, restricted by `session_crews`.
- The app reads it on demand. No cron job, no stored aggregate.
- Cheating: speed is reported by the client. A database function rejects implausible values and jumps. This is a known soft spot for 0.0.1 and is accepted.

### Delete account

The `delete-account` Edge Function (service role key in function secrets) runs the full accounts.md deletion path in order:

1. Transfer each owned crew to its longest-standing member, or dissolve it and kill its link if it has no other members.
2. Revoke the user's active invites and keep the invite records.
3. Delete session segments, session crews, and avatar files.
4. Turn the profile into a tombstone (status deleted, personal fields cleared) so the referral chain stays intact. The profile row is not deleted.
5. Delete the auth user.

No database cascade is relied on for these steps.

## Operations

- Keep-alive: a scheduled GitHub Actions job calls a database function (`ping`) so there is real database activity. Supabase does not guarantee this prevents a pause; check the current rule.
- Backups: the free plan has none, so a scheduled GitHub Actions job exports the database using a read-only Postgres role (not the service key), encrypts the dump, and stores it as a private workflow artifact with limited retention.
- GitHub disables scheduled workflows after a long stretch of repo inactivity, which would silently stop both jobs. Missing recent artifacts is the signal to check.
- Database-side jobs (orphan cleanup, stale session sweep) use pg_cron and need no external secrets.
- The service key never goes into CI. It stays on the operator's machine.
- Operator tools: SQL scripts or a small CLI run with the service key from the operator's machine; no admin UI.
- Secrets live in GitHub Actions secrets and EAS environment secrets, never in the repo.
- Environments: one Supabase project for development and one for production, within the two free projects.

## Modularity mapping

| App module (workspace package) | Backend schema |
|---|---|
| Referral | referral |
| Accounts | accounts |
| Crews | crews |
| Map | none (reads LocationStream) |
| LiveLocation | live, realtime channels |
| Leaderboard | leaderboard |

Removing the leaderboard means removing one module registration and dropping the `leaderboard` schema. Nothing else depends on it.

## Cross-platform notes

- Background location: `expo-location` with a background task, in a development build (not Expo Go). iPhone needs Always authorization with the When In Use first, then upgrade flow. Android needs foreground and background location permission and shows a persistent notification while live, which doubles as the live indicator.
- Deep links: iPhone uses universal links; Android uses App Links. Android can carry the invite code through install with the Play Install Referrer, so the clipboard paste fallback is iPhone-only.
- Push notifications, when added, go through Expo's push service over APNs and FCM.
- iPhone-only capabilities (Live Activity and Dynamic Island, CarPlay) and Android equivalents (ongoing notification, Android Auto) are platform-specific modules added later behind the same module registry.
- Maps handoff offers each platform's default maps app plus Google Maps and Waze.

## Known risks

- Free-tier pause and missing backups, mitigated by the scheduled jobs above.
- Realtime message ceiling, mitigated by adaptive cadence and a paid plan if usage grows.
- Client-reported speed can be spoofed.
- Vendor dependence on Supabase, limited by the Backend wrapper and plain Postgres.
- Background location through cross-platform plugins is the hardest part to get reliable; it is built and tested first, with a native module as the fallback for a platform that misbehaves.

## Open

- Free SMTP provider and sending domain: deferred by the owner; needed before email confirmation and password reset work for real users. Resend is the leading candidate.
- Custom domain for invite links
