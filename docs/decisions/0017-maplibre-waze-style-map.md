# 0017 MapLibre map with a Waze-style look

Status: accepted

## Decision

The shared map uses MapLibre with a custom night style, "RDV Night", inspired by Waze, on both platforms. Tiles are OpenFreeMap vector tiles built from OpenStreetMap. This replaces react-native-maps (Apple Maps on iPhone, Google Maps on Android).

## Rationale

The owner wants a Waze-like map, not Apple Maps. A custom style gives full control of the look and the same map on both platforms. It needs no API key and has no per-request billing, which fits the free-tier approach (decision 0010), and it removes the Google Maps key requirement.

## Consequences

- OpenStreetMap and OpenMapTiles attribution must stay visible.
- OpenFreeMap is a free community service with no key and no stated limits, but also no guarantee. Before launch decide whether to self-host the tiles (for example Protomaps files on Cloudflare R2) so the map does not depend on it.
- Follow mode is tilted and heading-up. The camera is set from the person's own location updates.
- Maps handoff for RDVs is separate and unchanged: it opens the person's own maps app (decision 0006).
- A native library change, so the app needs a new development build.

## Revisit

If the tiles prove unreliable, or the style needs data OpenFreeMap does not carry.
