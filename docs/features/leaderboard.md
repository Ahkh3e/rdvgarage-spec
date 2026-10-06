# Weekly top speed leaderboard

## Goal

A simple, social, per-crew ranking that gives crews a reason to open the app each week (decision 0007).

## Behavior

- Board tab shows one crew at a time. A selector at the top lists the user's selected crews.
- Ranked list for the current week: rank, avatar, handle, top speed, and the day it was set. The user's own row is highlighted.
- Each member's entry is their highest session max speed this week from sessions they shared with that crew (privacy.md). A crew the user did not share with never sees it.
- Ties share a rank; the earlier time ranks first within the tie.
- Speeds are shown in km/h in a monospaced numeral style.
- A week starts Monday 00:00 America/Toronto. The header says "This week" and "Resets Monday".
- A session that crosses the week boundary counts in each week by when readings were recorded (architecture.md).
- Empty state: "No sessions this week. Go live to get on the board."
- Footer on every view: the leaderboard disclaimer (`docs/disclaimers.md`).

## Rules

- Speed comes from the device as measured. There are no limits or plausibility checks (decision 0007).
- Speed is shown after the session, never live on the map.
- Previous weeks, history, and badges are not in 0.0.1.
- Disabling the leaderboard flag removes the Board tab with no effect on other modules.

## Platform notes

- Speed comes from the same location stream as live sessions on both platforms; no separate permission.

## Open questions

- A previous-week view and a streak or badge system, after 0.0.1
