# Data model

Postgres on Supabase. One schema per module (see `docs/architecture.md`). All timestamps are `timestamptz`. Ids are `uuid`. Week boundaries use America/Toronto.

## accounts

`accounts.profiles`

| Column | Notes |
|---|---|
| id | primary key; equals the auth user id but is not a foreign key to the auth table, so deleting the auth user never deletes or blocks the profile tombstone |
| handle | unique, 3-20 chars, lowercase letters, numbers, underscore |
| avatar_path | nullable; private storage path |
| car_icon | one of the icon keys in `docs/features/car-icons.md`; not null, default `gt` |
| invited_by | nullable profile id (null only for the operator-created first account) |
| invite_id | nullable invite id used to join |
| status | active, suspended, deleted |
| terms_version, terms_accepted_at | disclaimers acceptance |
| email | not stored here; it lives only in the auth table and is never exposed to other users |
| handle_changed_at | nullable |
| is_synthetic | true for users made by the operator toolkit; default false |
| created_at | |

A deleted profile is a tombstone: status deleted, handle freed or reserved per the open question, avatar and personal fields cleared, row kept so the referral chain holds.

## referral

`referral.invites`

| Column | Notes |
|---|---|
| id, inviter_id | |
| code | short unique code in the link |
| created_at, expires_at | 24 hours |
| status | active, revoked, disabled; expired is derived from expires_at |
| revoked_by, revoked_at | inviter, operator, or suspension |

Who joined an invite is `accounts.profiles.invite_id`.

## crews

`crews.crews`

| Column | Notes |
|---|---|
| id, name, description, avatar_path | |
| owner_id | |
| link_code | reusable, no expiry, regenerable |
| status | active, dissolved |
| is_synthetic | true for crews made by the operator toolkit; default false |
| created_at | |

`crews.members`: `crew_id`, `user_id`, `role` (owner, member), `joined_at`; primary key (crew_id, user_id). Exactly one owner per active crew.

`crews.selections`: `user_id`, `crew_id`; the crews the user has selected for the map and Board, synced across their devices.

## live

`live.sessions`: `id`, `user_id`, `started_at`, `ended_at` (nullable), `last_seen_at`, `platform`. A session is live when `ended_at` is null and `last_seen_at` is within 5 minutes.

`live.session_crews`: `session_id`, `crew_id`; the crews the user chose at Go live.

`live.segments`: `session_id`, `week_start` (Toronto Monday date), `max_speed_kmh`, `max_speed_at`, `distance_m`, `updated_at`; primary key (session_id, week_start).

Live positions are never stored.

## leaderboard

No tables. `leaderboard.weekly_top_speed(crew_id, week_start)` reads `live.segments` joined to `live.session_crews`, `crews.members`, and `accounts.profiles`.

## Access policies

| Table | Read | Write |
|---|---|---|
| accounts.profiles | the user; members of a shared crew (limited columns) | the user, for avatar and handle only, via function |
| referral.invites | the inviter for their own invites | functions only |
| crews.crews | members | owner, via function |
| crews.members | members of the crew | functions only (join, leave, remove, transfer) |
| crews.selections | the user | the user, via set_selected_crews |
| live.sessions | the user | functions only |
| live.session_crews | the user, and members of the listed crews | functions only |
| live.segments | members of crews listed in session_crews | the session's user, via function |

Every policy also requires the caller's profile to be active. Realtime channel `crew:<crew_id>` is private; join requires membership in `crews.members`.

## Retention

Everything above is kept while the account exists. Deleting the account removes sessions, session_crews, segments, the auth user with its email, and avatar files; invite records and the profile tombstone remain for the referral chain.
