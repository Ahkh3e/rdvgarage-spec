# 0028 Crew admins

Status: accepted

## Decision

A crew has one owner, the person who started it, and any number of admins that the owner adds and can remove. The owner and the admins are the crew's moderators.

- Admins can remove members (not the owner or another admin), turn a member's voice off or on for the whole crew, delete messages in the crew's rooms, cancel RDVs of the crew, and remove pins of the crew.
- Only the owner can add or remove admins, regenerate the crew link, transfer ownership and delete the crew.

## Rationale

Moderation has grown with chat and voice, and one person cannot watch a large crew. Splitting it from the owner's account-level powers lets the owner share the work without sharing control of the crew itself.

## Consequences

- Replaces the "admin role is planned later" line in crews.md and the owner-only checks in api.md and data-model.md with moderator checks for the actions above.
- Recorded in `crews.members.role` (owner, admin, member).
- When an admin leaves or is removed from the crew, the role goes with the membership.

## Revisit

If admins need to add members or edit the crew, or the owner needs a way to remove an admin's actions.
