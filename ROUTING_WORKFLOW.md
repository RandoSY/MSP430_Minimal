# Routing workflow

The current board was rerouted from scratch with **Freerouting 2.5.0**, using KiCad 10.0.3's Specctra DSN/SES export and import. The component placement, schematic nets, and dedicated J4 Spy-Bi-Wire header were retained. The Freerouting run completed with all connections routed and then passed KiCad PCB DRC.

## Settings used

- Two signal layers: F.Cu and B.Cu; 45-degree routing.
- Default signal width: 0.50 mm; 3V3 and GND net class: 0.75 mm.
- Copper clearance: 0.20 mm; through vias: 0.60 mm diameter, 0.30 mm drill.
- The router used narrower short segments at some pad connections. Verify the imported tracks and fabrication rules rather than assuming every segment has the nominal width.
- Freerouting reported zero unrouted items and zero routing violations. The imported result had 189 track segments and two vias before subsequent silkscreen edits.

## Repeat the routing

1. Work on a **copy** of the project, including its local `UMA_MSP430.pretty` library. Preserve the current routed board as a backup. Removing existing tracks and vias is necessary for a fresh route.
2. In KiCad PCB Editor, export the unrouted copy as a Specctra DSN. Keep unique, nonempty references on the mounting-hole footprints (`MH1`–`MH4`): the DSN exporter rejected the earlier unnamed holes.
3. In Freerouting, load the DSN and set the default trace width to 0.50 mm, create a power class containing `3V3` and `GND` with 0.75 mm width, and use 0.20 mm clearance. Confirm both copper layers and the via dimensions. Run the autorouter and optimizer, and save a Specctra SES file.
4. Import the SES into the same unrouted board copy in KiCad. Inspect the resulting routing and verify that power traces and J4's pins are correct. Run KiCad DRC against the actual project rules; check all errors, unconnected items, pad clearances, and manufacturer-specific limits. Do not treat the router's own score as a substitute for KiCad DRC.
5. Review both silkscreen layers and pad openings after any new route or component change. Verify the physical parts, run schematic ERC, and inspect fabrication outputs before ordering.

For the October 2026 run the locally available `freerouting-2.5.0.jar` was run headlessly with `-de` for the DSN, `-do` for the SES, `-mp 30`, `-mt 2`, `-da`, and `--gui.enabled=false`. A separate local user-data directory was used. The DSN net classes were edited before routing to apply the widths above. The JAR was not copied into this project; install or locate Freerouting separately to repeat the run.
