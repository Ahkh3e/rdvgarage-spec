# 0012 Email and password registration

Status: accepted

## Decision

Users register directly with RDV Garage using a handle, an email address, a password, and a valid invite code. Sign-in is email and password. No Sign in with Apple, no social logins.

## Rationale

Registration is with us, not through a third-party identity. It works identically on iPhone and Android, and needs no paid SMS provider. Offering no third-party login means Apple's Sign in with Apple requirement does not apply.

## Consequences

- Public signup is disabled in Supabase Auth. Accounts are created only by the `register` server function after the invite is validated, so uninvited users cannot create auth accounts.
- Passwords are hashed by Supabase Auth and never stored or logged by us. Minimum length is to be decided; no composition rules.
- Email confirmation and password reset both send email. Supabase's built-in sender is too limited for real use, so a free-tier SMTP provider is a 0.0.1 requirement.
- Emails are never shown to other users.
- Losing both the password and the email needs operator help.
- Account deletion remains in-app (App Review).
- Adding a social login later would trigger the Sign in with Apple requirement on iPhone.

## Revisit

If password reset support load or sign-up friction hurts onboarding; passkeys are a later option.
