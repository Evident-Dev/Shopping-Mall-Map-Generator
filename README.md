# Shopping Mall Map Generator

Board setup helper for the shopping mall board game. Use it here:
https://evident-dev.github.io/Shopping-Mall-Map-Generator/

## Generating a map

1. Type a seed, or click **Generate new seed**.
2. Set stores, escalators and tunnel pairs. The Halls box just shows the total: the generator always finds the fewest halls that fit the request for that seed. Tick **Lock large store to plaza** to keep the big stores on the plazas.
3. Click **Generate**, then **Save**. **Starter mall** gives the first-game layout, which is what the page opens on.

## Seeds

A seed is any text. The same seed and settings always produce the same map, so share the seed to play the same board again.

## Reading the map

- Blue edge = open, red edge = wall.
- Escalator arrows show the one-way direction.
- Tunnels with the same letter connect.

## Generation rules

### Core

- **Plazas.** Two 4×4 plazas placed as a mirrored pair, one left and one right of the board's centre line. The gap between them is 2 or 3 hall lengths. It is 3 whenever the hall count is 6 or more, and always 3 for the starter mall.
- **Corridors.** Two rows of 2×1 halls run between the plazas, one along the top edge of the plazas and one along the bottom. These corridor halls plus the plazas are the core and count as depth 0.
- **Big stores.** Exactly four, numbered 1 to 4, placed once the halls exist. Each takes a random legal spot: its 4-cell door edge sits either on a plaza's top, bottom, or outer side, or across exactly two end-to-end halls, corridor or spur. When a spot is taken its mirror is used next if that is legal too. With **Lock large store to plaza** ticked only plaza sides count. The starter mall always uses two per plaza, top and bottom.

### Halls

- **Minimum hall count.** Every generation searches up from the smallest possible skeleton (the 4 corridor halls). The hall count is raised by one and the map regenerated until every request fits, so each seed gets its own minimum. The Halls box only displays the total of the kept map.
- **Connection.** Every spur hall must have at least one side touching a hall, plaza, or escalator, and that neighbour's facing edge must be open (blue). Touching a wall edge does not count.
- **Long side rule.** Two halls may never touch long side to long side. A hall may attach its short end to another hall's long side, or short end to short end.
- **One per long side.** No more than one hall may attach anywhere along a given hall's long side.
- **Plazas.** A hall may only touch a plaza with its short end. A hall lying flat along a plaza edge is never placed, because it removes storefronts instead of adding them.
- **Mirroring.** When a spur is placed, its left/right mirror is also placed if that spot is empty, legal, and the hall count allows it.
- **No empty halls.** Every hall must end up with at least one store or the security room on one of its sides. Escalators are exempt because nothing can attach to them. For a given hall count several redraws are tried and the one with the fewest empty halls is kept.

### Stores and security

- **Doors.** A store or security room is 2×1. Its blue edge must sit fully against hall or plaza cells. Both cells of that edge must touch a hall or plaza, and an escalator's side wall never counts.
- **Fill order.** Each piece has a depth, 0 for the core and one more for each spur out from it. Stores take the lowest depth available first. At the same depth, a hall that has no store yet is chosen before a hall that already has one. Ties are random per seed.
- **Security.** Placed before any store, so it always lands on a corridor hall, above or below it, chosen by the seed.
- **Store mirroring.** When a store is placed, its left/right mirror is placed too if that spot is empty and is also a legal storefront.
- **Reserved cells.** A store never takes a cell that is being held for a tunnel at an escalator's end.

### Escalators

- **Corridor first.** Escalators are made by converting existing halls. Corridor halls convert first. They convert as pairs, with the partner at the mirrored position in the other corridor row, running the opposite direction, so the pair forms a loop around the middle rather than a trap in it. Any remaining count converts single corridor halls.
- **Spur escalators.** If more are needed than the corridor holds, spur halls convert, horizontal or vertical. A spur only converts if nothing hall-like touches its long sides, because an escalator's long sides become walls and would cut that neighbour off.
- **Chains.** Escalators may touch end to end, but every escalator in a touching chain runs the same direction. A new escalator takes the direction of any escalator it touches. If the touching escalators disagree, or run across it instead of along it, that hall is not converted.
- **Ends.** Both open ends of an escalator must lead into a hall, plaza, escalator, or tunnel through that piece's open edge. A spur whose end faces a wall or the board edge is never converted. A spur whose end faces empty space converts only while there is tunnel budget left, and that empty cell is reserved for a tunnel.

### Tunnels

- **Pairs.** Tunnels come in lettered pairs A, B, C, maximum 3. Matching letters connect. Each tunnel is a 1×1 piece in an empty cell whose one open edge faces the open edge of a walkable piece.
- **Escalator ends first.** Cells reserved at escalator ends get their tunnels before any random spot is used. A partner prefers another waiting escalator end, then the mirrored position, then any legal spot.
- **Spacing.** Any two tunnels, from any pair, are at least 6 cells apart measured as grid steps.

### Master rule

Every store, big store, security room, plaza, hall, escalator, and tunnel must be reachable from every other one. Movement passes through open edges only. Escalators may only be walked in the direction of their arrows. Tunnels connect to their twin. A map where any place can be entered but not left, or left but not entered, fails.

### Acceptance

A map is kept only when it contains every requested store, escalator, and tunnel pair, no escalator end leads nowhere, and the master rule holds. When a map fails, the same hall count is redrawn several times, and if none pass the hall count is raised and the process repeats. Same seed and settings always produce the same map.
