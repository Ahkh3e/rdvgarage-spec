# Accounts

Owning the user data (decision 0008) means we build user creation and management. Accounts are email and password (decision 0013), created only through an invite. This spec defines the minimum for 0.0.1 and what waits.

## User record

- id (internal, stable), handle (unique), avatar, created_at
- invited_by (user id), invite id used
- terms version accepted and when
- status: active, suspended, deleted
- email and password credential held by Supabase Auth; the email is never shown to other users and no phone or real name is collected

## Invite record

- id, inviter (user id), code, created_at, expires_at
- status: active, revoked, disabled (expired is derived from expires_at)
- revoked_by: inviter, operator, or suspension
- joined users are linked by invite id; records are kept while the inviter account or its tombstone exists

## Account form

Handle, email, password, optional avatar, plus acceptance of the disclaimers and confirmation of being 18 or older. Password minimum is 8 characters, no composition rules. Email is confirmed before first sign-in.

## Lifecycle in 0.0.1

| Operation | Behavior |
|---|---|
| Create | Open a valid invite, fill the account form, confirm the email, then sign in; invited_by stored. In test mode (development and test projects only) the server confirms the account automatically and the confirmation step is skipped; the Resend button and the 24-hour expiry apply only in production mode |
| Sign in | Email and password, then a session; the session stays until sign out or revoke |
| Forgot password | Reset link sent by email; sets a new password; other devices are signed out right after |
| Change password | Me, Change password; requires the current password; other devices are signed out |
| Resend confirmation | The Confirm email screen can resend the email. Unconfirmed accounts are removed after 24 hours, freeing the handle, so a mistyped email is fixed by starting over |
| Devices | Settings, Devices lists signed-in devices with platform and last seen; any can be revoked, or sign out everywhere |
| Sign out | Revoke the current session |
| Edit profile | Change avatar; change handle at most once per 30 days |
| Share invite | Creates a new invite link active for 24 hours; the user can revoke it earlier |
| Delete account | In-app, required by App Review. Removes profile details, email, sessions, and session summaries; leaves crews; referral chain keeps a tombstone node so the chain stays intact. If the user owns a crew, ownership passes to its longest-standing member; if the crew has no other members it is dissolved and its link stops working. Leaderboard entries are removed. All the user's active invites are revoked. Invite records are kept for the referral chain |

Suspending a user bans their auth user so they are signed out everywhere and cannot sign back in, and revokes all their active invites with reason suspension. Reinstating the user does not restore invites; they create new ones. Suspending a crew owner transfers ownership the same way deletion does. Operator hard delete follows the full deletion path: ownership transfer, leaderboard purge, deletion of the auth user.

## Profile

- Handle: 3-20 characters, lowercase letters, numbers, underscore; unique; reserved words blocked.
- Avatar: optional; chosen from the photo library or camera; cropped square; resized on the device to 512 pixels and compressed before upload; stored privately and shown only to people who share a crew.
- No display name, bio, or contact details in 0.0.1.

## Operator tools in 0.0.1

No admin UI. The operator toolkit on the server (`docs/ops.md`) is the only admin surface. It lets the operator:

- view a user and their referral chain
- suspend or restore a user
- hard delete on request
- list a user's invites and disable any invite
- create and delete users and crews, including synthetic ones, and simulate activity for testing

Suspended users are signed out everywhere and removed from live maps.

## Rules

- Suspension and deletion emit events so other modules (crews, live location, leaderboard) clean up without accounts knowing them.
- We never see, store, or log passwords; Supabase Auth hashes them.
- All personal data is deletable. A data export screen is not in 0.0.1.

## Platform notes

- In-app Delete account in Settings with confirmation, on both platforms.
- iPhone: session stored in Keychain. Android: session stored in the Keystore-backed secure store.
- Password managers and autofill are supported by using standard email and password fields.

## Not in 0.0.1

Change email, admin UI, report and block users, passkeys, two-factor, account merge, data export UI.

## Open

- Whether a deleted user's handle can be reused
