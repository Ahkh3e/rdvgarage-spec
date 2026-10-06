# Process

Keep it simple and consistent. One fact lives in one place; everything else links to it.

## Where things live

| Thing | Home |
|---|---|
| What and why of the product | `docs/product.md` |
| Behavior of a feature | `docs/features/<name>.md` |
| System design and flows | `docs/architecture.md` |
| Tables and policies | `docs/data-model.md` |
| Callable surface | `docs/api.md` |
| Visual rules | `docs/design.md` |
| Safety and legal wording | `docs/disclaimers.md` |
| Why a choice was made | `docs/decisions/NNNN-*.md` |
| Scope of a release | `docs/releases/<version>.md` |
| Work to do or done | GitHub Issues and milestones |

## Changing something

1. If it changes a decision, write or amend a decision record first. Mark the old one amended or superseded.
2. Edit the one home for the fact. Other docs link to it or restate it only in a summary.
3. Search for stale terms (below) and fix every hit in the same PR.
4. Update issues if scope moved. Roadmap changes only when phase scope changes.

## Canonical terms

- Share invite: a 24-hour invite link a member creates. Not "referral link" or "personal link".
- Crew link: the reusable link that adds an existing member to a crew.
- Go live: starting a live session for chosen crews.
- Session, segment: a live session; its per-week summary.
- RDV: always said R-D-V; write "an RDV".
- Units: km/h.

## Superseded terms to search for

Sign in with Apple, sign-in link, device link, recovery link, no password or email, one-time code, quota, signup limit, active-invite limit, ETA, MapKit, Core Location, Swift package, iOS-only wording in shared specs.

```
grep -rniE "sign in with apple|sign-in link|device link|recovery link|no password|one-time code|quota|signup limit|active-invite|mapkit|core location|swift" docs CLAUDE.md README.md
grep -rnwE "ETA|ETAs" docs CLAUDE.md README.md
```

Hits are fine only where a decision record explains the removal.

## Pull requests

- One topic per branch and PR; branch from `main`.
- Before merging, run `/code-review` on the PR, fix what it finds, and merge when the remaining findings are cosmetic.
- Squash merge, delete the branch.
- Commit and PR text follow the attribution lines in `CLAUDE.md` guidance.

## Issues

- Template per kind: Feature, Bug, Task. Every issue links its spec or says why none.
- Labels: feature, bug, task. Milestones: one per release or phase.
- No dates, deadlines, or time estimates anywhere.

## Quality bar for a spec

- States behavior, rules, platform notes, and open questions.
- No open question that blocks a release remains when the release is declared specced. A named build spike with a defined fallback is not a blocker.
- Disclaimers and privacy impact are stated wherever location or speed appears.
