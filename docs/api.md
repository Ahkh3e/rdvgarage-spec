# API

The callable surface for 0.0.1. The app reaches all of it through the Backend contract in the core package. SQL functions run in Postgres and are called over RPC; Edge Functions run server side with the service role. Every call needs a signed-in active profile unless marked anonymous. Sign-in, password reset, and password change use Supabase Auth directly through the Backend contract.

## Anonymous

Both anonymous calls are rate limited per network address and per code, and a code is locked after repeated failed attempts. Invite codes are random and long enough to be unguessable: 12 characters from a 32-character alphabet.

| Name | Type | Input | Result |
|---|---|---|---|
| check_invite | SQL | code | status only: valid, expired, revoked, disabled. No inviter or user data |
| register | Edge | invite code, handle, email, password, avatar (optional), terms version, age confirmation | account created and confirmation email sent, or error |

## Accounts

| Name | Type | Notes |
|---|---|---|
| update_profile | SQL | handle (once per 30 days) and avatar path |
| list_sessions | Edge | the user's signed-in devices |
| revoke_session | Edge | any of the user's sessions, or all |
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
| remove_member | SQL | owner only |
| transfer_ownership | SQL | owner only |
| regenerate_crew_link | SQL | owner only; old link stops working |
| delete_crew | SQL | owner only |
| list_my_crews | SQL | crews, members, who is live now, and which crews are selected |
| set_selected_crews | SQL | the user's selected crews, synced across their devices |

## Live

| Name | Type | Notes |
|---|---|---|
| start_session | SQL | crew ids; returns session id |
| checkpoint_session | SQL | session id, week segment values, updates last_seen_at |
| end_session | SQL | sets ended_at |

Positions are not an API call: they go over Realtime Broadcast on `crew:<crew_id>` channels.

## Leaderboard

| Name | Type | Notes |
|---|---|---|
| weekly_top_speed | SQL | crew id, week start; current week by default; rows of rank, handle, avatar, top speed, day set |

## Errors

All functions return a stable error code the app maps to a message: invalid_invite, expired_invite, revoked_invite, handle_taken, handle_invalid, handle_cooldown, email_invalid, email_taken, password_too_short, terms_required, age_confirmation_required, invalid_crew_link, not_a_member, not_owner, owner_must_transfer, session_not_found, suspended, rate_limited.

An email already in use returns a generic registration failure to the caller instead of confirming that the email exists; `email_taken` is for operator logs only.

## Operator only (service role, operator machine)

view user and referral chain, suspend, restore, hard delete, list and disable invites.
