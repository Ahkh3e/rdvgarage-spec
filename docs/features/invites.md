# Invites and onboarding

## Goal

Keep access referral-only (decision 0003) while making friend-to-friend invites frictionless.

## Behavior

- Every member has one personal invite link, reusable and unlimited.
- Opening the link on iPhone goes to the app, or to the App Store then into onboarding with the invite preserved.
- Signup requires a valid invite link. No link, no account.
- Each account records who invited it. The referral chain is stored.
- A member can regenerate their link at any time; the old link stops working.
- A member can be removed by whoever invited them or by an admin; removal cascades only if an admin chooses.

## Onboarding flow

1. Open invite link
2. Register an account in our own user database
3. Choose handle and avatar
4. Grant location permission (explained, not forced)
5. Land on My Crews, prompted to create or join a crew

## Rules

- Handles are unique and not changeable more than once per 30 days.
- Because links are unlimited, exclusivity relies on revocation and the referral chain. Abuse tooling is an open question.

## iOS

- Universal links for invite URLs.
- Credential method is open (decision 0008). Sign in with Apple is the recommended credential, stored against our own user record.

## Open questions

- Abuse handling: rate limits on signups per link, review queue
- Is joining a crew separate from joining the app? (Assumed yes; crews have their own join links.)
