# Stats and streaks

## Scope

Release 0.0.1 tracks live sessions only and shows the weekly top speed leaderboard. Meets attended, attendance, and streaks need RDVs and arrive after 0.0.1.

## Where it shows

Me has a Stats row. It shows Meets attended, counted from the person's own verified arrivals (rdvs.md), including arrivals at RDVs of crews they have since left. Streaks are shown there once their grace rule is decided.

## Metrics

- Distance driven: summed across live sessions.
- Meets attended: RDVs where arrival is verified.
- Streak: consecutive weeks with at least one verified meet or live drive.

## Attendance

- Verified by location: the user arrives within the RDV radius during the RDV time window.
- Radius and window are defined in rdvs.md (Arrival).
- Attendance requires the user to be live, or to tap I'm here for a one-time arrival check at the RDV.
- RSVP alone does not count.

## Rules

- Distance counts only while live, since location is only collected then.
- A missed week resets the streak. Grace rules are open.
- A session's stats are visible only to the crews the user shared that session with (privacy.md).

## Platform notes

- Distance comes from the location library during live sessions only.
- Arrival is checked on the device against live positions, or by a single reading from I'm here. There is no region monitoring for members who are not live.
- Needs background location permission only while live; same explanations as privacy.md.

## Open questions

- Streak grace days
- Leaderboards and their effect on safe driving. Release 0.0.1 adds a weekly top speed leaderboard (decision 0007); other speed-based stats are undecided
