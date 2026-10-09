# API

The callable surface for 0.0.1. The app reaches all of it through the Backend contract in the core package. SQL functions run in Postgres and are called over RPC; Edge Functions run server side with the service role. Every call needs a signed-in active profile unless marked anonymous. Sign-in, password reset, and password change use Supabase Auth directly through the Backend contract.

## Anonymous

Both anonymous calls are rate limited per network address and per code, and a code is locked after repeated failed attempts. Invite codes are random and long enough to be unguessable: 12 characters from a 32-character alphabet.

| Name | Type | Input | Result |
|---|---|---|---|
| check_invite | SQL | code | status only: valid, expired, revoked, disabled, invalid. No inviter or user data |
| register | Edge | invite code, handle, email, password, terms version, age confirmation. The avatar is added after the first sign-in from Me, Edit profile | confirmation email sent, or error. In test mode (server setting, honored only in development and test projects) the account is created confirmed, no email is sent, and the result is `confirmed`; otherwise the result is `check_email` |

On a valid invite and handle, `register` always answers the same way ("check your email") whether or not the email is already registered. If the email already has an account, the email that is sent says so and links to sign in and reset. Attempts count against the rate limit either way. Test mode is the exception: it returns `email_in_use` for a used email, since test projects are private. Invite and handle errors stay distinct because they reveal nothing about emails.

## Accounts

| Name | Type | Notes |
|---|---|---|
| update_profile | SQL | handle (once per 30 days), avatar path, car icon and car colour |
| list_sessions | SQL | the user's signed-in devices (reads the auth sessions table; the caller's own rows only) |
| revoke_session | SQL | one session |
| revoke_other_sessions | SQL | every session except the caller's; the app calls it right after a successful password reset |
| change_password | Edge | verifies the current password, sets the new one, signs the caller in again, returns that fresh session, and revokes every other session |
| delete-account | Edge | full deletion path in architecture.md |

## Referral

| Name | Type | Notes |
|---|---|---|
| create_invite | SQL | returns a new 24-hour invite; no limits |
| revoke_invite | SQL | inviter only |
| list_my_invites | SQL | each invite with status and who joined |

## Crews

| Name | Type | Notes |
|---|---|---|
| create_crew | SQL | name, description, avatar; caller becomes owner |
| join_crew | SQL | crew link code; caller must be an active member |
| leave_crew | SQL | an owner must transfer or delete first |
| remove_member | SQL | owner or admin; an admin cannot remove the owner or another admin |
| promote_admin, demote_admin | SQL | crew id and user id; owner only |
| set_voice_access | SQL | crew id, user id, allowed; owner or admin, never for yourself, and only for an active member of an active crew. Turning voice off for a member applies to every walkie channel of that crew's rooms at once and calls `walkie_kick` so the member reconnects listen-only; text chat is unaffected |
| transfer_ownership | SQL | owner only |
| regenerate_crew_link | SQL | owner only; old link stops working |
| delete_crew | SQL | owner only |
| list_my_crews | SQL | crews, members, who is live now, and which crews are selected |
| set_selected_crews | SQL | the user's selected crews, synced across their devices |

## Live

| Name | Type | Notes |
|---|---|---|
| start_session | SQL | crew ids; returns session id |
| checkpoint_session | SQL | session id and the values for the current week segment, cumulative and restarted from zero by the app when the Toronto week changes; updates last_seen_at. An optional week start lets the app write the previous week's final values just after the week rolls over; only the current week or the one before is accepted. NaN and Infinity are rejected |
| end_session | SQL | sets ended_at |

Positions are not an API call: they go over Realtime Broadcast on `crew:<crew_id>` channels.

## RDVs

