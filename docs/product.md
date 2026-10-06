# Product

RDV Garage is a referral-only social app for car enthusiasts, built for the Toronto car scene. "RDV" is short for rendezvous and is always said R-D-V.

Platform: iPhone first, Android eventually, one codebase (decisions 0005, 0011). Feature breakdown in `docs/features/README.md`.

## Core

- Join by invite only.
- Form private crews.
- Live shared map per crew; the map follows the user while driving.
- Crews drop RDVs: meets, cruises, private events.
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

- Referral chain visibility: who can see who invited whom
- Crew membership rules beyond owner and member
- Location sharing controls: pause, ghost mode, per-RDV sharing window
- Background location and battery strategy
