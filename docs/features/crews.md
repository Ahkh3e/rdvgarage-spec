# Crews

## Goal

Private groups of members who share a map and RDVs.

## Behavior

- A user can belong to any number of crews. No cap on crew count.
- Roles in 0.0.1: owner and member. An admin role is planned later. Owner can transfer ownership.
- Join by crew link. Joining a crew requires being an app member. Admin invites arrive with the admin role.
- Leaving is always allowed. The owner can remove members from the crew.
- The crew link is reusable and does not expire. It works only for existing app members and cannot be used to sign up. The owner can regenerate it at any time; the old link stops working.
- Crew has a name (3-30 characters), an optional avatar, and an optional description (up to 140 characters).
- The owner sees the member list and can remove a member, regenerate the crew link, transfer ownership, and delete the crew. A member sees the member list and can leave.

## My Crews

- The home screen is My Crews: the user's crews listed with live status.
- The user selects one or more crews. The map shows the selected crews together.
- Selection persists between sessions.
- Each crew has a color or marker style so members from different crews are distinguishable on the combined map.

## Rules

- A member visible in more than one selected crew shows once.
- Crew membership is visible to other members of that crew only.
- There are no limits on crew size or on crews per user.

## Platform notes

- Crew links are universal links on iPhone and App Links on Android. Opened by a non-member without the app, the link lands on a page that says an invite to the app is needed first and tells them to request a Share invite. There is no code-carrying fallback for crew links on either platform.
- Crew selection is stored on the account (`set_selected_crews`) and cached locally, so it follows the user to another device.

## Open questions

- Crew discoverability: none (link only) assumed
- Map clutter handling if a combined map gets crowded
