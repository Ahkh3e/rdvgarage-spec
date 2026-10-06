# Architecture

Goal: run 0.0.1 with as little operational work as possible, on free SaaS tiers, while keeping every module swappable (see `docs/releases/0.0.1.md`).

## Stack

| Concern | Choice | Why |
|---|---|---|
| iOS app | SwiftUI, Swift packages per module | Matches the module design; no cost |
| Map | MapKit | Built in, no API key, no usage billing |
| Database, auth, realtime, files | Supabase, Canada (Central) region | One free SaaS covers Postgres, Auth, Realtime, Storage, and server functions; Canadian data residency |
| Invite landing and universal links | Cloudflare Pages (static) | Free static hosting; serves the apple-app-site-association file and the expired-invite page |
| Code, issues, scheduled jobs | GitHub (this repo, `rdv-garage`, GitHub Actions) | Already in use |
| Build and TestFlight | Xcode Cloud | Included with the Apple Developer Program |
| Crash and diagnostics | Xcode Organizer and TestFlight feedback | Free, no SDK |

Not free and unavoidable: Apple Developer Program membership. A custom domain is optional; universal links can run on a Cloudflare `pages.dev` address first.

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
iPhone app (SwiftUI modules)
   |-- HTTPS (REST / RPC) --> Supabase Postgres  (accounts, referral, crews, live, leaderboard schemas)
   |-- Realtime Broadcast + Presence --> per-crew private channels
   |-- Auth (Sign in with Apple) --> Supabase Auth
   |-- Storage --> avatars
   '-- Universal link --> Cloudflare Pages (landing, expired page, AASA)
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

- A user reads another user's profile only if they share a crew.
- A session segment is readable only by members of the crews in `session_crews`.
- Realtime channels are private, one per crew; authorization checks `crews.members`.
- Everything requires an existing profile.

This is the main reason for choosing Postgres with RLS: decision 0004 (location only within crews) is a database rule, not a convention.

## Key flows

### Register (referral and accounts)

1. The app opens an invite link or reads the pasted code.
2. User signs in with Apple through Supabase Auth, creating an auth user with no access.
3. The app calls `register(invite_code, handle)`, one Postgres function that, in a single transaction, validates the invite (active, not expired unless already redeemed, not full, inviter active), reserves or consumes a slot, creates the profile, and records the referral.
4. Failure leaves no profile, so no access. Orphan auth users are removed by a scheduled cleanup.

Invite creation, revoke, and the active-invite limit are also Postgres functions. This avoids a separate server and any signup-hook dependency.

### Live location

1. User taps Go live and picks crews. The app creates a `live_sessions` row and `session_crews` rows.
2. The app joins each chosen crew's private channel, announces itself with Presence, and broadcasts position at an adaptive rate: faster while moving, slower when stationary, with low-rate heartbeats when parked.
3. Positions go over Broadcast only. They are never written to the database.
4. About once a minute the app writes a checkpoint to `live.session_segments` (max speed so far, distance, week). A session crossing the week boundary writes to two segments.
5. Stop or presence loss ends the session. A session with no recent checkpoint is treated as ended by a query, so no server job is needed to close sessions.

Message budget, roughly: one live driver broadcasting every few seconds to a five-person crew uses on the order of tens of thousands of messages per hour. The free 2 million messages per month supports on the order of tens of crew-hours of driving. Cadence is the lever; moving to the paid plan is the fallback.

### Weekly top speed leaderboard

- A SQL function returns, per crew, each member's maximum `max_speed` from segments whose `week_start` is the requested Toronto-time week, restricted by `session_crews`.
- The app reads it on demand. No cron job, no stored aggregate.
- Cheating: speed is reported by the client. A database function rejects implausible values and jumps. This is a known soft spot for 0.0.1 and is accepted.

### Delete account

A server function (the one place a secret is needed) revokes the Apple token and then deletes the auth user, which cascades to profile, segments, and invites per accounts.md.

## Operations

- Keep-alive: a scheduled GitHub Actions job calls the API so the free project does not pause from inactivity.
- Backups: the free plan has none, so a scheduled GitHub Actions job exports the database to a private location.
- Operator tools: SQL scripts or a small CLI run with the service key from the operator's machine; no admin UI.
- Secrets live in GitHub Actions secrets and Xcode Cloud environment variables, never in the repo.
- Environments: one Supabase project for development and one for production, within the two free projects.

## Modularity mapping

| App module (Swift package) | Backend schema |
|---|---|
| Referral | referral |
| Accounts | accounts |
| Crews | crews |
| Map | none (reads LocationStream) |
| LiveLocation | live, realtime channels |
| Leaderboard | leaderboard |

Removing the leaderboard means removing one Swift package registration and dropping the `leaderboard` schema. Nothing else depends on it.

## Known risks

- Free-tier pause and missing backups, mitigated by the scheduled jobs above.
- Realtime message ceiling, mitigated by adaptive cadence and a paid plan if usage grows.
- Client-reported speed can be spoofed.
- Vendor dependence on Supabase, limited by the Backend wrapper and plain Postgres.

## Open

- Credential method: Sign in with Apple through Supabase Auth is recommended and fits this design
- Custom domain for invite links
