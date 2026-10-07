# Maps handoff

## Goal

Tapping Directions on an RDV, a place or a pin opens the person's maps app at that place. Rendezview shows no route, ETA or turn-by-turn (decisions 0004 and 0006).

## Behavior

- Waze is the default on both iPhone and Android.
- The handoff always passes the coordinates. It passes the label where the app's link supports it (Apple Maps), URL-encoded. It never passes the person's position, a crew, or a handle.
- Me, Maps app lets the person choose Waze, Apple Maps (iPhone only) or Google Maps. The choice is stored on the device.
- If the chosen app is not installed, the app offers the others that are, and a choice made at that moment can be remembered.
- If none is installed, the handoff opens the place in the phone's browser using the Waze web link, and the card says it can be opened in any maps app.

## Links used

| App | Link |
|---|---|
| Waze | `https://waze.com/ul?ll=<lat>,<lng>&navigate=yes` |
| Apple Maps | `https://maps.apple.com/?daddr=<lat>,<lng>&q=<label>` |
| Google Maps | `https://www.google.com/maps/dir/?api=1&destination=<lat>,<lng>` |

The Waze universal link opens the app when installed and its web page otherwise. Installed apps are detected through the platform's allowed URL schemes (iPhone query schemes, Android package visibility).

## Rules

- No ETAs, routing, or route lines inside Rendezview (decision 0006).
- Only the place coordinates leave the app, and only to the app the person picked.
- A cruise hands off its start point only.
- A private event's place is handed off only to members who can see it (rdvs.md).

## Platform notes

- iPhone: Waze and Google Maps are probed through query schemes declared in the app config; Apple Maps is always present.
- Android: the same apps are probed through package queries declared in the app config.

## Open questions

- Whether the default should follow the phone's own default maps app instead of Waze.
