# Product

RDV Garage is a referral-only social app for car enthusiasts, built for the Toronto car scene. "RDV" is short for rendezvous and is always said R-D-V.

Platform: iPhone first (decision 0005). Feature breakdown in `docs/features/README.md`.

## Core

- Join by invite only.
- Form private crews.
- Live shared map per crew; the map follows the user while driving.
- Crews drop RDVs: meets, cruises, private events.
- Live ETA from every member to an RDV.
- Stats: distance driven, meets attended, streaks.

## Principles

- Location is shared only within crews, never publicly.
- Not a navigation app. Tapping an RDV hands off to the user's own maps app.
- Exclusive by design: growth through referral, not open signup.

## Later

- Car profiles
- Event photo galleries
- Convoy mode
- Location-based minigames

## Open questions

- Invite mechanics: invite quota per user, revocation, referral chain visibility
- Crew size limits and membership rules
- Location sharing controls: pause, ghost mode, per-RDV sharing window
- Background location and battery strategy
- ETA source: a third-party routing API would send crew locations outside the crew, which violates the privacy principle. Options: on-device estimate, self-hosted routing, or a provider with no retention. Must be decided before the ETA feature is specced.
