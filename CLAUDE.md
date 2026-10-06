# RDV Garage Spec

Spec, design, and issue tracking for https://github.com/Ahkh3e/rdv-garage. No app code here.

## Product constraints

- Location is shared only within crews, never publicly.
- Not a navigation app; RDV tap hands off to the user's own maps app.
- Referral-only; no open signup.
- iPhone first, Android eventually, one cross-platform codebase (React Native with Expo). Web is out of scope. Accounts have no password or email: join by a 24-hour share invite, sign in on other devices with a 24-hour sign-in link (decision 0012). No Sign in with Apple.
- Brand: dark, minimal, exclusive; Toronto car scene.
- "RDV" is always said R-D-V; write "an RDV".

## Safety

- No speed limits or safety rules in the product; safety is handled by prominent disclaimers (`docs/disclaimers.md`).
- Invites have no limits.
- No timelines, dates, or time estimates in specs or issues.

## Workflow

- Follow `docs/process.md`: one fact one home, canonical terms, stale-term search, `/code-review` before merge.

- Specs in `docs/features/<name>.md`; every issue links its spec.
- Work items are GitHub Issues on this repo (`gh issue ...`), using the templates in `.github/ISSUE_TEMPLATE/`.
- Code PRs live in `Ahkh3e/rdv-garage` and reference `Ahkh3e/rdvgarage-spec#<n>`.
- When work completes, close the issue. Update `docs/roadmap.md` only if phase scope changes.
- Spec changes that alter a decision get a record in `docs/decisions/`.
