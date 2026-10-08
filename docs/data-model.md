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

## rdvs

`rdvs.rdvs`

| Column | Notes |
|---|---|
| id, host_id | host is a profile id |
| title, kind | kind is meet, cruise or private_event |
| area_name | the coarse name shown for a private event before RSVP; the only place text a member without an answer can read |
| starts_at, ends_at | ends_at nullable; effective end is ends_at, or starts_at plus three hours when null (rdvs.md) |
| radius_m | 50 to 500, default 150 |
| note | nullable, up to 280 characters |
| status | scheduled, cancelled |
| created_at, updated_at | |

`rdvs.places`: `rdv_id` (primary key), `place_name`, `lat`, `lng`. Kept apart from `rdvs.rdvs` so a policy can hide the place of a private event from members who have not answered going or maybe. These are RDV coordinates, not member positions.

`rdvs.crews`: `rdv_id`, `crew_id`; the crews the RDV is for; primary key (rdv_id, crew_id).

`rdvs.rsvps`: `rdv_id`, `user_id`, `answer` (going, maybe, cant), `updated_at`; primary key (rdv_id, user_id).

`rdvs.arrivals`: `rdv_id`, `user_id`, `arrived_at`, `method` (live, here); primary key (rdv_id, user_id). No position is stored.

## places

`places.pins`: `id`, `dropper_id`, `label`, `note` (nullable), `address` (nullable), `lat`, `lng`, `expires_at` (24 hours after creation), `created_at`.

`places.pin_crews`: `pin_id`, `crew_id`; primary key (pin_id, crew_id).

## chat

`chat.rooms`: `id`, `kind` (crew, invite, rdv), `name`, `description` (nullable), `crew_id` (set for crew rooms, unique), `rdv_id` (set for RDV rooms, unique), `owner_id` (set only for invite rooms; crew rooms are moderated by their crew owner and RDV rooms by the host and the owners of the RDV's crews), `status` (active, closed), `created_at`.

`chat.members`: `room_id`, `user_id`, `role` (owner, member), `joined_at`, `muted` (default false), `last_read_at`; primary key (room_id, user_id). For a crew room the rows follow `crews.members`; for an RDV room they follow the going and maybe RSVPs; triggers on those tables add and remove the rows, and a returning person gets a new `joined_at`.

`chat.messages`: `id`, `room_id`, `sender_id`, `body` (1 to 1000 characters), `created_at`; deleted by a scheduled job when older than the `chat_message_ttl_days` setting (default 7).

`chat.floors`: `room_id` (primary key), `holder_id`, `lease_expires_at`; one row per room that has a speaker, cleared on release or when the lease ends. This holds no audio and no position. A change to it is broadcast on the room channel by `walkie_floor`.

`chat.push_settings` is not added in this release; the mute flag in `chat.members` is the setting the push function reads.

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
| rdvs.rdvs, rdvs.crews | members of a listed crew | the host or a crew owner, via function |
| rdvs.places | members of a listed crew, except that a private event's row is readable only by the host and members who answered going or maybe | the host, via function |
| places.pins, places.pin_crews | members of a listed crew while not expired | the dropper or an owner of a listed crew, via function |
| chat.rooms, chat.members | members of the room, and for a crew room members of the crew | functions only |
| chat.messages | members of the room, for messages created after the member joined and not older than `chat_message_ttl_days` | functions only (send, delete) |
| chat.floors | members of the room | functions only |
| rdvs.rsvps, rdvs.arrivals | members of a crew the RDV is for; the user always reads their own rows, even after leaving the crew | the user, via function (arrivals through record_arrival) |

Every policy also requires the caller's profile to be active. Realtime channel `crew:<crew_id>` is private; join requires membership in `crews.members`.

## Retention

Everything above is kept while the account exists, except chat messages, which are deleted after `chat_message_ttl_days`. Deleting the account removes sessions, session_crews, segments, the user's RSVPs and arrivals, the person's chat messages and room memberships (a room they own passes to its longest-standing member, or is deleted with none), the auth user with its email, and avatar files; RDVs the user hosted that have not ended are cancelled and keep their rows, with host_id cleared, so other members' arrivals and stats stay intact; invite records and the profile tombstone remain for the referral chain.
