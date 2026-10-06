# Live map

## Goal

See crew members on a shared map as they drive; the map follows the user.

## Behavior

- The map shows members of the selected crews (see crews.md) who are currently live.
- Sharing is manual per session (see privacy.md). Members who aren't live do not appear.
- Follow mode: the map centers on the user and rotates with heading, with no input needed while driving. Panning exits follow mode; a recenter button returns to it.
- Each member marker shows avatar, handle, crew color, and a car icon when car profiles ship.
- RDV pins appear on the map for the selected crews.
- Tapping a member shows a card: handle, crew, last update. Speed is never shown live (decision 0007).
- Tapping an RDV pin opens its detail with a Directions button (maps handoff).

## Rules

- No ETAs, routing, or route lines (decision 0006).
- Positions broadcast about every 3 seconds while moving and every 15 seconds while stationary (architecture.md).
- Stale positions fade, then drop after a timeout.

## Platform notes

- Location comes from the cross-platform location library with a background task while live (architecture.md).
- iPhone: Always authorization during a live session; the blue status bar indicator is expected.
- Android: foreground and background location permission; a persistent notification shows while live and serves as the live indicator.
- The map is MapLibre with the RDV Night style and OpenFreeMap vector tiles on both platforms (decision 0017). No key is needed.
- Live Activity (iPhone) and the Android ongoing-notification equivalent for an active session are later platform modules.

## Open questions

- Position update cadence versus battery
