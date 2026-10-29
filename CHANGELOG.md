# Changelog

## 1.0.2

- `CreateTerrain` and `ImportHeightmap` accept at most 2048 x 2048 quads (before 4096 x 4096), the largest size verified on DirectX 12 and DirectX 11. Larger requests are refused with an error instead of reaching the GPU.
- Requirements name DirectX 12 as the default RHI for Epic's experimental Mesh Partition system.
- Package for the Fab technical review: source only, without `Binaries` (Fab compiles the plugin), and without `Documentation/LICENSE.md`. The plugin is covered by the Fab Standard License alone.
- Pure plugin: internal test code (automation tests, Python test suites and test runners) is no longer shipped. No levels or sample assets are included. `SetWorldOverrideForTests` and the test-only `ImageWrapper` dependency are removed.

## 1.0.1

- `SetHeights` and `SampleHeights` reject non-finite rectangles, falloff distances and grid heights. Before, an infinite or NaN height in the grid would have been written into the base mesh. Samples without terrain keep using a value at or below -1e30.

## 1.0.0 release candidate

- Prepare a standalone Corivo Fab package from the 2026-10-18 backup.
- Add public documentation and explicit engine/plugin requirements.
- Keep native module names and MCP identifiers stable.

- Complete real asset/terrain creation, persistence and reload checks.
