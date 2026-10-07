# 0019 Black and blue

Status: accepted

## Decision

The accent changes from orange to blue (fill #2F6FF2, bright marks #5EA0FF) on a blue-black base, and the map style becomes a monochrome charcoal-blue night map. Cards, secondary buttons and chips are outlined rather than filled. Markers are round badges with an outlined name pill, and a tapped member opens a floating card with a Follow button. Everything else in decision 0018 (glass for floating layers, type, spacing, the accent-sparing rules) stays; only the accent hue and the map palette change.

## Rationale

The owner was not satisfied with the orange look and pointed to a charcoal map design with outlined dark cards and a single glowing accent, asking for black and blue instead. This keeps the dark, minimal, exclusive brand and removes the warm accent that read as loud next to the map.

## Consequences

- Updates decision 0017's palette: the Waze-inspired behavior (tilted follow camera, bright-to-dark road hierarchy) stays, the navy-and-blue coloring does not.
- The Android notification color, splash background and app icon should be checked against the new palette before store submission.
- Decision 0018 wording about orange is superseded by the blue equivalents in `docs/design.md`.

## Revisit

If blue marks lack contrast on the map in daylight, or the owner wants a second accent for crews.
