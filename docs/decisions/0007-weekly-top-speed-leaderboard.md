# 0007 Weekly top speed leaderboard in 0.0.1

Status: accepted

## Decision

Release 0.0.1 includes a per-crew leaderboard of each member's top speed this week. This reverses the earlier stats note against speed-based stats.

## Rationale

It is a simple, social, car-culture hook that makes the first release worth opening weekly.

## Design and disclaimers

- Speed is recorded only during a live session, as the device measured it. There are no speed limits, caps, or plausibility checks.
- Shown after the session, never as a live number on the map.
- Crew-only visibility.
- Safety is handled by prominent disclaimers (`docs/disclaimers.md`), not by rules in the product.

## Risk

A rank on speed can encourage unsafe driving, and recorded speeds can be wrong or spoofed. Accepted by the owner; disclaimers carry the message.

## Revisit

After 0.0.1 usage.
