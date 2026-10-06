# 0012 Email one-time code sign-in

Status: accepted

## Decision

Users sign in with an email address and a one-time code. No passwords, no Sign in with Apple, no social logins.

## Rationale

Works identically on iPhone and Android. Needs no paid SMS provider. Offering no third-party login means Apple's Sign in with Apple requirement does not apply. No password storage or reset flow to build.

## Consequences

- Supabase's built-in email sender is too limited for real use; a free-tier SMTP provider is needed.
- Emails are never shown to other users.
- Losing access to an email needs operator help in 0.0.1.
- Account deletion remains in-app (App Review).
- Adding a social login later would trigger the Sign in with Apple requirement on iPhone.

## Revisit

If email delivery or sign-in friction hurts onboarding; passkeys are a later option.
