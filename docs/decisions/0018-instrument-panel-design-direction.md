# 0018 Instrument panel design direction

Status: accepted

## Decision

Adopt the "instrument panel at night" direction in `docs/design.md`: a refined neutral ramp with translucent hairlines, Inter plus Inter Tight with tabular numerals, one radius family, glass for floating layers only, Feather icons, and the accent reserved for signals. Feature wording and behavior are unchanged.

## Rationale

The first build looked like a prototype: opaque boxed everything, orange used for labels and icons so it signalled nothing, mixed radii and icon styles, and a loud default look. The direction draws on Dieter Rams (as little design as possible), Linear (few theme inputs, a neutral ramp, a display cut for titles), Material's dark theme (elevation by lighter surface, desaturated accents), and Apple's materials guidance (glass for the functional layer, not for content).

## Consequences

- Typefaces change: Inter Tight is added for titles and numerals; JetBrains Mono is kept only for codes.
- New native dependencies: a blur view and haptics. A new development build is needed.
- The accent rules in `design.md` are binding: one primary accent button per screen.
- Disclaimer wording and placement rules in `docs/disclaimers.md` are unchanged; only their styling changes.

## Revisit

If glass hurts legibility over the map in daylight, or the display face reads as too tight on small phones.
