# 0010 Supabase and free SaaS for 0.0.1

Status: accepted, amended by decisions 0011 (cross-platform client), 0013 (email and password accounts), and 0015 (operator server for tooling and scheduled jobs)

## Decision

Run 0.0.1 on free SaaS tiers: Supabase (Canada region) for Postgres, Auth, Realtime and Storage, Cloudflare Pages for invite pages, GitHub and EAS Build for code and builds. react-native-maps for maps.

## Rationale

Minimal operations and no cost for a first release. Postgres Row Level Security enforces crew-only location (decision 0004) in the database.

## Relation to 0008

Amends decision 0008, which is updated to allow a managed host. Data ownership is unchanged. Users live in our own Postgres on Supabase, which is exportable standard Postgres. We use a managed host, not a closed identity provider that holds the only copy.

## Consequences

- No application servers to run; logic lives in Postgres functions and Edge Functions. The owner's hosted server runs only operator tooling and scheduled jobs (decision 0015).
- Free-tier limits shape the design (realtime messages, pause on inactivity, no backups); see `docs/architecture.md`.
- A Backend wrapper in the Core package keeps the provider replaceable.
- Hosting and region are resolved: Canada (Central).

## Revisit

When usage exceeds free limits or a feature needs capabilities the free tier lacks.
