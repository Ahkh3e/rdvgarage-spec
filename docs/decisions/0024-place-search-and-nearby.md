# 0024 Place search and nearby places

Status: accepted

## Decision

- Searching for an address, or a place or store by name, sends only the typed text to a geocoder, plus a coarse bias point rounded to about 1 km. It never sends a member's precise position, a crew, or a handle.
- Nearby places (fuel, food, parking and so on) are read on the device from the points of interest already in the map tiles. No third party is asked.
- The geocoder is Photon on OpenStreetMap data, self-hosted with the operator server when it is stood up, and the public Photon service until then. It is replaceable behind one `Geocoder` contract in the core package.
- Tapping a place or pin opens Waze by default, through the maps handoff (maps-handoff.md). This is a handoff, not navigation (decision 0006).

## Rationale

Decision 0004 requires any third party that would receive member locations to be vetted. Typed text with a coarse bias reveals far less than a position, and reading nearby places from the tiles keeps the precise position on the device. Photon uses the same OpenStreetMap data as the tiles (decision 0017), needs no key, and can be self-hosted later like the tiles.

## Consequences

- The tile style must include the points-of-interest layer.
- The public Photon service is a free community service with no guarantee; the self-host question is tracked with the tiles decision.
- Search finds stores and other named places only if OpenStreetMap lists them; a business missing from it will not be found, and opening hours and ratings are not available.

## Revisit

If search quality is poor for Toronto, or a vetted provider that sees only text is needed.
