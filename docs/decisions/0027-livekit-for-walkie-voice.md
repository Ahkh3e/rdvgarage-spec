# 0027 LiveKit for walkie-talkie voice

Status: accepted

## Decision

Live walkie-talkie audio goes through LiveKit Cloud over WebRTC. The server mints a short-lived, room-scoped token after checking room membership. Recording and egress features stay off. The service is behind one `Voice` contract in the core package so it can be replaced or self-hosted.

## Rationale

Real-time voice needs a media server; building one on Supabase Realtime is not possible and running our own needs the operator server that does not exist yet. LiveKit has a free tier, a React Native SDK, and an open-source server that can be self-hosted later.

## Vetting under decision 0004

Decision 0004 requires any third party that could receive member locations to be vetted. This service receives audio, not location:

- It is never given a position, speed, route, crew or handle. Participant ids are opaque and the display name sent is empty.
- Audio is not recorded by Rendezview and recording is off in the project settings.
- Tokens last 1 hour, name one room, and grant listen, with talk only while the floor lease holds.
- The service still sees the voice of whoever talks and their network address, which the product says in disclaimers.md.

## Consequences

- A new native dependency and development build.
- A cost item for moving free services to paid ones (#41); the free tier limits shape how many people can listen at once.
- Voice quality depends on the person's network, mostly cellular while driving.
- The API key and secret live in function secrets, never in the app.

## Revisit

If cost or the third-party exposure of audio becomes a problem, self-host the LiveKit server on the operator server.
