# Car icons and map markers

## Goal

Each person picks a minimal racecar icon that represents them on the map. Members are recognised by their car, and the map reads as a field of cars rather than a field of profile photos.

## Behavior

- Everyone has a car icon. The default is the GT, so nobody starts without one.
- The person chooses their icon in Me, Car. A picker shows every model in the set at a readable size, on a dark tile, with the current choice marked. Changing it applies at once and is visible to the crews they share with on the next position update.
- Icon choice is a profile field, visible to members of shared crews like the handle and avatar. It is not visible to anyone else.
- Photos (avatars) stay in lists, the member card and the Board. The map uses the car.

## The icon set

The flat silhouettes below are the picker tiles and the list glyph. On the map each model is built in 3D from the same shapes (decision 0023).

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

- A member on the map is a small 3D model of their chosen car, in the map itself like a Waze vehicle, facing the way they are travelling (heading, or the direction of travel when the phone reports none). A parked car keeps its last heading. Decision 0023.
- The body is tinted in the person's car colour (below), or the crew colour when they have not chosen one, with a dark window band, a lighter roof, dark wheels, and a wing where the model has one. Where a person is in more than one selected crew, the colour is that of the crew that sorts first. Crews are also told apart by the crew dot in the name pill (design.md).
- A name pill sits under the car: a small crew-colour dot and the handle.
- Your own car is the bright blue accent with a soft pulsing halo under it.
- A member who is not live is not on the map at all: no car, no name pill and no trail. The crew list shows them as offline.
- Tapping a marker or a list row opens the member card (live-map.md). The card shows the avatar and handle, with the car icon at the right of the title row.
- The crew list row shows the avatar photo, the handle and crew, a small glyph of their car, and their live state or speed (decision 0022).

## Car colour

- Under Me, Car there is a Colour row below the icon picker: a palette of neon colours (blue, cyan, green, lime, yellow, orange, red, pink, purple, white) and Crew colour, which is the default. Changing it applies at once and shows to the crews they share with on the next position update, like the icon.
- The car body, the trail and the marker halo use the chosen colour. The name pill keeps the crew-colour dot, so crews are still told apart. Your own car is the bright blue accent until you choose another colour.
- A colour is a profile field visible to members of shared crews, like the handle and car icon. It is chosen from the fixed palette only; there are no free colour inputs. Decision 0029.

## Rules

- No brand or trademark marks in any icon.
- The icon never reveals anything beyond what the person chose.
- Icons are chosen from the fixed set only; free uploads are out of scope.

## Data

- `accounts.profiles.car_icon`: text, one of the keys above, not null, default `gt`. `update_profile` accepts it (data-model.md, api.md).
- `accounts.profiles.car_color`: text, one of the palette keys in Car colour, nullable (null means the crew colour). `update_profile` accepts it.

## Open questions

- Whether a crew can have its own icon style later.
- Whether a crew can have its own icon style later.
- Whether people can pick a custom colour outside the palette.
