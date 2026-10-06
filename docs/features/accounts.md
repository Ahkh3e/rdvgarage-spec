# Accounts

Owning the user data (decision 0008) means we build user creation and management. Accounts have no password and no email (decision 0012). This spec defines the minimum for 0.0.1 and what waits.

## User record

- id (internal, stable), handle (unique), avatar, created_at
- invited_by (user id), invite id used
- terms version accepted and when
- status: active, suspended, deleted
- no email, phone, or password exists

## Invite record

- id, inviter (user id), code, created_at, expires_at
- status: active, revoked, disabled (expired is derived from expires_at)
- revoked_by: inviter, operator, or suspension
- joined users are linked by invite id; records are kept while the inviter account or its tombstone exists

## Sign-in link record

- id, user id, code, created_at, expires_at, used_at, revoked_at
- single use, 24 hours

## Lifecycle in 0.0.1

| Operation | Behavior |
|---|---|
| Create | Register through a valid invite link; accept the disclaimers and confirm 18 or older; choose handle and optional avatar; invited_by stored; signed in on that device |
| Sign in on another device | A signed-in device creates a sign-in link or QR (24 hours, single use); opening it on the new device signs that device in |
| Sessions | Settings, Devices lists signed-in devices with platform and last seen; any can be revoked |
| Sign out | Revoke the current session |
| Edit profile | Change avatar; change handle at most once per 30 days |
| Share invite | Creates a new invite link active for 24 hours; the user can revoke it earlier |
| Delete account | In-app, required by App Review. Removes profile details, sessions, and session summaries; leaves crews; referral chain keeps a tombstone node so the chain stays intact. If the user owns a crew, ownership passes to its longest-standing member; if the crew has no other members it is dissolved and its link stops working. Leaderboard entries are removed. All the user's active invites and sign-in links are revoked. Invite records are kept for the referral chain |
| Recover access | If every device is lost and no sign-in link exists, the operator can issue a recovery sign-in link after confirming identity out of band. Not guaranteed |

Suspending a user also revokes all their active invites with reason suspension and signs them out everywhere. Reinstating the user does not restore invites; they create new ones. Suspending a crew owner transfers ownership the same way deletion does. Operator hard delete follows the full deletion path: ownership transfer, leaderboard purge, deletion of the auth user.

## Profile

- Handle: 3-20 characters, lowercase letters, numbers, underscore; unique; reserved words blocked.
- Avatar: optional; chosen from the photo library or camera; cropped square; resized on the device to 512 pixels and compressed before upload; stored privately and shown only to people who share a crew.
- No display name, bio, or contact details in 0.0.1.

## Operator tools in 0.0.1

No admin UI. A CLI or script for the operator to:

- view a user and their referral chain
- suspend or restore a user
- hard delete on request
- list a user's invites and disable any invite
- issue a recovery sign-in link

Suspended users are signed out everywhere and removed from live maps.

## Rules

- Suspension and deletion emit events so other modules (crews, live location, leaderboard) clean up without accounts knowing them.
- A sign-in link grants full account access. It is shown with a warning, is single use, and expires after 24 hours.
- All personal data is deletable. A data export screen is not in 0.0.1.

## Platform notes

- In-app Delete account in Settings with confirmation, on both platforms.
- iPhone: session stored in Keychain. Android: session stored in the Keystore-backed secure store.
- QR scanning uses the camera; the link also works without scanning.

## Not in 0.0.1

Admin UI, report and block users, optional recovery email, passkeys, two-factor, account merge, data export UI.

## Open

- Whether a deleted user's handle can be reused
