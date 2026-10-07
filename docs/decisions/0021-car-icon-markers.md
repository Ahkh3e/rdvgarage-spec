# 0021 Car icon markers

Status: accepted

## Decision

Members are shown on the map as a chosen minimal racecar icon (top-down, rotating with heading) on a round badge ringed in the crew tint, with a name pill below. People pick one of eight generic models in Me. The icon is a profile field visible to shared crews. Photos stay in lists, the member card and the Board. Details in `docs/features/car-icons.md`.

## Rationale

The owner wants a map that feels like cars, with personal choice. Crew tint on the ring keeps crews distinguishable without the earlier per-crew shapes, which a car silhouette replaces.

## Consequences

- Supersedes the shaped markers in design.md (decision 0002 context): crews are told apart by ring color and the name pill's dot, and by the crew name in lists, not by marker shape.
- `accounts.profiles` gains `car_icon` (data-model.md, api.md).
- Icons must carry no brand or trademark marks.

## Revisit

If rings of six tints are not enough to tell crews apart on a busy map.
