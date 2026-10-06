# 0004 Location shared only within crews

Status: accepted

## Decision

A user's live location is visible only to members of the crews they choose. Never public, never to non-members.

## Rationale

Trust and safety for a private scene. Core privacy promise of the product.

## Consequences

Any third party that would receive member locations (routing, analytics, maps) must be vetted. The ETA source is blocked on this; see `docs/product.md`.
