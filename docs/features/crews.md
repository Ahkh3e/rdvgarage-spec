# Crews

## Goal

Private groups of members who share a map and RDVs.

## Behavior

- A user can belong to any number of crews. No cap on crew count.
- Roles in 0.0.1: owner and member. An admin role is planned later. Owner can transfer ownership.
- Join by crew link. Joining a crew requires being an app member. Admin invites arrive with the admin role.
- Leaving is always allowed. The owner can remove members from the crew.
- The crew link is reusable and does not expire. It works only for existing app members and cannot be used to sign up. The owner can regenerate it at any time; the old link stops working.
- Crew has a name, avatar, and short description.

## My Crews

- The home screen is My Crews: the user's crews listed with live status.
- The user selects one or more crews. The map shows the selected crews together.
- Selection persists between sessions.
- Each crew has a color or marker style so members from different crews are distinguishable on the combined map.

## Rules

- A member visible in more than one selected crew shows once.
- Crew membership is visible to other members of that crew only.
- Crew size limits are open; none in v1 unless performance requires one.

## iOS

- Crew links are universal links with the same code-paste fallback as app invites.
- Crew selection is stored locally and synced to the account.

## Open questions

- Crew discoverability: none (link only) assumed
- Crew size limit if the combined map gets crowded
