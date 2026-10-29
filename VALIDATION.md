# Validation status

Practical checks recorded on 27 to 29 October 2026, using Unreal Engine 5.8.4 / Win64 in isolated local projects. Production projects and the original backup ZIPs were unchanged.

## Version 1.0.2 (29 October 2026)

- Package for the Fab technical review: source only. No Binaries, no LICENSE file, no test code, no levels or sample assets.
- Clean Win64 BuildPlugin with strict includes on the unpacked release ZIP, as Fab builds it: passed, no compiler warnings.
- Buyer install path: the plugin built from the ZIP was installed into a fresh Blueprint-only project; the editor mounted it, registered the toolset and answered `list_toolsets`, `describe_toolset` and `GetTerrains` over the native MCP server.
- The test suites used for earlier checks are internal and are no longer part of the package.
- `CreateTerrain`, `SetHeights` and `RebuildTerrain` at 64, 512, 1024 and 2048 quads per side, and 30-round bursts of create, edit, rebuild and delete, on DirectX 12 and DirectX 11 (NVIDIA GeForce RTX 4080 SUPER, driver 617.42): no GPU errors. A 4096 x 4096 request is refused with an error; with the previous limit such a terrain made the editor stop responding.

## Version 1.0.1 (28 October 2026)

- Clean Win64 BuildPlugin with strict includes: passed.
- Buyer install path: the release ZIP was unpacked into a fresh Blueprint-only project; the editor loaded the prebuilt binaries without recompiling, registered the toolset and answered `list_toolsets`, `describe_toolset` and `GetTerrains` over the native MCP server.
- All 44 automation tests passed on the rebuilt plugin (22 of them with expected diagnostic warnings).
- Practical check: a 40 x 40 m terrain was created; a height grid with a NaN sample was rejected with an explicit message, infinite values are rejected at the JSON boundary, and a finite grid still wrote 441 vertices and read back 900 cm.

## Version 1.0.0 (27 October 2026)

- Fresh editor loading, native MCP tool discovery and schema reads: passed.
- Existing automation suite: 44 passed, zero failed (22 tests include expected diagnostic warnings).
- Created a 100 m terrain, changed its centre to 900 cm, rebuilt and waited for completion.
- Saved and reopened the test level; the 900 cm height was retained.
- The test terrain was rendered and the resulting image was inspected.

## Scope of acceptance

These are reproducible technical and visual smoke checks on the named fixtures. They do not claim exhaustive compatibility or performance at arbitrary project scale. Mac, Linux, other engine versions and large production terrains are outside the validated scope. Third-party example assets used locally for testing are not included in the product package.

## Fab status

Version 1.0.0 was submitted for review. Version 1.0.1 is prepared as an update; product approval, submission and publication are separate from these tests.
