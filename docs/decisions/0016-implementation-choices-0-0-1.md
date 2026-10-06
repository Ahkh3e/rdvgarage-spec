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
- **The previous week's final values are written to the previous week.** When the Toronto week changes mid-session the app sends the old week's last values with the old week's start date (allowed for the current and previous week only), then starts the new week from zero.
- **A parked member keeps broadcasting.** Location updates stop when the car is parked, so the app re-sends the last position every 15 seconds. "Moving" expires after a few seconds without a fix.
- **Imprecise fixes are not used for speed or distance.** A reading with a horizontal accuracy worse than 100 metres still places the marker but never counts toward the top speed. This is data quality, not a speed limit (decision 0007).
- **Reset links must be asked for on this phone.** A reset link carries session tokens, so the app only accepts one within an hour of requesting a reset on the same phone. A link anyone else crafted is ignored. The reset page on the web remains available for other cases.
- **Broadcast positions carry no verified sender.** Trust within a crew is by design. The receiver ignores positions for anyone who is not a member of the crew the message arrived on, and ignores everything until the crew list has loaded.
- **Sharing follows crew membership.** Leaving or being removed from a crew a person is live to stops sharing with that crew immediately; with no crews left the session ends.
- **Presence is announced once the channel is ready**, and again after a reconnect.
- **Operator server runs the toolkit and the scheduled jobs**; the keep-alive and backups are shell scripts with cron (decision 0015).

## Consequences

`docs/SETUP.md` in the code repo lists every account and key to supply. A device test on a real phone is still to be done through an EAS build.
