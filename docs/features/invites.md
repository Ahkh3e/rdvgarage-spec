# Invites and onboarding

## Goal

Keep access referral-only (decision 0003) while making friend-to-friend invites frictionless.

## Behavior

- A member invites someone with Share invite. Each tap creates a new invite link and opens the iOS share sheet.
- An invite link is active for 24 hours from creation, then expires and cannot be used to sign up.
- Until it expires, an invite can be used by more than one person (multi-use within the 24 hours). Each invite has a signup limit, value to be decided, after which it stops working even if unexpired.
- A member can have only a limited number of active invites at once; the number is to be decided. At the limit, Share invite asks the member to revoke an active invite first. It never hands back an existing invite.
- Validity is checked when the invite is first redeemed, by opening the link or entering the code. Redeeming reserves one of the invite's signup slots; the slot is released if onboarding is abandoned, so the signup limit holds even when many people redeem at once. Once redeemed, the onboarding session holds that invite through registration, so expiry alone never rejects a user mid-onboarding. Registration still fails if the invite was revoked, disabled, or its inviter suspended or deleted in the meantime.
- A typed invite code expires with its link. If the app is opened after the pasted or typed code has expired, the code-entry screen shows the same expired message and the request-a-new-invite guidance.
- An expired, revoked, full, or disabled invite opens a landing page that says so and asks the user to request a new invite from the person who shared it.
- An invite whose inviter is suspended or deleted stops working immediately.
- Opening the link on iPhone goes straight into the app if installed.
- If the app is not installed, the link opens a landing page that copies the invite code to the clipboard and sends the user to the App Store. iOS does not carry link data through an install, so on first launch the app reads the pasted code, and the user can always type the code manually.
- Onboarding includes an Enter invite code screen.
- Signup requires a valid invite link. No link, no account.
- Each account records who invited it. The referral chain is stored.
- A member can revoke an active invite link before it expires.
- A member can see who joined through each invite they shared. Invite records, including expired ones, are kept for as long as the inviter account or its tombstone exists, so the referral chain and the who-joined list stay intact.
- The operator can disable any invite link.
- Expiry limits how long a leaked link is dangerous; revocation and the operator CLI cover the rest until abuse tooling ships.
- In 0.0.1 an invited member can be suspended or deleted only by the operator (see accounts.md). In-app removal from the app is not available; crew owners can remove members from their own crew (see crews.md).

## Onboarding flow

1. Open invite link
2. Register an account in our own user database
3. Choose handle and avatar
4. Grant When In Use location permission (explained, not forced); the upgrade to Always happens on first Go live (privacy.md)
5. Land on My Crews, prompted to create or join a crew

## Rules

- Handles are unique and not changeable more than once per 30 days.
- Exclusivity relies on invite expiry, the signup limit per invite, the active-invite limit per member, revocation, and the referral chain. Abuse tooling is an open question.
- Crew links cannot be used to sign up. They work only for existing app members, so signup always needs a Share invite.

## iOS

- Universal links for invite URLs.
- Clipboard read on first launch needs the user's paste permission prompt; handle denial by falling back to manual code entry.
- Credential method is open (decision 0008). Sign in with Apple is the recommended credential, stored against our own user record.

## Open questions

- Values for the per-invite signup limit and the active-invite limit per member
- Abuse handling and a review queue beyond those limits
- Revisit single-use invites if multi-use proves too loose
