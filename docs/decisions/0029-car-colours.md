# 0029 Car colours

Status: accepted

## Decision

A person can choose their car colour from a fixed palette of neon colours, under Me, Car. The colour is a profile field shown to the crews they share with. The car, its trail and its halo use it; the name pill keeps the crew-colour dot. With no choice the car takes the crew colour as before.

## Rationale

Cars were told apart only by crew colour, so two members of one crew looked alike. A chosen colour makes a car recognisable and lets people make the map theirs, which suits the brand. A fixed neon palette keeps the map legible on the dark style and avoids unreadable or off-brand colours.

## Consequences

- Closes the open question in car-icons.md. `accounts.profiles.car_color` is nullable.
- The trail turns neon: a brighter, more saturated version of the car colour.
- Colour is no longer a sole cue for which crew a member is in; the crew dot in the name pill carries that.

## Revisit

If people ask for free colour choice, or the palette needs more colours.
