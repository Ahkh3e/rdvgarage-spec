# 0013 Email and password accounts

Status: accepted

## Decision

Users create an account with a form: handle, email, password, and a valid invite code, plus acceptance of the disclaimers. Sign-in is email and password. Forgotten passwords are recovered by email. No Sign in with Apple, no social logins. Supersedes decision 0012 (link-based accounts).

## Rationale

The owner wants familiar accounts with real recovery, so a lost phone does not mean a lost account. Email recovery avoids operator-assisted recovery, which would need identity checks the operator cannot reliably make.

## Liability-minded choices

- Passwords are handled entirely by Supabase Auth, which hashes them. We never see, store, or log a password.
- We collect only what is needed: handle, email, and optional avatar. The email is never shown to other users, and no phone number or real name is collected.
- Email confirmation is required before first sign-in, so an email really belongs to the account holder and recovery goes to the right person.
- Account deletion removes the account and personal data (decision 0008, accounts.md).
- Terms acceptance, the 18 or older confirmation, and the version accepted are stored with the account.
- This lowers credential-handling risk because we never touch passwords. It does add breach surface compared with decision 0012: an email address and a password hash per user now exist in Supabase Auth. Mitigations are no sharing of email, minimal fields, Supabase's managed security, and deletion on request. Legal risk is not removed; a privacy policy, terms of use, and a lawyer's review are still needed (`docs/disclaimers.md`, `docs/store-submission.md`).

## Consequences

- Email delivery is required for confirmation and password reset. Supabase's built-in sender is too limited for real use, so a free-tier SMTP provider and a sending domain are needed before real users. The owner has deferred choosing them; development can use the built-in sender.
- Public signup is disabled. Accounts are created only by the `register` function after the invite is validated.
- The sign-in link and recovery-link mechanism from decision 0012 is removed. Settings still lists signed-in devices and can revoke any of them.
- Minimum password length is 8 characters, with no composition rules.
- Account deletion remains in-app (App Review).
- Adding any social login later would trigger the Sign in with Apple requirement on iPhone.

## Revisit

If email delivery or sign-up friction hurts onboarding; passkeys are a later option.