| Name | Type | Notes |
|---|---|---|
| create_rdv | SQL | title, kind, place, area name (required for a private event and never the place name or street, since it is the only place text shown before RSVP; the host types it), start, optional end, note, crew ids (all the caller's), radius; caller becomes host; start must not be in the past |
| update_rdv | SQL | host only; may change the crews (still all the caller's); an unchanged start is accepted even if now past; notifies people who answered going or maybe when place or time changes |
| cancel_rdv | SQL | host or an owner or admin of a listed crew; a non-host gets `not_host` on edit |
| list_rdvs | SQL | crew ids; upcoming and recent (ended within the last 14 days; a cancelled RDV only until its window ends), with counts and the caller's answer; counts include only current crew members; private event places withheld until going or maybe |
| list_rsvps | SQL | rdv id; members per answer, for the detail screen; members of a listed crew only |
| set_rsvp | SQL | rdv id and answer; allowed until the RDV ends; a cancelled RDV returns `rdv_closed` |
| record_arrival | Edge | rdv id and one position reading; checks radius and attendance window, stores only the arrival, discards the position. A live member's on-device arrival uses the same call. Returns `{recorded, already}`; `outside_radius`, `outside_window` and `rdv_closed` are 409, `rdv_not_found` is 404 (also returned to a member who cannot see a private place) |

## Places

| Name | Type | Notes |
|---|---|---|
| drop_pin | SQL | label, note, coordinates, optional address, crew ids (all the caller's); expires after 24 hours |
| remove_pin | SQL | dropper or an owner or admin of a listed crew |
| list_pins | SQL | crew ids; unexpired pins |
| search_places | Edge | text and a coarse bias point (about 1 km); calls the geocoder from the server and returns results; stores nothing; rate limited per account |

Nearby is not an API call; it is read from the map tiles on the device (docs/features/places.md).

## Chat

| Name | Type | Notes |
|---|---|---|
| create_room | SQL | name, optional description, member ids (each shares a crew with the caller); caller becomes owner |
| list_rooms | SQL | the caller's rooms with last message, unread count, muted flag |
| send_message | SQL | room id and body of 1 to 1000 characters; members only; rate limited per account |
| list_messages | SQL | room id and an optional before marker; messages after the caller joined and still kept |
| delete_message | SQL | the sender, the owner of an invite room, an owner or admin of the crew for a crew room, or the host or an owner or admin of one of the RDV's crews for an RDV room |
| add_room_member, remove_room_member | SQL | add: owner of an invite room, and the new member must share a crew with the caller. Remove: owner of an invite room, or the host or an owner or admin of one of the RDV's crews for an RDV room, which blocks the person. Crew rooms follow the crew |
| leave_room, delete_room, transfer_room | SQL | members leave an invite room; its owner deletes or transfers it; crew and RDV rooms cannot be left or deleted directly (a crew room goes with its crew, an RDV room closes with its RDV) |
| mark_read, set_room_muted | SQL | the caller's own marker and mute flag |
| open_rdv_room | SQL | rdv id; host only, before the RDV ends; creates the RDV's room for members who answered going or maybe |

Messages arrive live over Realtime Broadcast on each person's private `inbox:<user_id>` channel, which only that person can join. `delete_crew` also deletes the crew's room, members, messages and floor and calls `walkie_kick` for anyone connected.

## Walkie-talkie

| Name | Type | Notes |
|---|---|---|
| walkie_token | Edge | room id; checks membership and returns a LiveKit token for that room that lets the person listen and publish, valid 5 minutes, renewed while in the room; the participant id is a keyed hash of the person and the room; also returns a roster of participant ids to members, for current members only; rate limited |
| walkie_kick | Edge | service only; called by a database trigger when a member is removed or a room is deleted or closed; removes that participant from the audio service |
| close_rdv_rooms | SQL | service only; run by a scheduled job; closes RDV rooms whose RDV has ended or been cancelled and calls `walkie_kick` for anyone connected |

## Leaderboard

| Name | Type | Notes |
|---|---|---|
| weekly_top_speed | SQL | crew id, week start; current week by default; rows of rank, handle, avatar, top speed, day set |

## Errors

All functions return a stable error code the app maps to a message: invalid_invite, expired_invite, revoked_invite, handle_taken, handle_invalid, handle_cooldown, email_invalid, email_in_use (test mode only), invalid_checkpoint, password_too_short, wrong_password, email_unconfirmed, reset_link_invalid, terms_required, age_confirmation_required, invalid_crew_link, not_a_member, not_owner, pin_not_found, pin_label_invalid, pin_note_invalid, pin_place_invalid, pin_crew_required, invalid_query, search_unavailable, not_host, rdv_area_required, rdv_title_invalid, rdv_kind_invalid, rdv_place_invalid, rdv_note_invalid, rdv_radius_invalid, rdv_time_invalid, rdv_end_invalid, rdv_crew_required, rdv_answer_invalid, rdv_method_invalid, voice_revoked, not_moderator, cannot_moderate_admin, room_not_found, not_room_member, not_room_owner, room_name_invalid, room_description_invalid, message_invalid, no_shared_crew, room_closed, walkie_unavailable, rdv_not_found, rdv_in_past, rdv_closed, outside_radius, outside_window, owner_must_transfer, session_not_found, suspended, rate_limited.

A registration for an email already in use does not return an error; see the note under Anonymous.

## Operator only (service role, operator server)

view user and referral chain, suspend, restore, hard delete, list and disable invites, create and delete users and crews, and simulate activity. See `docs/ops.md`; none of this is exposed as an app or network endpoint.
