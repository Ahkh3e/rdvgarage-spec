# 0015 Operator toolkit on the hosted server

Status: accepted

## Decision

0.0.1 includes a command line operator toolkit that runs on the owner's hosted Linux server. It manages users and crews directly and generates synthetic users and fake activity for testing. The same server runs the keep-alive and backup jobs. There is no admin UI and no network endpoint for any of it.

## Rationale

A solo, fast-moving build needs to exercise the whole product, including live maps and the leaderboard, without recruiting testers. A server-side toolkit does that with the same code paths the app uses. Running scheduled jobs on the server also avoids GitHub disabling idle workflows.

## Consequences

- The service role key sits on the server in a root-readable file. Compromise of the server is compromise of all data, so it is locked down (key-only SSH, firewall, minimal software).
- Everything the toolkit creates is tagged synthetic and wipeable in one command.
- A production guard and an audit log limit mistakes. Production use needs an explicit flag and the typed project name.
- Because the toolkit can bypass access policies, it must never be exposed as an HTTP route or hidden app feature. That would be a real backdoor.
- Backups use a read-only role, so a lost backup key does not grant write access. A copy goes to free object storage off the server so a lost disk does not lose the only copy.

## Revisit

If more than one operator needs access, add per-operator credentials and a proper admin surface.
