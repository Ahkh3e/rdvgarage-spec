# Store submission notes

Not needed for 0.0.1 development. Recorded here so nothing is missed later.

## Both stores

- Privacy policy and terms of use at public URLs; they must match `docs/disclaimers.md` and `docs/data-model.md`.
- In-app account deletion (present in accounts.md).
- Location data handling disclosed accurately: collected only while live, shared with chosen crews, summaries kept while the account exists.
- Age rating consistent with the 18 or older rule.
- Reviewer access: the app is invite-only, so provide a reviewer account or a long-lived reviewer invite path in the review notes.

## Apple App Store

- Privacy nutrition labels for precise location and user content.
- Purpose strings for When In Use and Always location, matching privacy.md wording.
- Background location mode justification; the app uses it only while a Go live session is active.
- No Sign in with Apple needed because there is no third-party login (decision 0013).
- Privacy labels list email address (account confirmation and recovery) as well as precise location and user content.

## Google Play

- Data safety form.
- Background location permission declaration with a demonstration video; the persistent notification while live supports the justification.
- Foreground service declaration for location.

## Also

- Developer program accounts: Apple Developer Program, Google Play developer registration.
- Release signing and EAS credentials.
- A support contact address (can be a role address on the link domain).
