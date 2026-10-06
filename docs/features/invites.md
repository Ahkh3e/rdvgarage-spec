# Invites and onboarding

## Goal

Keep access referral-only (decision 0003) while making friend-to-friend invites frictionless.

## Behavior

- Every member has one personal invite link, reusable and unlimited.
- Opening the link on iPhone goes straight into the app if installed.
- If the app is not installed, the link opens a landing page that copies the invite code to the clipboard and sends the user to the App Store. iOS does not carry link data through an install, so on first launch the app reads the pasted code, and the user can always type the code manually.
- Onboarding includes an Enter invite code screen.
- Signup requires a valid invite link. No link, no account.
- Each account records who invited it. The referral chain is stored.
- A member can regenerate their link at any time; the old link stops working.
- A member can see who joined through their link.
- Each link has a signup cap that the owner of the link can raise; the cap value is to be decided. The operator can disable a link.
- A leaked link is a known risk in 0.0.1: it is limited by the cap, regeneration, and the operator CLI, and nothing more until abuse tooling ships.
- In 0.0.1 an invited member can be suspended or deleted only by the operator (see accounts.md). In-app removal from the app is not available; crew owners can remove members from their own crew (see crews.md).

## Onboarding flow

1. Open invite link
2. Register an account in our own user database
3. Choose handle and avatar
4. Grant When In Use location permission (explained, not forced); the upgrade to Always happens on first Go live (privacy.md)
5. Land on My Crews, prompted to create or join a crew

## Rules

- Handles are unique and not changeable more than once per 30 days.
- Because links are unlimited, exclusivity relies on revocation and the referral chain. Abuse tooling is an open question.

## iOS

- Universal links for invite URLs.
- Clipboard read on first launch needs the user's paste permission prompt; handle denial by falling back to manual code entry.
- Credential method is open (decision 0008). Sign in with Apple is the recommended credential, stored against our own user record.

## Open questions

- Abuse handling: rate limits on signups per link, review queue
- Is joining a crew separate from joining the app? (Assumed yes; crews have their own join links.)
