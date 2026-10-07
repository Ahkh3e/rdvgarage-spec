# 0023 3D car models on the map

Status: accepted

## Decision

Members are drawn on the map as small 3D racecar models, the way Waze draws vehicles, instead of the flat badge with a top-down icon. Each model is built from the map's own 3D shapes (body, a dark window band, a roof, wheels, and wings where the model has them), so it tilts, turns and is lit with the buildings around it. The body is tinted in the crew colour, and your own car is the bright blue. Cars face the way they are travelling and glide to each new position. Details in `docs/features/car-icons.md`.

## Rationale

The owner asked for everyone to look like Waze's 3D cars. Building the models from map shapes needs no 3D asset pipeline, works the same on both platforms, and keeps the car correctly tied to the road under the tilted camera.

## Consequences

- Amends decision 0021: the marker is the 3D model. The flat top-down silhouettes remain as the preview tiles in the picker and as the small glyph in the crew list.
- Crews are told apart by body colour and the dot in the name pill. The ring around a badge no longer exists.
- Cars are drawn at a readable size when zoomed out, and life size at street level.
- Cars are a fixed size larger than life, which never changes with zoom, so a pinch never rescales the models; when zoomed out too far to read a car, the map shows a coloured dot for it.
- A member who is not live is not drawn at all (no dimmed or idle state).
- The shapes are simple. Richer models (rounded bodies, lights, a different shape per brand) would need a real 3D asset pipeline and a custom renderer, which is a later step.

## Revisit

If the simple shapes are not enough of a likeness, move to authored 3D models.
