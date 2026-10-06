# Privacy controls

## Principle

Location is shared only within crews (decision 0004), and only when the user chooses.

## Behavior

- Sharing is manual per session. The user taps Go live and picks which of their crews can see them.
- Go live is explicit each time; there is no persistent always-on mode.
- A session ends when the user taps Stop, or after a configurable idle timeout.
- While live, a persistent indicator shows who can see the user.
- Arriving at an RDV can prompt: Go live for this RDV?
- Stopping removes the user from the map for everyone immediately.

## Data

- Live positions are held only as long as needed to render the map. No long-term location history is shared with crews.
- Distance and attendance stats are computed from sessions and RDV arrivals (stats.md).
- Location data is never sold or sent to third parties for ads. Any processor must be vetted (decision 0004).

## iOS

- Always authorization is requested only when the user first goes live, with a clear purpose string.
- If permission is denied, the app explains how to enable it and works without sharing.

## Open questions

- Retention period for location history stored on the server for stats
- Per-RDV sharing window versus manual sessions only
