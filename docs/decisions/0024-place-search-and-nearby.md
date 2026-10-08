# 0024 Place search and nearby places

Status: accepted

## Decision

- Searching for an address, or a place or store by name, sends only the typed text to a geocoder, plus a coarse bias point rounded to about 1 km. It never sends a member's precise position, a crew, or a handle.
- Nearby places (fuel, food, parking and so on) are read on the device from the points of interest already in the map tiles. No third party is asked.
- Search goes through a server function (`search_places`) that forwards only the text and the bias point, so the geocoder never sees a member's IP address or account. The app waits for a short pause in typing and at least three characters before each call, and the function is rate limited per account.
- The geocoder is Photon on OpenStreetMap data, self-hosted on the owner's home server with the Canada index and reached through a Cloudflare tunnel. A proxy accepts only requests that carry an access key, which the search function sends, so the public cannot use it. It is replaceable behind one `Geocoder` contract in the core package.
- Directions on a place card or pin opens Waze by default, through the maps handoff (maps-handoff.md). This is a handoff, not navigation (decision 0006).

## Rationale

Decision 0004 requires any third party that would receive member locations to be vetted. Typed text with a coarse bias, sent through our own function, reveals far less than a position and no identity, though a typed street name still hints at a home area, which is why recent searches stay on the device and are never stored on the server, and reading nearby places from the tiles keeps the precise position on the device. Photon uses the same OpenStreetMap data as the tiles (decision 0017), needs no key, and can be self-hosted later like the tiles.

## Consequences

- The tile style must include the points-of-interest layer.
- The home server is not highly available: if the home internet or power drops, search stops, and the function answers `search_unavailable`. Moving it to a cloud host is tracked with the other free-to-paid moves (#41). The hostname is a temporary subdomain until the product has its own domain.
- Search finds stores and other named places only if OpenStreetMap lists them; a business missing from it will not be found, and opening hours and ratings are not available.

## Revisit

If search quality is poor for Toronto, or a vetted provider that sees only text is needed.
