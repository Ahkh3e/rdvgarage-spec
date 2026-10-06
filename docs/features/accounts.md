# Accounts

Owning the user database (decision 0008) means we build user creation and management. This spec defines the minimum for 0.0.1 and what waits.

## User record

- id (internal, stable), handle (unique), avatar, created_at
- email and password credential held by Supabase Auth (decision 0012)
- invited_by (user id), share invite id used
- status: active, suspended, deleted
- no email or phone shown to other users

## Invite record

- id, inviter (user id), created_at, expires_at, max_signups, signups_used
- status: active, expired, full, revoked, disabled
- revoked_by: inviter, operator, or suspension
- joined users are linked by invite id; records are kept while the inviter account or its tombstone exists

## Lifecycle in 0.0.1

| Operation | Behavior |
|---|---|
| Create | Register via a valid invite link; handle and avatar set during onboarding; invited_by stored |
| Sign in | Email and password, then a session token; refresh and expiry |
| Sign out | Revoke the current session |
| Sessions | List and revoke other devices (stretch) |
| Edit profile | Change avatar; change handle at most once per 30 days |
| Share invite | Creates a new invite link active for 24 hours; the user can revoke it earlier |
| Delete account | In-app, required by App Review. Removes profile and session summaries; leaves crews; referral chain keeps a tombstone node so the chain stays intact. If the user owns a crew, ownership passes to its longest-standing member; if the crew has no other members it is dissolved and its link stops working. Leaderboard entries are removed. All of the user's active invites are revoked. Invite records are kept for the referral chain |
| Recover access | Password reset by email; losing both the password and the email needs operator help |

Suspending a user also revokes all their active invites with reason suspension. Reinstating the user does not restore them; they create new invites. Suspending a crew owner transfers ownership the same way deletion does. Operator hard delete follows the full deletion path: ownership transfer, leaderboard purge, deletion of the auth user.

## Operator tools in 0.0.1

No admin UI. A CLI or script for the operator to:

- view a user and their referral chain
- suspend or restore a user
- hard delete on request
- list a user's invites and disable any invite

Suspended users are signed out everywhere and removed from live maps.

## Rules

- Handles: 3-20 chars, lowercase letters, numbers, underscore; reserved words blocked.
- Suspension and deletion emit events so other modules (crews, live location, leaderboard) clean up without Accounts knowing them.
- Passwords are hashed by Supabase Auth and never stored or logged by us. Minimum length is to be decided; no composition rules.
- All personal data is exportable and deletable.

## Platform notes

- In-app Delete account in Settings with confirmation, on both platforms.
- iPhone: session stored in Keychain. Android: session stored in the Keystore-backed secure store.
- Registration form and password reset per decision 0012.

## Not in 0.0.1

Admin UI, report and block users, email or phone verification, two-factor, account merge, data export UI.

## Open

- Whether a deleted user's handle can be reused
- Signup limit per invite link
