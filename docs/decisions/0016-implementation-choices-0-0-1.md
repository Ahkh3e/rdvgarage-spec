# 0016 Implementation choices for 0.0.1

Status: accepted

Choices made while building 0.0.1 that refine earlier decisions. None changes a product rule.

## Decisions

- **Expo SDK 57 and EAS cloud builds.** SDK 57 needs Xcode 26.4 or newer for local iOS builds. Builds, TestFlight, and device testing go through EAS. Store submission also needs a current Xcode SDK.
- **Flags come from build configuration**, not from the backend, in 0.0.1. Removing a module is still a flag or one entry in the module list.
- **Sessions are listed and revoked by SQL functions** that read the auth sessions table for the caller, rather than Edge Functions. Fewer moving parts and the same result.
- **Change password returns a fresh session.** Supabase can end the caller's own session on an admin password change, so the function signs the caller in again and hands the new session back before signing out the other devices.
- **Avatar after sign-in.** The registration form does not upload a photo; the person adds one from Edit profile once signed in.
- **Rate limits.** Per network address: 60 registrations an hour, 120 invite checks an hour, 20 unknown invite codes an hour. Per email: 5 registration attempts an hour, counted only for requests that pass validation and the invite check. The network address is the platform's `cf-connecting-ip`, else `x-real-ip`, else the last `x-forwarded-for` entry, because a client controls the front of that header.
- **Checkpoint values are cumulative for the current week segment.** The app restarts them from zero when the Toronto week changes. NaN and Infinity are rejected because they would hold first place all week and could not be corrected; there are still no speed limits (decision 0007).
- **The terms version lives in one place**, the database settings; the register function reads it.
- **Crew avatars** are not in 0.0.1; the data model has the column.
- **Operator server runs the toolkit and the scheduled jobs**; the keep-alive and backups are shell scripts with cron (decision 0015).

## Consequences

`docs/SETUP.md` in the code repo lists every account and key to supply. A device test on a real phone is still to be done through an EAS build.
