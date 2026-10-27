# Corivo Mesh Terrain Toolset: troubleshooting

| Symptom | Check |
| --- | --- |
| Plugin is missing | Install the purchased plugin into the correct engine or project Plugins directory. |
| Module cannot load | Use UE 5.8 / Win64 and rebuild the source plugin with the supported toolchain. |
| Toolset is missing | Enable all declared plugins, restart, then inspect Python and plugin errors in the Output Log. |
| MCP cannot connect | Confirm the native server is running, its port, the client transport and loopback address. |
| An argument is rejected | Refresh `describe_toolset` and follow its runtime schema. |
| Assets or classes are missing | Supply the required project assets and enable the engine dependency that owns the class. |
| An operation succeeds but looks wrong | Inspect it in the editor and undo or restore your duplicate/backup before continuing. |

## Before filing an issue

Reproduce in a fresh test project, record the minimal operation sequence, and include the engine and plugin versions. Do not attach your entire paid project, credentials or third-party assets.

## First smoke check

Create a small terrain in a disposable level, inspect its information, sample base heights, change a small grid, rebuild and wait for completion. Compare base and built heights and inspect the viewport before working on a production level.
