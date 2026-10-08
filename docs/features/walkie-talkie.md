# Walkie-talkie

## Goal

Hold a button and talk to a room, and everyone who is listening hears you at once, like a radio. Each room (chat-rooms.md) has one walkie channel. It is voice for the people in the room only, and Rendezview does not record it.

## Behavior

- A room's walkie channel is off until a member joins it. Joining makes the person a listener: they hear whoever talks. Leaving stops it.
- To talk, the person holds the Talk button on the room screen. While held, their microphone is open and a "On the air" state shows on their phone and on every listener's. Releasing closes the microphone.
- One person talks at a time. The first to press takes the floor; anyone else who presses while it is held sees who has it and cannot talk until it is free. A turn ends when the person releases, or after 30 seconds, whichever comes first, and the floor is then free.
- Everyone listening sees who is talking: the speaker's handle, avatar and car icon, and a level meter. Nothing else about the speaker is shown.
- A latch option in the walkie settings lets a tap open the microphone and a second tap close it, still limited to 30 seconds. It does not change anything else.
- Joining needs the microphone permission, asked the first time a person joins a channel with a plain explanation. Refusing it lets them listen only.
- A member can mute a channel's sound without leaving it, and can see who else is in the channel.
- A person can be in one channel at a time. Joining another leaves the first.
- While in a channel the app keeps audio running in the background so a person can listen with the phone locked or in another app. iPhone uses the audio background mode; Android keeps a foreground service with a persistent notification. Talking requires the app to be open.
- An iPhone Live Activity and the Android notification show "In a walkie channel" with the room name and a Leave control while joined. They show nothing about location.

## Voice transport

Live audio goes through a hosted real-time audio service, LiveKit Cloud (decision 0027), never through Supabase.

- Joining calls the `walkie_token` function, which checks membership in the room and returns a short-lived token for that room only. The token lets the person listen, and talk only while they hold the floor.
- The floor is controlled by `take_floor` and `release_floor` in the database, with a lease that ends by itself after 30 seconds, so a crashed phone cannot hold it. The app opens the microphone only after `take_floor` succeeds, and the token's publish right follows the lease.
- Audio is not recorded or stored by Rendezview, and the audio service's recording options stay off.
- The audio service sees the audio and an opaque participant id. It is never given a position, a crew or a handle.

## Rules

- Walkie-talkie is not safe to use while driving; the Talk button needs a hand, and hands-free use depends on the person's own headset. The screen shows the short line in disclaimers.md (Placement). The product adds no lockout (CLAUDE.md).
- Only members of a room can join its channel or take the floor; removal from the room ends the person's audio at once, by revoking the token and ending any lease.
- No audio is kept after a turn ends, and nothing in a channel carries location, speed or a route.
- A room owner can remove a member from a channel and from the room.
- Every channel has a short disclaimer and a visible On the air state so nobody is recorded without knowing; the microphone indicator of the phone is expected.

## Platform notes

- The client uses the LiveKit React Native SDK with the WebRTC native modules, so it needs a new development build (not Expo Go). Android needs the foreground service and microphone permissions; iPhone needs the audio and voice-over-IP background modes and the microphone usage text.
- Routing follows the phone: speaker, a connected headset, or Bluetooth. A hardware button as the Talk control is later work.
- CarPlay and Android Auto are later platform modules (features/README.md).

## Data and API

`chat.floors` in data-model.md; `walkie_token` (Edge), `take_floor`, `release_floor` and `leave_walkie` (SQL) in api.md.

## Open questions

- Headset or steering-wheel button as Talk, to avoid touching the phone.
- Whether a latch mode should exist at all given the safety line.
- Audio service cost and limits as usage grows, tracked with moving free services to paid ones (#41).
- Echo and howling when two phones are in the same room.
