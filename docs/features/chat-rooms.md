# Chat rooms

## Goal

Members talk in text, in a room for a crew or for a hand-picked group. A room is also where its walkie-talkie channel lives (walkie-talkie.md). Rendezview organizes no gathering and vouches for nothing said in a room (disclaimers.md).

## Rooms

- **Crew room.** Every crew has one standing room. Its members are exactly the crew's members, so joining or leaving the crew joins or leaves the room. It cannot be deleted while the crew exists and is deleted with the crew.
- **Invite-only room.** Any member can create a room with a name (3-30 characters) and an optional description (up to 140 characters), and add members from the people they share a crew with. It can include members of different crews. The creator is its owner.
- **RDV room.** The host can open a room for an RDV. Its members are the people who answered Going or Maybe, kept in step with the RSVPs automatically; nobody adds or removes them by hand. It has no owner: the host and the owners of the RDV's crews can delete messages, and it closes when the RDV's window ends or the RDV is cancelled (rdvs.md). If the host deletes their account the RDV is cancelled and the room closes.
- The room list is a Rooms tab: crew rooms, then other rooms, each with its last message line and an unread count. A room with no messages shows an empty state.

## Messages

- Text only in this release: up to 1000 characters, plain text with links shown as text. No images, files, reactions or replies.
- Messages arrive live to members who have the room open and appear in the list for everyone else on their next load.
- A message shows the sender's handle, avatar and car icon, and its time.
- The sender can delete their own message. A room owner can delete any message in their room, and a crew owner can delete any message in their crew's room. A deleted message disappears for everyone.
- Messages are kept for 7 days and then deleted by a scheduled job (the value is a server setting, `chat_message_ttl_days`). There is no history beyond that and no export.
- A member who joins sees only messages sent after they joined, and only while they are still kept. A person who leaves a crew or changes an RSVP and returns gets a new join time, so earlier messages stay hidden from them. Membership of crew rooms and RDV rooms is kept in step by database triggers on crew membership and RSVPs, so it never waits for a job.

## Membership

- The owner of an invite-only room can add and remove members and delete the room. Crew rooms and RDV rooms have no member management; they follow the crew or the RSVPs. An owner can transfer ownership. Members can leave at any time.
- Added members get the room in their list at once and can leave if they did not want it. Nobody can be added who does not share a crew with the person adding them.
- Inside an invite-only room members see each other's handle, avatar and car icon, even when they share no crew. That is all they see; crews and other profile fields stay private (crews.md).
- A member who is removed, leaves, or is removed from the crew behind a crew room stops seeing the room and its messages at once.
- Leaving or deleting an account removes the person's messages and memberships. A room whose owner deletes their account passes to the longest-standing member, or is deleted if it has none.

## Unread and notifications

- Each member has a read marker per room; the unread count is the messages after it from other people.
- While the app is running, it listens on the channel of each room the person has not muted. A new message from someone else, while the person is away from that room, shows as a local notification, "@handle in <room>", with the message text; the text stays on the phone. A member can mute a room, which keeps the unread count and stops the notification.
- A message reaches a closed app only through remote push (notifications.md, #33). A push carries the handle and the room name only, never the message text, so no third party sees conversation (decision 0004), and the person's mute setting is stored on the server so the push can honour it. Notifications never carry a position.
- The notification permission is asked the first time a person opens the Rooms tab, not at launch. Refusing it leaves chat working.

## Rules

- Chat and the walkie-talkie are not safe to use while driving. The room screen shows the short line in disclaimers.md (Placement), and the product adds no lockout or speed rule (CLAUDE.md, disclaimers.md).
- Location is never part of chat: no live position, speed or route appears in a room, and nothing in a message is read for location.
- Only members of a room can read, send or list it. Policies enforce this in the database (data-model.md).
- A person cannot be added to a room by someone they share no crew with, and a room never reveals who else is a member to anyone outside it.

## Platform notes

- Live delivery uses Realtime Broadcast on a private channel per room, authorised by room membership, like crew position channels (architecture.md). Messages are written through a function and read from the table. A person in many rooms holds many channels while the app runs, which counts against the Realtime connection limit (architecture.md); the app listens only to unmuted rooms.
- The Rooms tab, room list and room screen are the same on iPhone and Android.

## Data and API

Tables `chat.rooms`, `chat.members`, `chat.messages` in data-model.md; functions in api.md.

## Open questions

- Report and block: they are out of scope in 0.0.1 and chat is where they matter first.
- Whether members can mute another member.
- Images and other attachments.
- Whether RDV rooms should stay open after the RDV for a short time.
