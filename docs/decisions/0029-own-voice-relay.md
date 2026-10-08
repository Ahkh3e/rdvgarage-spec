# 0029 Our own voice relay

Status: accepted. Supersedes decision 0027.

## Decision

Walkie-talkie audio is relayed by a small service that Rendezview runs on its own server, over a WebSocket on HTTPS behind the Cloudflare Tunnel, instead of a LiveKit media server.

- A phone gets a short-lived signed token from the `walkie_token` function and connects to the relay with it. The relay forwards each talker's audio frames to the other people in the room, in memory, and keeps no audio, content or logs of it.
- The relay knows who each connection is from the token, so it announces who is talking itself. Nobody can claim to be someone else.
- The relay enforces who may talk (voice-off members get a listen-only token), the 60 second turn limit, frame size and rate limits, and disconnects people on request from `walkie_kick`.
- Audio is compressed on the phone and mixed on the phone; the server does not mix.

## Rationale

A media server needs UDP and open ports, which a Cloudflare Tunnel cannot carry and a home behind two routers cannot easily offer. A relay over a WebSocket works through the tunnel with no router changes, keeps the audio on a server the owner controls so no third party hears it (decision 0004), and has no per-minute fee. Hold-to-talk voice is forgiving: it does not need the echo cancellation and congestion control that make a full media server worth its cost.

## Consequences

- Audio over TCP can stutter on a poor network because one lost packet holds up the rest. Quality on cellular is the thing to test.
- There is no echo cancellation; the speaker is muted while Talk is held.
- The relay runs on the home server, so its uptime and bandwidth are the limits. Moving it to a cloud host is part of #41.
- Decision 0027's LiveKit and WebRTC native modules are removed. The `Voice` contract in core stays, so another transport can replace this one.
- The disclaimers now say voice is relayed by Rendezview's own server, not a third party.

## Revisit

If voice quality over cellular is poor, or the number of listeners outgrows the home server, move to a media server over UDP (decision 0027's approach) on a host with a public address.
