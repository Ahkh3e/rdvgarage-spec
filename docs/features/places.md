# Places: search, pins and nearby

## Goal

The map works like a normal map for finding things: search an address or place, drop a pin that the crew can see, and see what is nearby. Tapping a place or pin hands off to Waze (maps-handoff.md). It is not navigation (decision 0006).

## Search

- A search field sits on the Map, top of the screen. It finds addresses, intersections, and named places such as stores, restaurants, garages and parking lots (for example a specific shop or a chain by name). Typing shows matching results as a list, each with its name, kind and street address; choosing one moves the map there and shows a place card.
- The place card shows the name, the address, Directions (handoff), Drop pin, and Make an RDV.
- Search sends only the typed text and a coarse bias point (decision 0024). Results rank by match first, then by closeness to the coarse bias point (the map area on screen), so a chain name lists its nearest branches first. They are never ranked by where crew members are.
- Recent searches are kept on the device only.
- No results shows an empty state with the query and a line suggesting a shorter search or dropping a pin by hand.

## Dropping a pin

- Long press anywhere on the map, or choose Drop pin on a place card. The pin takes a label (3-40 characters, defaulting to the address or place name) and an optional note (up to 140 characters).
- The dropper chooses which of their crews can see it, from the crews selected on the map. At least one is required.
- A pin is visible only to members of the chosen crews. It shows as a small pin glyph in the crew colour with its label when zoomed in, and is a different shape from an RDV pin (rdvs.md).
- A pin expires 24 hours after it is dropped. The dropper can remove it sooner, and a crew owner can remove any pin in their crew.
- Tapping a pin opens its card: label, note, who dropped it, address, Directions, Make an RDV. Make an RDV opens the RDV create screen with the place filled in.
- There is no limit on pins.
- A pin is a place, not a person. It never shows who is near it or how far anyone is from it.

## Nearby

- A Nearby control on the map lists places around the map center by category: fuel, food, coffee, parking, car wash, EV charging. Categories come from the points of interest in the map tiles and are read on the device.
- Picking a category lists the closest places in order of straight-line distance from the map center, and shows them on the map. No driving distance or time is shown (decision 0006). Place cards, pin cards and the pin and RDV rows in the crew sheet also show the straight-line distance from the person's own position, worked out on the device and never sent anywhere.
- Tapping a result opens its place card, with Directions and Drop pin.
- Nearby is a browse by category; search is for a specific name. Both end at the same place card.
- Nearby works with the map centered anywhere, so it never needs the person's own position or sends it anywhere.

## Directions

Directions on any place card or pin calls the maps handoff with the place coordinates and label. The handoff rules, including which app opens, are in maps-handoff.md.

## Rules

- Location is shared only within crews (decision 0004): a pin reaches only the crews its dropper chose.
- No ETAs, routing, or route lines (decision 0006).
- Search and nearby never send a member's precise position to anyone.
- The place card, the pin sheet and the RDV create screen show the short line in disclaimers.md (Placement).

## Platform notes

- Same MapLibre map and RDV Night style on both platforms; long press and pin rendering are the same.
- The points-of-interest layer is on in the style but drawn quietly (design.md).

## Data and API

`places.pins` and `places.pin_crews` in data-model.md; `drop_pin`, `remove_pin` and `list_pins` in api.md. Search goes through the `search_places` function behind the core `Geocoder` contract; nearby has no server call and is read from the loaded tiles.

## Open questions

- Whether a pin can be kept past 24 hours as a saved crew spot.
- Whether nearby needs categories beyond the six listed.
