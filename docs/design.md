# Design

## Brand

Dark, minimal, exclusive. Toronto car scene.

## Principles

- Dark-first UI; no light theme in v1 (decision 0001).
- Restraint: neutrals plus one accent (decision 0002).
- The map is the hero surface.
- Copy is terse. Say "an RDV", never "a RDV".
- Disclaimers are visible and plain, never hidden in small grey text.

## Direction

"Instrument panel at night" (decision 0018). The map is the hero, the chrome is glass, and the one blue is a signal rather than decoration. Elevation comes from lighter surfaces, not shadows. Alignment and consistency over ornament: every border, radius and size comes from a token.

## Color tokens

Black and blue, with cool neutrals. The reference for the look is a charcoal map with outlined dark cards, round badge markers and a single glowing accent.

| Token | Value | Use |
|---|---|---|
| background | #080A0F | App background |
| s1 | #0E1118 | Grouped containers |
| surface | #12161E | Cards, solid fallback for glass |
| raised | #181D27 | Selected rows |
| hairline | rgba(150,170,210,0.10) | Row dividers |
| border | rgba(150,170,210,0.18) | Card, input and chip outlines, glass edge |
| focus | rgba(76,141,255,0.70) | Focused input border only |
| text | #F2F5FA | Titles, values |
| muted | #A3ABBA | Secondary lines |
| subtle | #6B7385 | Captions, placeholders; never essential text |
| accent | #2F6FF2 | Fill for the primary button and switches (white label) |
| accentBright | #5EA0FF | Small marks: own marker, live dot, active-tab indicator, rank 1 bar, focus, links |
| accentPressed | #2559C4 | Pressed accent fill |
| onAccent | #FFFFFF | Label on an accent fill |
| danger | #FF453A | Error text and destructive labels only, never a fill |

Accent rules: one primary accent button per screen. The bright blue is for small marks only: your own map marker, the live indicator, the active-tab indicator, the focus ring, links and the rank 1 mark on the Board. Tab labels, list icons, section titles and chip borders stay neutral. A disabled primary button is a neutral tint. Secondary buttons are outlined, not filled.

Crew markers are the one exception to a single accent. Each crew gets one of six fixed, desaturated tints plus a distinct shape so crews are told apart without relying on color. Tints are used only for crew markers, a small dot and crew labels, never for interface chrome.

## Typography

- Interface: Inter at 400, 500 and 600.
- Titles and numerals: Inter Tight at 600 and 700, with tabular figures so columns align. Negative tracking on display sizes.
- Codes only (invite code): JetBrains Mono.
- All are free to use. System font fallback.
- Scale: hero 40, large title 32, title 22, headline 16 semibold, body 15, callout 14, caption 12, overline 11 uppercase with wide tracking, numeral 20, large numeral 56.
- Titles are left-aligned. No more than three sizes on a screen.

## Spacing, shape, material

- Spacing on a 4 and 8 grid: 4, 8, 12, 16, 20, 24, 32, 48. Screen gutter 20. Touch targets at least 44.
- One radius family: 10 small, 14 for buttons and inputs, 22 for cards and floating panels, 28 for sheets, full round for chips and the Go live button. Continuous corners.
- Floating layers (tab bar, map sheet, map controls, modal sheets) are blurred glass with a hairline edge. Rows and cards inside them stay flat. When Reduce Transparency is on, glass becomes a solid surface.

## Map

- One custom map style on both platforms, "RDV Night": a monochrome charcoal-blue night map with near-black ground, grey roads that get lighter and wider as they get bigger (highways near white), no coloured parks or water, quiet labels, and subtle 3D buildings when close in (decisions 0017 and 0019). The only colour on the map is the blue accent and the crew tints on markers.
- The map takes three quarters of the screen with a crew sheet underneath. Scrolling the member list squeezes the map to a quarter; tapping a live member jumps to them and opens the map back up.
- Buttons on the map: zoom in, zoom out, a 2D and 3D switch (flat style without extruded buildings), and a recenter button. They are glass round buttons.
- Follow mode is tilted about 55 degrees in 3D, close behind the person, and turns with the road. The camera keeps the last heading when they stop. Dragging the map ends follow mode; the recenter button brings it back. Tapping the live pill fits everyone who is live.
- Attribution for OpenStreetMap and OpenMapTiles stays visible.
- Tapping a live member in the list or a marker opens a floating card over the map with their avatar, handle, crew and a Follow button; Follow keeps the camera on them until the map is dragged.
- Member markers: the member's chosen racecar icon, top-down and rotating with heading, on a round dark badge ringed in the crew tint, with an outlined name pill underneath (crew dot and handle). See `docs/features/car-icons.md`. Your own marker is an accent arrow while following, an accent dot with a soft halo otherwise.
- Stale members fade, then disappear (architecture.md).
- No route lines (decision 0006).

## Components

- Go live: a pill-shaped accent button on the Map; once live it becomes a glass pill with a pulsing dot, the crews it is visible to, and a Stop control.
- Lists are grouped: one container, rows separated by inset hairlines, 64 high, no box around each row.
- Chips are 32 high with a small crew dot; selected chips lighten, they do not take the accent.
- Empty states are left-aligned with an overline, a title, one line of body and stacked actions.
- The Board shows a flat podium for the top three with large tabular numerals, then restrained rows; only rank 1 carries an accent mark.
- Sheets for Go live, crew actions, and Share invite.
- Lists have an empty state and a short line of guidance.
- Primary action per screen: one accent button at most.

## Motion

Short and functional: 100 ms press, 160 ms fades, 240 ms sheet and content, 320 ms screens; never over 400. Presses scale to 0.98. The map follows the user smoothly; rank changes on the Board fade in place. The live dot pulses slowly and holds still when Reduce Motion is on. Haptics: a light tick on tab change and on selecting a member, a light impact on the primary action, a success tap when going live.

## Icons and marker

- One icon set (Feather), outline style, single stroke weight.
- RDV marker is defined with the RDVs feature; not needed for 0.0.1.
