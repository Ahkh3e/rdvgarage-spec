# App shell and screens

## Navigation

Bottom tabs, each contributed by a module through the registry:

| Tab | Module | Content |
|---|---|---|
| Map | map (with live-location) | Shared map of selected crews; follow mode; Go live button |
| Crews | crews | My Crews home, crew detail, create and join |
| Rooms | chat | Chat rooms, last message and unread count; opens a room with messages and the walkie channel |
| Board | leaderboard | Weekly top speed per crew |
| Me | accounts (with referral) | Profile, Change password, Share invite, Devices, Legal, Delete account |

The app opens on Map when the user is live, otherwise on Crews.

## Screens

| Screen | Module | Notes |
|---|---|---|
| Welcome, Sign in, Forgot password, Enter invite code | accounts | Shown with no session |
| Invite expired | accounts | Expired, revoked, or disabled invite |
| Create account | accounts | Handle, email, password, optional avatar, disclaimers and age acceptance; creates the account |
| Confirm email | accounts | Shown after the form until the email is confirmed (skipped in test mode); Resend button; says unconfirmed accounts expire after 24 hours |
| Reset password | accounts | Opened by the reset link; sets a new password |
| My Crews | crews | List, selection toggles, live status |
| Crew detail | crews | Members, link, owner actions, the crew's upcoming RDVs |
| Create crew, Join crew | crews | Join opens from a crew link |
| Map | map | Selected crews' live members; recenter |
| Room, Create room, Room members | chat | Messages and composer, the Talk button and channel state; create and manage an invite-only room |
| Go live sheet | live-location | Pick crews, start, stop; shows who can see you |
| Board | leaderboard | Crew selector, ranked list, disclaimer footer |
| Me | accounts | Profile edit, Change password, Share invite, My invites, Maps app, Stats |
| Devices | accounts | Signed-in devices, revoke, sign out everywhere |
| Legal | accounts | Full disclaimers text |

## Core contracts

Small interfaces in the core package. Modules depend only on these, never on each other.

```ts
interface Session { userId: string; handle: string; signOut(): Promise<void> }
interface AuthApi { signIn(email: string, password: string): Promise<void>; requestPasswordReset(email: string): Promise<void>; completePasswordReset(newPassword: string): Promise<void>; changePassword(current: string, next: string): Promise<void>; resendConfirmation(email: string): Promise<void>; listSessions(): Promise<DeviceSession[]>; revokeSession(id: string | 'others'): Promise<void> }
interface CrewContext { selected: CrewId[]; select(ids: CrewId[]): void; subscribe(fn): Unsubscribe }
interface LocationStream { subscribe(fn: (p: MemberPosition) => void): Unsubscribe }
interface Events { emit<T extends AppEvent>(e: T): void; on<T extends AppEvent>(type: T['type'], fn): Unsubscribe }
interface Backend { auth: AuthApi; rpc(name: string, args?: object): Promise<unknown>; invoke(name: string, body?: object): Promise<unknown>; channel(name: string): RealtimeChannel }
interface Module { id: string; register(shell: Shell): void }
interface Shell { addTab(tab: Tab): void; addRoute(route: Route): void; addFlag(name: string, default: boolean): void }
```

App events used in 0.0.1: `session.started`, `session.ended`, `crew.selected`, `account.suspended`, `account.deleted`. On `account.suspended` the live-location module ends the session and the map drops the user; the server also ends the user's sessions, so the 5-minute sweep is only the backstop. Modules listen for the events they need; the emitter never knows who listens.

## Flags

Each module registers a flag, default on. A module with its flag off adds no tabs, routes, or handlers. In 0.0.1 flags come from the build configuration (`EXPO_PUBLIC_FLAGS`, a JSON object), with each module's default used when a flag is not set. Remote flags read from the Backend are a later option.

## Rules

- A module is added by adding its entry to the app's single module list, and removed by deleting that entry.
- Disclaimer acceptance gates account creation; the Go live sheet and Board show short disclaimers (`docs/disclaimers.md`).
- Empty and error states: every list has an empty state; every call shows the mapped error message (see `docs/api.md`).
