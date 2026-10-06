# 0008 Self-owned user database

Status: accepted, amended by decision 0010 (managed host allowed; data ownership unchanged)

## Decision

RDV Garage owns its user data. Users register with us and live in a Postgres database we control and can export. A managed host, including its auth service, is allowed as long as it is not the only copy of the data (decision 0010).

## Rationale

Users, referral chains, crews, and locations are the product's core and its privacy promise (decision 0004). Owning the data keeps control over retention and access, and avoids lock-in.

## Consequences

- We own the schema, the data, deletion, and backups. A managed host can run the service.
- Apple App Review requires account deletion from within the app.
- The credential method is open. Sign in with Apple can still be a credential, stored against our own user record.
- The Accounts module hides the user service behind an interface.

## Open

- Credential method
- Hosting and region: resolved in decision 0010 (Supabase, Canada Central)
