# RDV Garage Spec

Spec, design, and issue tracking for https://github.com/Ahkh3e/rdv-garage. No app code here.

## Product constraints

- Location is shared only within crews, never publicly.
- Not a navigation app; RDV tap hands off to the user's own maps app.
- Referral-only; no open signup.
- iPhone first, Android eventually, one cross-platform codebase (React Native with Expo). Web is out of scope. No Sign in with Apple; users register directly with email and password.
- Brand: dark, minimal, exclusive; Toronto car scene.
- "RDV" is always said R-D-V; write "an RDV".

## Workflow

- Specs in `docs/features/<name>.md`; every issue links its spec.
- Work items are GitHub Issues on this repo (`gh issue ...`), using the templates in `.github/ISSUE_TEMPLATE/`.
- Code PRs live in `Ahkh3e/rdv-garage` and reference `Ahkh3e/rdvgarage-spec#<n>`.
- When work completes, close the issue. Update `docs/roadmap.md` only if phase scope changes.
- Spec changes that alter a decision get a record in `docs/decisions/`.
