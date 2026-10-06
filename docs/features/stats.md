# Stats and streaks

## Scope

Release 0.0.1 tracks live sessions only and shows the weekly top speed leaderboard. Meets attended, attendance, and streaks need RDVs and arrive after 0.0.1.

## Metrics

- Distance driven: summed across live sessions.
- Meets attended: RDVs where arrival is verified.
- Streak: consecutive weeks with at least one verified meet or live drive.

## Attendance

- Verified by location: the user arrives within the RDV radius during the RDV time window.
- Radius and window are set per RDV with sensible defaults.
- Attendance requires the user to be live, or to grant a one-time arrival check at the RDV.
- RSVP alone does not count.

## Rules

- Distance counts only while live, since location is only collected then.
- A missed week resets the streak. Grace rules are open.
- A session's stats are visible only to the crews the user shared that session with (privacy.md).

## Platform notes

- Distance comes from the location library during live sessions only.
- Arrival checks use region monitoring around the RDV on both platforms.
- Needs background location permission; same explanations as privacy.md.

## Open questions

- Streak grace days
- Leaderboards and their effect on safe driving. Release 0.0.1 adds a weekly top speed leaderboard (decision 0007); other speed-based stats are undecided
