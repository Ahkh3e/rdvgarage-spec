# Car icons and map markers

## Goal

Each person picks a minimal racecar icon that represents them on the map. Members are recognised by their car, and the map reads as a field of cars rather than a field of profile photos.

## Behavior

- Everyone has a car icon. The default is assigned at account creation (the first model in the set, then rotating) so nobody starts without one.
- The person chooses their icon in Me, Car. A picker shows every model in the set at a readable size, on a dark tile, with the current choice marked. Changing it applies at once and is visible to the crews they share with on the next position update.
- Icon choice is a profile field, visible to members of shared crews like the handle and avatar. It is not visible to anyone else.
- Photos (avatars) stay in lists, the member card and the Board. The map uses the car.

## The icon set

Eight models, drawn as top-down silhouettes in a single weight with no brand marks, logos or real model names. Generic names only.

| Key | Name | Character |
|---|---|---|
| gt | GT | Long hood, fastback coupe |
| formula | Formula | Open wheel, wide rear wing |
| proto | Prototype | Enclosed cockpit, long tail |
| rally | Rally | Boxy hatch with a roof scoop |
| muscle | Muscle | Wide, flat, blunt nose |
| hyper | Hypercar | Low wedge, split wing |
| drift | Drift | Compact coupe, stance wide |
| kart | Kart | Small, open, minimal |

- All icons share a drawing grid so they sit at the same size and weight. Each is a single vector shape, so it can be tinted and rotated.
- More models can be added later without changing anything but the set and the profile check.

## The marker (how a point looks)

- A member on the map is their car icon seen from above, pointing the way they are travelling (heading, or the direction of travel when the phone reports none). A parked car keeps its last heading.
- The icon is white-grey on a round dark badge. The badge has a thin ring in the crew tint, so crews can still be told apart without relying on color alone (design.md). Where a person is in more than one selected crew, the ring uses the crew that sorts first.
- A name pill sits under the badge: a small crew-tint dot and the handle.
- Your own marker uses your own car, the same badge, with the bright blue accent ring and a soft pulsing halo instead of a crew tint. It replaces the arrow while following.
- A member who has stopped sending updates fades the whole marker, and their trail is cleared (movement trails).
- Tapping a marker or a list row opens the member card (live-map.md). The card shows the avatar and handle, with the car icon at the right of the title row.
- The member card and the crew list show the avatar photo; the car icon is shown in the list row as a small glyph beside the live state.

## Rules

- No brand or trademark marks in any icon.
- The icon never reveals anything beyond what the person chose.
- Icons are chosen from the fixed set only; free uploads are out of scope.

## Data

- `accounts.profiles.car_icon`: text, one of the keys above, not null, default `gt`. `update_profile` accepts it (data-model.md, api.md).

## Open questions

- Whether a crew can have its own icon style later.
- Whether people can colour their car (crew tint is currently the only colour).
