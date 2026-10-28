# Validation status

Practical checks recorded on 27 and 28 October 2026, using Unreal Engine 5.8.4 / Win64 in isolated local projects. Production projects and the original backup ZIPs were unchanged.

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
