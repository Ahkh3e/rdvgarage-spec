# 0014 Test mode auto-confirms accounts

Status: accepted

## Decision

For testing playthroughs, the `register` function has a server-side test mode that creates accounts already confirmed and sends no email. It is honored only in projects named `development` or `test`, and ignored in `production`.

## Rationale

Playthroughs of the whole app should not wait on choosing an SMTP provider (deferred by the owner). Production still needs verified emails for recovery (decision 0013).

## Consequences

- No email is needed to test registration, crews, live location, or the leaderboard.
- Password reset, resend confirmation, and unconfirmed-account expiry are tested only in production mode.
- In test mode a used email returns an error; anti-enumeration holds in production only.
- The release checklist verifies the setting is off in production.
- The app learns the mode from the `register` response, never from a client setting.

## Revisit

When the SMTP provider is chosen, test mode can stay for fast local testing or be removed.
