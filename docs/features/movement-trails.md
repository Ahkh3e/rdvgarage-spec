# Movement trails

## Goal

A short tail behind each live member and behind you, so direction and recent movement are readable at a glance, the way a navigation app draws the road just driven.

## Behavior

- Each live member has a trail of the path they just took, in the colour of their car (car-icons.md): yours takes your car colour, blue by default; theirs takes their car colour, or the crew tint when they have not chosen one.
- The trail runs along the road, the same width as the road under it at the current zoom, with a bright neon glow: the trail uses a more saturated, lighter version of the car colour so it stands out on the dark map, with a wide soft glow and a bright core. It is strongest at the member and fades toward the tail.
- Corners are rounded, never cut across blocks. The phone matches positions to the road lines it has already drawn for the visible map and follows the road's bends between positions. Where no road is loaded (zoomed out, or off road), the trail is a smoothed line.
- A gap in time or a jump in distance starts a fresh trail, so a lost signal never draws a long line across the city.
- A member who stops sending updates keeps a fading marker but loses their trail.

## Rules

- The trail is the last 450 metres of path, by distance, never by time, so its length cannot hint at how fast someone is going (decision 0007 for the speed rules).
- Trails exist only in memory on each phone, built from positions already shared with the crew. Nothing is stored, uploaded or kept after the app closes (privacy.md). There is no route history.
- No ETAs, routing or planned route lines (decision 0006). A trail is the past, never a prediction.
- A phone also sends an extra position when a driver turns a corner, so other phones can draw corners. This stays inside the normal message budget (architecture.md).

## Simulation

- Fake drivers and simulator phones drive loops snapped to real roads, so trails and snapping can be tested without cars (docs in the code repo: `docs/LOCAL_RUN.md`).
