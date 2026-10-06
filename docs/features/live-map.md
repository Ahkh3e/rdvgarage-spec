# Live map

## Goal

See crew members on a shared map as they drive; the map follows the user.

## Behavior

- The map shows members of the selected crews (see crews.md) who are currently live.
- Sharing is manual per session (see privacy.md). Members who aren't live do not appear.
- Follow mode: the map centers on the user and rotates with heading while driving. Panning exits follow mode; a recenter button returns to it.
- Each member marker shows avatar, handle, crew color, and a car icon when car profiles ship.
- RDV pins appear on the map for the selected crews.
- Tapping a member shows a card: handle, crew, speed, last update. Speed display is optional and off by default.
- Tapping an RDV pin opens its detail with a Directions button (maps handoff).

## Rules

- No ETAs, routing, or route lines (decision 0006).
- Positions update about once per second while driving, less when stationary.
- Stale positions fade, then drop after a timeout.

## iOS

- Core Location with Always authorization during a live session.
- Background location while live; the blue status bar indicator is expected.
- MapKit for the map surface.
- Live Activity for an active session is a Phase 1 stretch.

## Open questions

- Position update cadence versus battery
- Whether speed is shown at all
