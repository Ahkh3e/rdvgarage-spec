# Stats and streaks

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
- Stats are visible to the user and to their crews; crew leaderboards are open.

## Open questions

- Whether non-live driving counts toward distance (it can't if we never collect it)
- Streak grace days
- Leaderboards and their effect on safe driving. Release 0.0.1 adds a weekly top speed leaderboard (decision 0007); other speed-based stats are undecided
