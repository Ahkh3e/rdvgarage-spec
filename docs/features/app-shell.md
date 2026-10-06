# App shell and screens

## Navigation

Bottom tabs, each contributed by a module through the registry:

| Tab | Module | Content |
|---|---|---|
| Map | map (with live-location) | Shared map of selected crews; follow mode; Go live button |
| Crews | crews | My Crews home, crew detail, create and join |
| Board | leaderboard | Weekly top speed per crew |
| Me | accounts (with referral) | Profile, Share invite, Devices, Legal, Delete account |

The app opens on Map when the user is live, otherwise on Crews.

## Screens

| Screen | Module | Notes |
|---|---|---|
| Welcome, Enter invite code | accounts | Shown with no session |
| Invite expired | accounts | Expired, revoked, or disabled invite |
| Disclaimers and age | accounts | Must accept to continue |
| Handle and avatar | accounts | Creates the account |
| My Crews | crews | List, selection toggles, live status |
| Crew detail | crews | Members, link, owner actions |
| Create crew, Join crew | crews | Join opens from a crew link |
| Map | map | Selected crews' live members; recenter |
| Go live sheet | live-location | Pick crews, start, stop; shows who can see you |
| Board | leaderboard | Crew selector, ranked list, disclaimer footer |
| Me | accounts | Profile edit, Share invite, My invites |
| Devices | accounts | Sessions, Add a device (link and QR), revoke |
| Legal | accounts | Full disclaimers text |

## Core contracts

Small interfaces in the core package. Modules depend only on these, never on each other.

```ts
interface Session { userId: string; handle: string; signOut(): Promise<void> }
interface CrewContext { selected: CrewId[]; select(ids: CrewId[]): void; subscribe(fn): Unsubscribe }
interface LocationStream { subscribe(fn: (p: MemberPosition) => void): Unsubscribe }
interface Events { emit<T extends AppEvent>(e: T): void; on<T extends AppEvent>(type: T['type'], fn): Unsubscribe }
interface Backend { rpc(name: string, args?: object): Promise<unknown>; invoke(name: string, body?: object): Promise<unknown>; channel(name: string): RealtimeChannel }
interface Module { id: string; register(shell: Shell): void }
interface Shell { addTab(tab: Tab): void; addRoute(route: Route): void; addFlag(name: string, default: boolean): void }
```

App events used in 0.0.1: `session.started`, `session.ended`, `crew.selected`, `account.suspended`, `account.deleted`. On `account.suspended` the live-location module ends the session and the map drops the user; the server also ends the user's sessions, so the 5-minute sweep is only the backstop. Modules listen for the events they need; the emitter never knows who listens.

## Flags

Each module registers a flag, default on. A module with its flag off adds no tabs, routes, or handlers. Flags are read at launch from the Backend; the default is used if unreachable.

## Rules

- A module is added by adding its entry to the app's single module list, and removed by deleting that entry.
- Disclaimer acceptance gates account creation; the Go live sheet and Board show short disclaimers (`docs/disclaimers.md`).
- Empty and error states: every list has an empty state; every call shows the mapped error message (see `docs/api.md`).
