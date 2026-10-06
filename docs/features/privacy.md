# Privacy controls

## Principle

Location is shared only within crews (decision 0004), and only when the user chooses.

## Behavior

- Sharing is manual per session. The user taps Go live and picks which of their crews can see them.
- Go live is explicit each time; there is no persistent always-on mode.
- A session ends when the user taps Stop, after 15 minutes stationary, or when the app is force-quit or location permission is revoked.
- On session end the max speed and distance are finalized and stored. If the app is killed, the server closes the session after 5 minutes without a position and finalizes it from the last received data.
- While live, a persistent indicator shows who can see the user.
- Arriving at an RDV can prompt: Go live for this RDV?
- Stopping removes the user from the map for everyone immediately.

## Data

- Live positions are held only as long as needed to render the map. No breadcrumb or route history is stored or shared.
- Per completed session the server stores only: start and end time, total distance, max speed, and the time max speed was set. This feeds the leaderboard and stats.
- Session summaries are visible to crew members only through the leaderboard and stats. Retention: weekly leaderboard data kept for 90 days, distance totals kept while the account exists.
- Account deletion removes all session summaries.
- Distance and attendance stats are computed from sessions and RDV arrivals (stats.md).
- Location data is never sold or sent to third parties for ads. Any processor must be vetted (decision 0004).

## iOS

- Always authorization is requested only when the user first goes live, with a clear purpose string.
- If permission is denied, the app explains how to enable it and works without sharing.

## Open questions

- Per-RDV sharing window versus manual sessions only
