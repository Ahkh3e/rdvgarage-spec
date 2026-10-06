# 0008 Self-owned user database

Status: accepted

## Decision

RDV Garage owns and hosts its user database. Users register with us; we do not delegate accounts to a third-party auth or backend-as-a-service provider.

## Rationale

Users, referral chains, crews, and locations are the product's core and its privacy promise (decision 0004). Owning the data keeps control over retention and access, and avoids lock-in.

## Consequences

- We run the user service, storage, and security (password or credential handling, sessions, deletion, backups).
- Apple App Review requires account deletion from within the app.
- The credential method is open. Sign in with Apple can still be a credential, stored against our own user record.
- The Accounts module hides the user service behind an interface.

## Open

- Credential method
- Hosting and region: resolved in decision 0010 (Supabase, Canada Central)
