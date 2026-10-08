# Walkie-talkie

## Goal

Hold a button and talk to a room, and everyone in the room hears you at once, like a group radio. Each room (chat-rooms.md) has one walkie channel. It is voice for the people in the room only, and Rendezview does not record it.

## Behavior

- Being in a room is being in its walkie channel. There is no separate join step: when a member opens a room they are in the channel and hear whoever is holding Talk, with the chat on the same screen. Leaving the room (Back to the room list, the Leave control, or opening another room) leaves the channel.
- The room screen is a text chat with a voice button: the messages fill the screen, and at the bottom the text composer sits beside a round Talk button. A strip above the composer shows who is talking.
- To talk, the person holds the Talk button. While it is held their microphone is open and an "On the air" state shows on their phone and on every other member's. Releasing closes the microphone. The button is hold to talk only.
- Several people can talk at the same time and everyone in the room hears all of them mixed, so there is no turn-taking or floor. A held microphone closes by itself after 60 seconds, so a phone that is dropped or jammed against something does not stay open; the person presses again to keep talking.
- Everyone sees who is talking: the speaker's handle, avatar and car icon, and a level meter. Nothing else about the speaker is shown.
- Opening a room never asks for the microphone. The permission is asked the first time the person presses Talk, with a plain explanation. Refusing it lets them listen only.
- A member can mute the room's sound without leaving it, and can see who else is in the channel.
- A person is in one room's channel at a time. Opening another room leaves the first.
- A member stays in the room, and keeps hearing it, while the app is in the background or the phone is locked, until they leave the room or end it from the Live Activity or notification. iPhone uses the audio background mode; Android keeps a foreground service with a persistent notification. Talking requires the app to be open.
- An iPhone Live Activity and the Android notification show "In a room" with the room name and a Leave control while the person is in the channel with the app in the background. They show nothing about location.

## Voice transport

Live audio goes through a relay that Rendezview runs on its own server (decision 0029), as a WebSocket over HTTPS. It is not a third-party service, it needs no UDP and no open router ports, and it is never routed through Supabase.

- Opening a room calls the `walkie_token` function, which checks membership in the room and returns a token for that room, valid 5 minutes. It lets the person listen and publish, or listen only when their voice is off for one of the room's crews. While in the room the app asks for a fresh token before each one expires, and the call fails once the person is no longer a member.
- The microphone is published only while Talk is held. The app never publishes silently.
- Who is talking comes from the relay. It knows who each connection is from the signed token, so it announces start and stop events itself and nobody can claim to be someone else. Listeners map the relay's participant ids to people with a roster the server issues: `walkie_token` also returns, for the room's current members only, a map from each participant id to the member. Listeners show the speaker's handle, avatar and car icon from the app's own data. The roster is refreshed with each token and when the room's member list changes, so someone who has just joined or been added can take up to about 15 seconds to show by name. The people in the channel are the people connected to the relay.
- The participant id is a keyed hash of the person and the room, so it is stable inside one room and cannot be linked across rooms.
- When a member is removed, leaves the crew behind a crew room, has their voice turned off, or the room is deleted or closed, a database trigger queues a `walkie_kick`, which asks the relay to disconnect that participant at once. Because a removed phone could reconnect with the token it already holds, the kick is repeated every minute for 6 minutes, and each time only while the person is still not allowed in; if they have been legitimately restored or re-added the kick is dropped. Turning voice back on queues no kick.
- Audio is relayed in memory and is not recorded, stored or logged by Rendezview. The relay keeps only counters.
- The relay sees the audio as it passes through, an opaque participant id and the room id. It is never given a position, a crew name or a handle.

## Rules

- Walkie-talkie is not safe to use while driving; the Talk button needs a hand, and hands-free use depends on the person's own headset. The screen shows the short line in disclaimers.md (Placement). The product adds no lockout (CLAUDE.md).
- A token request that is rate limited shows "Busy, try again shortly" and backs off to a minute rather than retrying hard. Nobody can change their own voice access, so a person whose voice was turned off cannot turn it back on.
- Only members of a room can open its channel or get a token. A moderator of the room (chat-rooms.md) can remove a member, which removes them from the channel at once.
- Voice can be turned off for a person for a whole crew. A crew owner or admin does it from the crew's member list, once, and it applies to every walkie channel of that crew's rooms (the crew room and the crew's RDV rooms) together. It is not set room by room. The person can still listen and use text chat; their Talk button is disabled with "Voice is off for you in <crew>". An owner can turn it back on at any time. Rooms that belong to no crew, the invite-only rooms, have no crew behind them, so only their owner removing a member applies there. An RDV room that spans several crews is voice-off for a person who is revoked in any one of those crews.
- No audio is kept, and nothing in a channel carries location, speed or a route.
- Every channel shows a visible On the air state so nobody is recorded without knowing; the microphone indicator of the phone is expected.

## Platform notes

- The client captures the microphone and plays the mixed audio through native audio libraries and sends compressed audio frames over the WebSocket, so it needs a new development build (not Expo Go). Android needs the foreground service and microphone permissions; iPhone needs the audio background mode and the microphone usage text.
- Routing follows the phone: speaker, a connected headset, or Bluetooth. A hardware button as the Talk control is later work.
- CarPlay and Android Auto are later platform modules (features/README.md).

## Data and API

No tables of its own. `walkie_token` and `walkie_kick` (Edge) in api.md; a private queue holds kicks until they have been sent and expired.

## Open questions

- Headset or steering-wheel button as Talk, to avoid touching the phone.
- Whether a latch mode (tap to open, tap to close) should exist at all given the safety line; it is not part of this release.
- Echo and howling when two phones are in the same room.
- Relay capacity and uptime on the home server: it carries every listener's copy of every talker's audio, so cost is bandwidth rather than a per-minute fee. Moving it to a cloud host is tracked with the other free-to-paid moves (#41).
- Audio over a TCP connection stutters on a poor network, because a lost packet holds up everything behind it. A media server over UDP would avoid that (decision 0029 revisit).
- There is no built-in echo cancellation, so the speaker is muted while Talk is held.
- Whether a revoked person should see how long their voice has been off, and whether a moderator can add a reason.
- Whether invite-only rooms need their own voice-off control, per room.
