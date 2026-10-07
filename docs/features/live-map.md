# Live map

## Goal

See crew members on a shared map as they drive; the map follows the user.

## Behavior

- The map shows members of the selected crews (see crews.md) who are currently live.
- Sharing is manual per session (see privacy.md). Members who aren't live do not appear.
- Follow mode: the map centers on the user and rotates with heading, with no input needed while driving. Panning exits follow mode; a recenter button returns to it.
- Each member marker is their chosen racecar icon on a badge ringed in the crew tint, with their handle below (car-icons.md). Each live member has a short movement trail (movement-trails.md).
- RDV pins appear on the map for the selected crews.
- Tapping a member shows a card: avatar, handle, crew, last update, and a Follow button. The crew list under the map shows each member with their car icon and live state, and each live member's speed in km/h when they have chosen to show it (decision 0022). Speed is not shown on markers or the card.
- Tapping an RDV pin opens its detail with a Directions button (maps handoff).

## Rules

- No ETAs, routing, or route lines (decision 0006).
- Positions broadcast about every 3 seconds while moving and every 15 seconds while stationary (architecture.md).
- A member who is not live has no position on the map: about 45 seconds after their last update they disappear from the map, and the crew list shows them as offline. There is no idle or dimmed state.

## Platform notes

- Location comes from the cross-platform location library with a background task while live (architecture.md).
- iPhone: Always authorization during a live session; the blue status bar indicator is expected.
- Android: foreground and background location permission; a persistent notification shows while live and serves as the live indicator.
- The map is MapLibre with the RDV Night style and OpenFreeMap vector tiles on both platforms (decision 0017). No key is needed.
- Live Activity (iPhone) and the Android ongoing-notification equivalent for an active session are later platform modules.

## Open questions

- Position update cadence versus battery
