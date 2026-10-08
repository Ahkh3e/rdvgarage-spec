# 0026 Chat rooms and walkie-talkie

Status: accepted

## Decision

- Rooms: every crew has a standing room, any member can create an invite-only room that can span crews, and an RDV can open a room for the members who plan to attend.
- Text messages are kept for 7 days, then deleted by a scheduled job. Deleting an account removes the person's messages.
- Each room has a walkie-talkie channel: hold to talk, one speaker at a time, 30 seconds a turn, not recorded.
- Inside an invite-only room members see each other's handle, avatar and car icon, even without a shared crew, and nothing more.

## Rationale

Crews need somewhere to talk besides the map. Invite-only rooms let friends from two crews talk without merging them, which is how people actually move between groups. A short retention keeps the stored record of conversations small, which suits a privacy-first product (decision 0004). Walkie-talkie is a different shape from messaging: it is live and ephemeral, so it is stored nowhere.

## Consequences

- Revealing a handle to non-crew members extends who can see a profile (crews.md says crew membership is visible to crew members only). The rule is limited to the room and to the people who chose to be added.
- Chat while driving is a safety risk. Safety stays with prominent disclaimers (CLAUDE.md, disclaimers.md), not a lockout.
- Report and block become needed sooner; they stay open in chat-rooms.md.
- Decision 0027 covers the voice service.

## Revisit

If retention should be longer, or if abuse in invite-only rooms needs a report-and-block feature first.
