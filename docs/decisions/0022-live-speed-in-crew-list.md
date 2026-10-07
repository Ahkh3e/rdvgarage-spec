# 0022 Live speed in the crew list

Status: proposed, at the owner's request; confirm before building

## Decision

The crew list on the map shows each live member's current speed in km/h, whole numbers, with a parked member shown as parked. Speed is not shown on map markers, on the member card, or in trails, and it is never stored. It travels in the same crew-only position broadcast, in memory.

## Rationale

The owner asked for it: a glanceable sense of who is moving how fast, the same social hook as the Board.

## Consequences

- Reverses part of decision 0007 ("shown after the session, never as a live number") and the matching lines in live-map.md, leaderboard.md and privacy.md. The Board is unchanged: it still shows weekly top speed after sessions, from stored segments only.
- The position broadcast payload gains `speed_kmh` (architecture.md). Phones that do not send it (older builds) show no number.
- Live speed next to a speed leaderboard raises the risk of unsafe driving. The decision 0007 stance still applies: no limits or caps in the product, with prominent disclaimers (`docs/disclaimers.md`). The wording should mention that live speed is visible to the crew.
- Not an input to the Board: the Board still uses the device's measured max per session, so showing speed live does not change what counts.

## Open questions

- Whether a person can turn off showing their speed to the crew for a session. Recommended: a per-session switch at Go live, on by default, so the live-location privacy stance (sharing is manual and per crew) is preserved.

## Revisit

After use. If it changes how people drive, remove it first.
