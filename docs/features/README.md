# Feature set

iPhone first (see decision 0005). One spec per feature in this folder; each links its issues. Status lives in issues.

## Phase 1 - MVP

| Feature | Spec | Summary |
|---|---|---|
| Invites and onboarding | invites.md | Join by invite code or link; per-user invite quota; referral chain |
| Profile | profile.md | Handle, avatar, home area; minimal in v1 |
| Crews | crews.md | Create and join private crews; roles; member limits; leave and remove |
| Live map | live-map.md | Crew members live on a shared map; follow mode while driving; pause and ghost controls |
| RDVs | rdvs.md | Drop an RDV (meet, cruise, private event) with place, time, crew; RSVP |
| Maps handoff | maps-handoff.md | Tap an RDV to open Apple Maps, Google Maps, or Waze |
| Stats and streaks | stats.md | Distance driven, meets attended, streaks |
| Notifications | notifications.md | RDV drops, RSVPs, member arriving, invites |
| Privacy controls | privacy.md | Per-crew sharing, pause, ghost mode, sharing window per RDV |

## iOS integrations

- Background location with Always authorization (Phase 1)
- Push notifications (Phase 1)
- Home screen widget: next RDV (Phase 2)
- CarPlay: crew map and next RDV (Phase 3)

## Phase 2

| Feature | Spec | Summary |
|---|---|---|
| Car profiles | car-profiles.md | Garage of cars per user with specs and photos |
| Event galleries | galleries.md | Photos per RDV, crew-only |

## Phase 3

| Feature | Spec | Summary |
|---|---|---|
| Convoy mode | convoy.md | Ordered convoy with lead and tail, gap alerts |
| Minigames | minigames.md | Location-based games within crews |

## Next

Write the Phase 1 specs in dependency order: invites, crews, privacy, live-map, rdvs, maps-handoff, stats, notifications, profile.
