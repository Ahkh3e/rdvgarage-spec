# 0012 Link-based accounts: no password, no email

Status: superseded by decision 0013 (email and password accounts). Reason: the owner wanted familiar accounts with email recovery. The privacy benefit of holding no email or password was traded away for recovery and familiarity.

## Decision

Accounts have no password and no email. A person joins through a 24-hour invite link, chooses a handle, and is signed in on that device. To use the account on another device, a signed-in device generates a 24-hour sign-in link. No Sign in with Apple, no social logins.

## Rationale

Matches how the product already works: everything is link-based and referral-only. Nothing for users to remember, nothing to leak or reset, no email provider to run, and no personal contact data to store. Offering no third-party login means Apple's Sign in with Apple requirement does not apply.

## Consequences

- Accounts are bound to devices. Losing every signed-in device with no sign-in link means losing the account, unless the operator issues a recovery sign-in link after confirming identity out of band. No recovery is guaranteed; the disclaimers say so (`docs/disclaimers.md`).
- A sign-in link grants full account access, so it is single-use, expires after 24 hours, can be revoked by its creator, and is shown with a warning.
- Sessions are listed and can be revoked from Settings, since that is the main account-security tool.
- No SMTP provider, email confirmation, or password reset is needed. The earlier SMTP and domain questions disappear, except for the domain used for links.
- Public signup is disabled. Accounts are created only by the `register` function after the invite is validated.
- Account deletion remains in-app (App Review).
- Adding any social login later would trigger the Sign in with Apple requirement on iPhone.

## Revisit

If device-bound accounts cause too many lost accounts; an optional recovery email or passkeys are later options.
