# Feature set

iPhone first, Android eventually, one codebase (decisions 0005, 0011). One spec per feature in this folder; each links its issues. Status lives in issues. 0.0.1 scope is in `docs/releases/0.0.1.md`.

## Phase 1 - MVP

| Feature | Spec | Summary |
|---|---|---|
| App shell | app-shell.md | Tabs, screens, core contracts, flags |
| Invites and onboarding | invites.md | Join by invite link or code; share invites that expire after 24 hours, no limits; referral chain |
| Accounts | accounts.md | Account creation by invite with email and password, devices, deletion |
| Profile | profile.md (planned) | Handle, avatar, home area; minimal in v1 |
| Crews | crews.md | Create and join private crews; roles; member limits; leave and remove |
| Live map | live-map.md | Crew members live on a shared map; follow mode while driving |
| Movement trails | movement-trails.md | A short tail behind each live member, along the road |
| Car icons and markers | car-icons.md | Pick a minimal racecar icon; how a point looks on the map |
| Leaderboard | leaderboard.md | Weekly top speed per crew |
| RDVs | rdvs.md | Drop an RDV (meet, cruise, private event) with place, time, crew; RSVP; arrival by location |
| Places | places.md | Search an address, drop crew-visible pins, nearby places; Directions hands off |
| Maps handoff | maps-handoff.md | Directions opens Waze by default, or Apple Maps or Google Maps |
| Stats and streaks | stats.md | Distance driven, meets attended, streaks |
| Notifications | notifications.md | A notification while you are live and when a friend goes live (local in 0.0.1; remote push later); RDV drops, RSVPs, invites later |
| Privacy controls | privacy.md | Manual live sessions with per-crew sharing; ghost mode and per-RDV windows later |

## Platform integrations

- Background location: Always on iPhone, background permission with a persistent notification on Android (0.0.1)
- Push notifications (Phase 1)
- Home screen widget: next RDV (Phase 2)
- CarPlay and Android Auto: crew map and next RDV (Phase 3)

## Phase 2

| Feature | Spec | Summary |
|---|---|---|
| Car profiles | car-profiles.md (planned) | Garage of cars per user with specs and photos |
| Event galleries | galleries.md (planned) | Photos per RDV, crew-only |

## Phase 3

| Feature | Spec | Summary |
|---|---|---|
| Convoy mode | convoy.md (planned) | Ordered convoy with lead and tail, gap alerts |
| Minigames | minigames.md (planned) | Location-based games within crews |

## Next

Phase 1 specs are written; profile is covered by accounts.md in 0.0.1.
