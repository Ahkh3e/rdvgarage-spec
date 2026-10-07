# RDVs

## Goal

A crew member drops an RDV: a place and a time their crews can see, plan around and show up to. The app does not organize, supervise or insure any gathering (disclaimers.md).

## Behavior

- Any member of a crew can create an RDV for one or more of their crews. The creator is the host.
- Kinds: meet (park and hang out), cruise (a drive that starts at a point), private event.
- An RDV ends at its end time, or three hours after its start when no end time is set. Every rule below that says the RDV ends, its window ends, or a past RDV uses this one value.
- Fields: title (3-60 characters), kind, place, start time, optional end time, optional note (up to 280 characters), the crews it is for, and the arrival radius.
- Place is a point picked on the map, found by search, or carried over from a pin (places.md), with a short name. A cruise has a start point only; there is no route or destination line (decision 0006).
- The arrival radius defaults to 150 m and the host can set 50 to 500 m.
- The host can edit or cancel their RDV. A crew owner can cancel any RDV for their crew. A change to the place or time notifies people who answered Going or Maybe.
- A cancelled RDV stays visible, marked cancelled, until its window passes, so people who planned around it see why it is gone.

## RSVP

- Each member answers Going, Maybe or Can't, and can change it until the RDV ends. No answer is shown as no answer.
- The RDV shows counts and the list of members for each answer, to members of its crews only.
- RSVP is a plan, not attendance. It never counts toward stats (stats.md).

## Private events

- A private event is visible in the list to members of its crews, but its exact place stays hidden until the member answers Going or Maybe. Before that they see the title, kind, time and the area name only.
- Its map pin appears only for members who answered Going or Maybe, and the host.
- This keeps a private place within the people who chose to come, which is narrower than the crew (decision 0004 only sets the outer limit).

## Where it appears

- The Crews tab crew detail lists the crew's upcoming RDVs. A new Plans screen under the Map tab lists upcoming RDVs across the selected crews, soonest first, with a Past section.
- A pin shows on the map for each RDV of the selected crews from the moment it is created until its window ends. The pin is a ring in the crew colour with the Feather `flag` glyph, and the title appears under it when zoomed in (design.md). A cancelled RDV has no pin.
- Where an RDV belongs to more than one selected crew, the pin takes the colour of the crew that sorts first, as markers do (car-icons.md).
- Tapping a pin or a list row opens the RDV detail: title, kind, host, place, time, note, RSVP control, who is going, and a Directions button (maps-handoff.md). The detail may show the straight-line distance from the person's own position to the place, such as "1.2 km away", worked out on the device and never sent anywhere. It never shows an ETA, a driving distance or a route (decision 0006). A private event with a hidden place shows no distance.
- An RDV that is happening now shows a Live badge in lists and on its pin.

## Arrival

Arrival marks a member as having attended and feeds Meets attended and streaks (stats.md).

- The attendance window starts one hour before the start time and ends when the RDV ends.
- Arrival is verified by location: the member is inside the RDV radius during the window. Nothing else counts.
- If the member is live to a crew the RDV is for, the app notices on the device that their position is inside the radius during the window and then calls record_arrival once with that reading. The server makes the real check; the device check only decides when to call.
- If the member is not live, they can tap I'm here on the RDV detail inside the window. The app takes one position reading and calls record_arrival; the server checks it against the radius and the window, then discards the position. If the reading is outside the radius, nothing is recorded and the app says so. Nothing is collected in the background for a member who is not live.
- On arrival a prompt can offer Go live for this RDV? (privacy.md). Accepting opens the Go live sheet with the RDV's crews already chosen; the person still taps Start, and the offer starts nothing by itself.
- Arrival is recorded once per member per RDV. It is visible only to members of the RDV's crews and is not removed when the RDV is later edited.
- The host's own arrival counts the same way.

## Rules

- No ETAs, routing, or route lines (decision 0006).
- RDVs are visible only to members of the crews they were made for. A member who leaves a crew stops seeing its RDVs and their RSVP stops counting (it returns if they rejoin), but their recorded arrivals remain for their own stats.
- Creating an RDV is not limited in number, per the no-limits stance on invites; there is no cap per crew or per member.
- An RDV cannot be made for a crew the member is not in. It cannot start in the past.
- The place and its radius are stored; no member position is stored for an RDV (privacy.md).
- Disclaimers apply: the create screen shows the short line that meets and cruises are organized by users and attended at one's own risk (disclaimers.md item 5).
- Speed is never part of an RDV.

## Platform notes

- The place picker and pins use the same MapLibre map and RDV Night style on both platforms (decision 0017). Place search, pins and the nearby list are in places.md; search sends only typed text (decision 0024).
- Local reminders for RDVs a member answered Going or Maybe are scheduled on the device before the start time and cancelled when the RDV is cancelled or the answer changes. They need the notification permission already asked at first Go live (notifications.md).
- An RDV being dropped, changed or cancelled reaches a member whose app is closed only through remote push (notifications.md). Until that exists, they see it on opening the app.

## Data and API

Tables are in data-model.md (`rdvs.rdvs`, `rdvs.crews`, `rdvs.rsvps`, `rdvs.arrivals`); callable functions are in api.md.

## Open questions

- Whether a cruise should allow an ordered list of stops later. Not in this release.
- Whether recurring RDVs (a weekly meet) are needed.
- Streak grace days stay open in stats.md.
