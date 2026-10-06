# 0006 No ETA

Status: accepted

## Decision

The app does not compute or show ETAs. No feature depends on routing or travel-time estimates.

## Rationale

The app is not a navigation app. ETAs require a routing provider, which would send crew locations to a third party and conflict with decision 0004. The live map already shows where everyone is.

## Consequences

- Removes live ETA from the original concept and from Phase 1.
- Tapping an RDV hands off to the user's own maps app for directions.
- Members' live positions on the crew map are how crews see who is close.

## Revisit

If crews ask for arrival info and an on-device approach is viable.
