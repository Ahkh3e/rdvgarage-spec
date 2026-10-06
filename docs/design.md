# Design

## Brand

Dark, minimal, exclusive. Toronto car scene.

## Principles

- Dark-first UI; no light theme in v1 (decision 0001).
- Restraint: neutrals plus one accent (decision 0002).
- The map is the hero surface.
- Copy is terse. Say "an RDV", never "a RDV".
- Disclaimers are visible and plain, never hidden in small grey text.

## Color tokens

| Token | Value | Use |
|---|---|---|
| background | #0A0A0B | App background |
| surface | #131316 | Cards, sheets |
| raised | #1C1C21 | Inputs, selected rows |
| border | #2A2A31 | Dividers |
| text | #F4F4F5 | Primary text |
| muted | #8C8C96 | Secondary text |
| accent | #FF4F1F | The single accent: primary actions, your own marker, live state |
| danger | #FF453A | Destructive actions and errors only |

Crew markers are the one exception to a single accent. Each crew gets one of six fixed, desaturated tints plus a distinct shape so crews are told apart without relying on color. Tints are used only for crew markers and crew labels, never for interface chrome.

## Typography

- Interface: Inter, regular and semibold.
- Numbers (speeds, distances, ranks): JetBrains Mono, so columns align.
- Both are free to use. System font fallback.
- Sizes: 12 caption, 14 body, 16 default, 20 title, 28 large title.

## Map

- Dark map on both platforms: Apple Maps dark appearance on iPhone, a dark custom style on Google Maps for Android.
- Member markers: avatar in a circle ringed with the crew tint; your own marker uses the accent.
- Stale members fade, then disappear (architecture.md).
- No route lines (decision 0006).

## Components

- Go live: a large accent button on the Map; once live it becomes a Stop control with a "visible to" chip listing the crews.
- Sheets for Go live, crew actions, and Share invite.
- Lists have an empty state and a short line of guidance.
- Primary action per screen: one accent button at most.

## Motion

Short and functional: 150 to 250 ms eases, no decorative animation. The map follows the user smoothly; rank changes on the Board fade in place.

## Icons and marker

- One icon set, outline style, single weight.
- RDV marker is defined with the RDVs feature; not needed for 0.0.1.
