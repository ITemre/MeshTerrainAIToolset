# Validation status

Practical checks recorded on 27 October 2026, using Unreal Engine 5.8.4 / Win64 in isolated local projects. Production projects and the original backup ZIPs were unchanged.

## Technical and practical checks

- Clean Win64 BuildPlugin with strict includes: passed.
- Fresh editor loading, native MCP tool discovery and schema reads: passed.
- Existing automation suite: 44 passed, zero failed (22 tests include expected diagnostic warnings).
- Created a 100 m terrain, changed its centre to 900 cm, rebuilt and waited for completion.
- Saved and reopened the test level; the 900 cm height was retained.
- The test terrain was rendered and the resulting image was inspected.

## Scope of acceptance

These are reproducible technical and visual smoke checks on the named fixtures. They do not claim exhaustive character compatibility or performance at arbitrary project scale. Mac, Linux, other engine versions, large production Landscapes and all possible third-party rigs are outside the validated scope. Third-party example assets used locally for testing are not included in the product package.

## Fab status

Prepared as a release candidate. Product approval, submission and publication are separate from these tests.
