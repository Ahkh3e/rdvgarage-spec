# Privacy controls

## Principle

Location is shared only within crews (decision 0004), and only when the user chooses.

## Behavior

- Sharing is manual per session. The user taps Go live and picks which of their crews can see them.
- Go live is explicit each time; there is no persistent always-on mode.
- A session ends when the user taps Stop, when the app is force-quit, or when location permission is revoked. Being stationary does not end a session; a parked user at a meet stays live.
- A stationary session still sends a heartbeat every 30 seconds. If a session has no checkpoint or heartbeat for more than 5 minutes, the server marks it ended and finalizes it from the last received data. This 5-minute value is used everywhere.
- On session end the max speed and distance are finalized and stored.
- While live, a persistent indicator shows who can see the user.
- Arriving at an RDV can prompt: Go live for this RDV? Accepting opens the Go live sheet with that RDV's crews chosen; the person still taps Start.
- Directions is offered for places, pins and RDVs only, never for a member: a member's live position is not handed to a maps app.
- Stopping removes the user from the map for everyone immediately.

## Data

- Live positions are held only as long as needed to render the map. No breadcrumb or route history is stored or shared.
- Per completed session the server stores only: start and end time, total distance, max speed, the time max speed was set, and the crews the session was shared with. This feeds the leaderboard and stats.
- A session summary is split at the leaderboard week boundary, one summary per week touched, so each week gets its own max speed and distance.
- A session summary is visible only to the crews the user chose at Go live. A crew the user did not share with never sees it in its leaderboard or stats.
- Session summaries are kept while the account exists.
- Account deletion removes all session summaries.
- Distance and attendance stats are computed from sessions and RDV arrivals (stats.md).
- Chat messages are kept for 7 days and then deleted; account deletion removes a person's messages at once (chat-rooms.md). Walkie-talkie audio is not recorded or stored by Rendezview; it passes through Rendezview's own relay in memory, which sees the voice and the network address but no location, crew name or handle (decision 0029).
- Location data is never sold or sent to third parties for ads. Any processor must be vetted (decision 0004).

## Platform notes

- iPhone: location permission is two steps. Onboarding asks for When In Use with an explanation. The first time the user taps Go live, the app asks to upgrade to Always, with a clear purpose string.
- Android: foreground location is requested in onboarding; background location is requested on first Go live, with a rationale screen. While live, a persistent notification shows who can see the user.
- Go live without background location is not offered; the app explains why it is needed.
- If permission is denied, the app explains how to enable it and works without sharing.

## Open questions

- Per-RDV sharing window versus manual sessions only (after 0.0.1)
