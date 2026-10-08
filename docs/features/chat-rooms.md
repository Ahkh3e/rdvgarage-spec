# Chat rooms

## Goal

Members talk in text, in a room for a crew or for a hand-picked group. A room is also where its walkie-talkie channel lives (walkie-talkie.md). Rendezview organizes no gathering and vouches for nothing said in a room (disclaimers.md).

## Rooms

- **Crew room.** Every crew has one standing room. Its members are exactly the crew's members, so joining or leaving the crew joins or leaves the room. It cannot be deleted while the crew exists. Deleting the crew deletes its room, members, messages and floor, and disconnects anyone in its walkie channel (api.md).
- **Invite-only room.** Any member can create a room with a name (3-30 characters) and an optional description (up to 140 characters), and add members from the people they share a crew with. It can include members of different crews. The creator is its owner.
- **RDV room.** The host can open a room for an RDV. Its members are the people who answered Going or Maybe, kept in step with the RSVPs automatically; nobody adds or removes them by hand. It has no owner: the host and the owners of the RDV's crews can delete messages, and it closes when the RDV's window ends or the RDV is cancelled (rdvs.md). A scheduled job closes such rooms, which sends `room_closed` for new messages and disconnects anyone in the walkie channel; a closed room stays readable until its messages expire, and the host cannot reopen it once the window has passed. If the host deletes their account the RDV is cancelled and the room closes.
- A room screen is the message list with the composer at the bottom and the walkie Talk button beside it (walkie-talkie.md).
- The room list is a Rooms tab: crew rooms, then other rooms, each with its last message line and an unread count. A room with no messages shows an empty state.

## Messages

- Text only in this release: up to 1000 characters, plain text with links shown as text. No images, files, reactions or replies.
- Messages arrive live to members who are in the app and appear in the list for everyone else on their next load.
- A message shows the sender's handle, avatar and car icon, and its time.
- The sender can delete their own message. The owner of an invite room, a crew owner or admin for its crew room, and the host or an owner or admin of one of the RDV's crews for an RDV room can delete any message in that room. A deleted message disappears for everyone.
- Messages are kept for 7 days and then deleted by a scheduled job (the value is a server setting, `chat_message_ttl_days`). There is no history beyond that and no export.
- A member who joins sees only messages sent after they joined, and only while they are still kept. A person who leaves a crew or changes an RSVP and returns gets a new join time, so earlier messages stay hidden from them. Membership of crew rooms and RDV rooms is kept in step by database triggers on crew membership and RSVPs, so it never waits for a job.

## Membership

- The owner of an invite-only room can add and remove members and delete the room. Crew rooms and RDV rooms have no member management; they follow the crew or the RSVPs. An owner can transfer ownership. Members can leave at any time.
- Added members get the room in their list at once and can leave if they did not want it. Nobody can be added who does not share a crew with the person adding them.
- Inside an invite-only room members see each other's handle, avatar and car icon, even when they share no crew. That is all they see; crews and other profile fields stay private (crews.md).
- A member who is removed, leaves, or is removed from the crew behind a crew room stops seeing the room and its messages at once. To remove someone from a crew room, a crew owner or admin removes them from the crew (crews.md). In an RDV room the host or a crew owner or admin of the RDV can remove a member, who is then blocked from the room even while their RSVP stays, so the RSVP sync does not add them back.
- Leaving a room or a crew removes only the person's membership; their earlier messages stay for the others until they expire. Deleting an account removes the person's messages and memberships. An invite room whose owner deletes their account passes to the longest-standing member, or is deleted if it has none.

## Unread and notifications

- Each member has a read marker per room, starting at the time they joined. The unread count is the messages from other people after the later of the marker and the join time, so it never counts messages the person cannot see.
- While the app is running, it listens on one private channel for the person, `inbox:<user_id>`, which carries a new-message event for every room they are in. A new message from someone else, while the person is away from that room and has not muted it, shows as a local notification, "@handle in <room>", with the message text; the text stays on the phone. A member can mute a room, which keeps the unread count and stops the notification.
- A message reaches a closed app only through remote push (notifications.md, #33). A push carries the handle and the room name only, never the message text, so no third party sees conversation (decision 0004), and the person's mute setting is stored on the server so the push can honour it. Notifications never carry a position.
- The notification permission is asked the first time a person opens the Rooms tab, not at launch. Refusing it leaves chat working.

## Rules

- Chat and the walkie-talkie are not safe to use while driving. The room screen shows the short line in disclaimers.md (Placement), and the product adds no lockout or speed rule (CLAUDE.md, disclaimers.md).
- Shipping chat and voice bumps the terms version, so everyone accepts the updated disclaimers on their next open (disclaimers.md).
- Moderation of voice is per crew, not per room: a crew owner or admin turns a member's voice off once for all of that crew's rooms (walkie-talkie.md). Text is not affected by it.
- Location is never part of chat: no live position, speed or route appears in a room, and nothing in a message is read for location.
- Only members of a room can read, send or list it. Policies enforce this in the database (data-model.md).
- A person cannot be added to a room by someone they share no crew with, and a room never reveals who else is a member to anyone outside it.

## Platform notes

- Live delivery uses Realtime Broadcast. Sending a message writes it to the table and broadcasts a small event (room id, sender, text) to each member's private `inbox:<user_id>` channel, which only that person can join. One channel per person, however many rooms, keeps the connection count down; each message counts as one Realtime message per recipient against the free-tier monthly limit (architecture.md, #41). The room screen shows live messages from the same channel.
- The Rooms tab, room list and room screen are the same on iPhone and Android.

## Data and API

Tables `chat.rooms`, `chat.members`, `chat.messages` in data-model.md; functions in api.md.

## Open questions

- Report and block: they are out of scope in 0.0.1 and chat is where they matter first.
- Whether members can mute another member.
- Images and other attachments.
- Whether RDV rooms should stay open after the RDV for a short time.
