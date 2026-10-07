# 0020 Movement trails

Status: accepted

## Decision

Show a short trail behind each live member and behind you, drawn along the road at road width, fading to the tail. Trails are the last 450 metres of path, kept in memory on each phone only. Details in `docs/features/movement-trails.md`.

## Rationale

The owner wants the map to read like a navigation app. A tail shows direction and recent movement without any routing.

## Consequences

- No new stored data and no route history; consistent with decision 0004 (privacy enforced server side) and the rule that positions are never stored.
- Length is by distance so it never encodes speed.
- Phones send an extra position at corners; the message budget in architecture.md still holds.
- Road matching uses map geometry already on the phone. It only works where roads are loaded.

## Revisit

If trails look wrong on freeways or in tunnels, or the corner broadcasts push the message budget.
