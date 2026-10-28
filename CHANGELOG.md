# Changelog

## 1.0.1

- `SetHeights` and `SampleHeights` reject non-finite rectangles, falloff distances and grid heights. Before, an infinite or NaN height in the grid would have been written into the base mesh. Samples without terrain keep using a value at or below -1e30.

## 1.0.0 release candidate

- Prepare a standalone Corivo Fab package from the 2026-10-18 backup.
- Add public documentation and explicit engine/plugin requirements.
- Keep native module names and MCP identifiers stable.

- Complete real asset/terrain creation, persistence and reload checks.
