# API

The callable surface for 0.0.1. The app reaches all of it through the Backend contract in the core package. SQL functions run in Postgres and are called over RPC; Edge Functions run server side with the service role. Every call needs a signed-in active profile unless marked anonymous.

## Anonymous

| Name | Type | Input | Result |
|---|---|---|---|
| check_invite | SQL | code | status only: valid, expired, revoked, disabled. No inviter or user data. Rate limited |
| register | Edge | invite code, handle, avatar (optional), terms version | session, or error |
| redeem-device-link | Edge | code | session, or error |

## Accounts

| Name | Type | Notes |
|---|---|---|
| update_profile | SQL | handle (once per 30 days) and avatar path |
| create_device_link | SQL | returns a code once; 24 hours, single use |
| revoke_device_link | SQL | creator only, unused links |
| list_sessions | Edge | the user's signed-in devices |
| revoke_session | Edge | any of the user's sessions |
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
| list_my_crews | SQL | crews, members, and who is live now |

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

All functions return a stable error code the app maps to a message: invalid_invite, expired_invite, revoked_invite, handle_taken, handle_invalid, handle_cooldown, not_a_member, not_owner, link_used, link_expired, session_not_found, suspended, rate_limited.

## Operator only (service role, operator machine)

view user and referral chain, suspend, restore, hard delete, list and disable invites, issue recovery sign-in link.
