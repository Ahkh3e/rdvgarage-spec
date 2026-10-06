# Accounts

Owning the user database (decision 0008) means we build user creation and management. This spec defines the minimum for 0.0.1 and what waits.

## User record

- id (internal, stable), handle (unique), avatar, created_at
- credential reference (method open, see decision 0008)
- invited_by (user id), invite link id used
- status: active, suspended, deleted
- no email or phone shown to other users

## Lifecycle in 0.0.1

| Operation | Behavior |
|---|---|
| Create | Register via a valid invite link; handle and avatar set during onboarding; invited_by stored |
| Sign in | Credential check, then a session token; refresh and expiry |
| Sign out | Revoke the current session |
| Sessions | List and revoke other devices (stretch) |
| Edit profile | Change avatar; change handle at most once per 30 days |
| Regenerate invite link | Old link dies immediately |
| Delete account | In-app, required by App Review. Removes profile and session summaries; leaves crews; referral chain keeps a tombstone node so the chain stays intact. If the user owns a crew, ownership passes to its longest-standing member; if the crew has no other members it is dissolved and its link stops working. Leaderboard entries are removed |
| Recover access | Depends on credential method; Sign in with Apple needs none |

## Operator tools in 0.0.1

No admin UI. A CLI or script for the operator to:

- view a user and their referral chain
- suspend or restore a user
- hard delete on request

Suspended users are signed out everywhere and removed from live maps.

## Rules

- Handles: 3-20 chars, lowercase letters, numbers, underscore; reserved words blocked.
- Suspension and deletion emit events so other modules (crews, live location, leaderboard) clean up without Accounts knowing them.
- Passwords, if used, are hashed with a modern algorithm and never logged.
- All personal data is exportable and deletable.

## iOS

- In-app Delete account in Settings with confirmation.
- Sessions stored in Keychain.
- Credential flow per decision 0008.

## Not in 0.0.1

Admin UI, report and block users, email or phone verification, two-factor, account merge, data export UI.

## Open

- Credential method
- Whether a deleted user's handle can be reused
- Rate limits on registration per invite link
