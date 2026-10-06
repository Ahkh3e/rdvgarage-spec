# Invites and onboarding

## Goal

Keep access referral-only (decision 0003) while making friend-to-friend invites frictionless and with no limits on how many people a member can bring in.

## Behavior

- A member invites someone with Share invite. Each tap creates a new invite link and opens the system share sheet.
- An invite link is active for 24 hours from creation, then expires.
- An invite can be used by any number of people until it expires. There is no limit on signups per invite and no limit on how many invites a member creates.
- A member can revoke an active invite before it expires, and can see who joined through each invite they shared.
- The operator can disable any invite.
- An invite whose inviter is suspended or deleted stops working immediately.
- Validity is checked when the link is opened or the code is entered, and again at registration. Registration honors an invite for one extra hour after it expires so a person already in onboarding is not cut off. A revoked or disabled invite, or one whose inviter is suspended or deleted, fails immediately.
- An expired, revoked, or disabled invite opens a landing page that says so and asks the user to request a new invite from the person who shared it. The code-entry screen shows the same message.
- A typed invite code expires with its link.
- Signup requires a valid invite. No invite, no account.
- Each account records who invited it and which invite. The referral chain is stored. Invite records, including expired ones, are kept while the inviter account or its tombstone exists, so the chain and the who-joined list stay intact.
- In 0.0.1 an invited member can be suspended or deleted only by the operator (see accounts.md). Crew owners can remove members from their own crew (see crews.md).

## Link carrying

- Opening the link on a phone with the app installed goes straight into the app.
- If the app is not installed, the link opens a landing page that routes by platform. iPhone: copies the invite code to the clipboard and sends the user to the App Store; on first launch the app reads the pasted code. Android: sends the user to the Play Store with the code in the install referrer, which the app reads on first launch. Desktop: explains that RDV Garage is a mobile app.
- On either platform the user can always type the code manually on the Enter invite code screen.

## Onboarding flow

1. Open the invite link, or enter the code
2. Fill the account form: handle, email, password, optional avatar; accept the disclaimers and confirm being 18 or older (`docs/disclaimers.md`)
3. Confirm the email by tapping the link, which hands off to the app, then sign in
4. Grant When In Use location permission (explained, not forced); the upgrade to Always happens on first Go live (privacy.md)
5. Land on My Crews, prompted to create or join a crew

## Rules

- Handles are unique and not changeable more than once per 30 days.
- Exclusivity relies on the 24-hour expiry, revocation, the referral chain, and operator action. There are no quotas by design.
- Crew links cannot be used to sign up. They work only for existing app members, so signup always needs a Share invite.

## Platform notes

- iPhone: universal links for invite URLs. Clipboard read on first launch needs the user's paste permission prompt; handle denial by falling back to manual code entry.
- Android: App Links for invite URLs and the Play Install Referrer to carry the code through install.
- Accounts are email and password (decision 0013). More platform detail is in `docs/architecture.md`.

## Open questions

- Abuse handling beyond revocation and operator action, if misuse appears
