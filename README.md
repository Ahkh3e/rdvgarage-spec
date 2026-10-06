# RDV Garage Spec

Spec, design, and work tracking for RDV Garage. Code lives in [Ahkh3e/rdv-garage](https://github.com/Ahkh3e/rdv-garage).

## Layout

- `docs/product.md` - vision, users, principles, privacy model
- `docs/features/` - one spec per feature
- `docs/design.md` - brand and design system
- `docs/architecture.md` - system design and stack
- `docs/roadmap.md` - phases and status
- `docs/decisions/` - decision records
- Issues - all work items, tracked here, not in the code repo. Labels: `feature`, `bug`, `task`. Milestones: one per roadmap phase.

## Workflow

1. Write or update the feature spec in `docs/features/`.
2. Open an issue from a template, linking the spec.
3. Implement in `rdv-garage`; the PR references `Ahkh3e/rdvgarage-spec#<n>`.
4. On merge, close the issue. Update `docs/roadmap.md` only if phase scope changes.
