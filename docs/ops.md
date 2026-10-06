# Operator toolkit

0.0.1 includes an operator toolkit that runs on the owner's hosted Linux server. It manages users and crews directly and can generate synthetic users and fake activity, so the whole product can be exercised without real people. It is operator tooling, not an app feature, and it is never exposed to the internet (decision 0015).

## Principles

- Command line only. No web endpoint, no API route, no hidden app path. Reaching it requires SSH to the server.
- It uses the Supabase service role key, which can bypass every access policy. The key lives only in a root-readable environment file on the server, never in git, CI, or the app. Compromise of the server is compromise of all data, so the server is locked down (key-only SSH, firewall, minimal packages).
- Every record it creates is tagged synthetic and can be wiped in one command. Real users and crews are never touched by synthetic commands.
- Each command writes an audit line: time, operator, environment, command, arguments, and result. Secrets are never logged.
- Destructive commands support a dry run that prints what would change.
- Code lives in the `rdv-garage` repo under `ops/` (TypeScript on Node, same toolchain as the app).

## Environment guard

The config names the environment: `development`, `test`, or `production`.

- In `development` and `test`, all commands run freely.
- In `production`, commands that create synthetic data, or delete or suspend real users, require an explicit production flag and typing the project name to confirm. Synthetic users are never added to a crew owned by a real user in production.
- Test mode registration (decision 0014) is separate and enforced by the `register` function, not by this toolkit.

## Commands

| Command | What it does |
|---|---|
| `user create [--count N]` | Create synthetic users with random handles prefixed `sim_`, confirmed, no email sent, random passwords; optional `--invited-by` sets the referral chain; credentials written to a local file (mode 600) on request |
| `user delete <handle>` / `--synthetic` | Run the full deletion path from architecture.md for one user, or all synthetic users |
| `user suspend` / `restore <handle>` | Ban or unban the auth user, revoke invites per accounts.md |
| `user show <handle>` | Profile, status, referral chain, crews, invites |
| `invite list <handle>` / `disable <id>` | View a user's invites and disable one |
| `crew create` | Synthetic crew with `--owner`, `--members N`, random names |
| `crew delete <id>` | Delete a crew (any crew by id; real crews need typing the name) |
| `crew add` / `remove <id> <handle>` | Change membership |
| `sim invites` | Create invites and have synthetic users register through them, building referral chains |
| `sim live` | Simulate live sessions for chosen crews (below) |
| `sim leaderboard` | Write random segments for the current week so the Board has data |
| `purge-synthetic` | Delete all synthetic users, crews, sessions, and segments |
| `status` | Counts of users, crews, live sessions, synthetic share, plus a database ping |

## Simulator

`sim live` makes fake members behave like real ones so a real phone sees them on the map:

- Starts sessions through the normal functions (`start_session`) with the chosen crews.
- Joins each crew's private realtime channel as the synthetic user and broadcasts positions along random routes in the Toronto area at the normal cadence (every 3 seconds moving, 15 seconds stationary).
- Writes checkpoints about once a minute with random plausible speeds and distances, so the leaderboard fills in.
- Ends sessions on stop, or lets some go stale on purpose to exercise the 5-minute sweep.
- Options: number of users, crews, route style (city, highway), and whether to include a session that crosses the week boundary.

Because it uses the same paths as the app, it also load-tests realtime message volume against the free tier.

## Synthetic data rules

- `is_synthetic` marks profiles and crews. Sessions, segments, and memberships belong to a synthetic owner or user and go with them.
- Synthetic emails use the reserved `.invalid` domain, so no email can be delivered by accident.
- Synthetic users can be added to real test crews in `development` and `test` so a real tester can interact with fake members.
- Real users never see anything that identifies a user as synthetic beyond the `sim_` handle.

## Server jobs

The same server runs the scheduled jobs from architecture.md instead of GitHub Actions: the keep-alive ping and the database backup (read-only role, encrypted, rotated, with a copy kept off the server). This avoids GitHub disabling idle scheduled workflows.

## Open

- Where the second copy of backups is kept
