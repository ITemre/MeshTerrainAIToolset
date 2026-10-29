# Mesh Terrain AI Toolset

Create and inspect Mesh Terrain, import heightmaps and manage terrain modifiers through Unreal Editor's native MCP server.

Public documentation for the commercial Fab plugin from Corivo. This repository contains documentation only. The plugin source, binaries and project files are distributed separately through Fab.

<!-- AI_SETUP_PROMPT_START -->
## Set up with your AI assistant

Copy the whole block below into an AI assistant that can run commands, edit files on your computer,
and connect to MCP servers. It checks the installation, enables the required plugins and connects itself.

```text
Please set up the "Mesh Terrain AI Toolset" Unreal Engine plugin for me and connect yourself to it.

1. Find my Unreal project (.uproject). Ask me to select it if there are several. It must use
   Unreal Engine 5.8 on Windows. Preserve unsaved editor work before editing settings or restarting.
2. Check that the purchased plugin is installed: either in the engine under
   Engine/Plugins/Marketplace or as <Project>/Plugins/MeshTerrainToolset. If it is missing, stop and
   tell me to install it from Fab first.
3. Enable these plugins in the .uproject "Plugins" list (add entries with "Enabled": true and
   preserve all other settings): MeshTerrainToolset, ToolsetRegistry, MeshPartition,
   ModelContextProtocol.
4. Start the editor's native MCP server with -ModelContextProtocolStartServer, or enable auto start
   in Project Settings > Plugins > Model Context Protocol and restart safely. Confirm the server's
   configured port. The default endpoint is http://127.0.0.1:8000/mcp. Verify it belongs to this project.
5. Add that endpoint to your own MCP configuration as an HTTP (Streamable HTTP) server named
   "unreal", then reload your MCP connection if needed. Preserve existing server entries; use a
   different name if "unreal" already points to another project. Keep the endpoint on loopback.
6. Call list_toolsets and confirm every identifier below is listed. Call describe_toolset for each:
   MeshTerrainToolset.MeshTerrainToolset
7. Read the agent guide at
   https://github.com/ITemre/MeshTerrainAIToolset/blob/main/AI_AGENT_HANDOFF.md
   (also shipped as Documentation/AI_AGENT_HANDOFF.md) and follow the tool reference and project rules.
8. Use read-only terrain inspection for the first check. Do not create, delete, edit or rebuild
   terrain during setup.
   Tell me in two sentences which project and endpoint are connected, whether all required toolsets
   are available, and what I can ask you to do next.
```

<!-- AI_SETUP_PROMPT_END -->

## Features

- Create and list Mesh Partition terrain actors.
- Read terrain bounds, resolution, build state and modifiers.
- Import heightmaps and add or remove supported modifiers.
- Sample base or built terrain heights and edit base height grids.
- Rebuild terrain and wait for asynchronous build completion.

## Requirements

- Unreal Engine 5.8, Windows 64-bit editor. Other engines and platforms are not claimed.
- Engine plugins: **ToolsetRegistry**, **MeshPartition**, **ModelContextProtocol**.
- Mesh Terrain is Epic's experimental Mesh Partition system. Use DirectX 12 as the default RHI (Project Settings > Platforms > Windows > Default RHI) and a current GPU driver.
- Terrains are limited to 2048 x 2048 quads. Verified on an NVIDIA GeForce RTX 4080 SUPER (driver 617.42) with DirectX 12 and DirectX 11.
- An HTTP MCP-compatible assistant or client. The client and any paid AI service are separate from this product.
- Editor only. The tools do not run in a packaged game.

## Installation and MCP connection

1. Install the product from Fab when available, or copy the purchased `MeshTerrainToolset` plugin folder to `<Project>/Plugins/`. The package ships Win64 editor binaries for UE 5.8 and the source; a C++ toolchain is only needed if the editor asks to rebuild the plugin.
2. Enable `MeshTerrainToolset` and its dependencies under **Edit > Plugins**, plus **Model Context Protocol**. Restart the editor.
3. Start the editor's native MCP server with `-ModelContextProtocolStartServer`, or enable auto start under **Project Settings > Plugins > Model Context Protocol**.
4. Connect your client using Streamable HTTP. The default endpoint is `http://127.0.0.1:8000/mcp`; confirm the port in the editor settings. Keep it on loopback.
5. Call `list_toolsets`, then `describe_toolset` for the identifiers listed below. Use the returned input schemas for your installed engine.

```json
{"mcpServers":{"unreal":{"type":"http","url":"http://127.0.0.1:8000/mcp"}}}
```

The JSON example is a common client configuration, not a universal format. Follow your client's setup instructions.

## Toolset identifiers

- `MeshTerrainToolset.MeshTerrainToolset`

See the [captured runtime schemas](RUNTIME_SCHEMAS.md) for the verified wire-format arguments.

## First workflow

Create a small terrain in a disposable level, inspect its information, sample base heights, change a small grid, rebuild and wait for completion. Compare base and built heights and inspect the viewport before working on a production level.

The [data types](DATA_TYPES.md) describe structured arguments. The [agent guide](AI_AGENT_HANDOFF.md) includes a setup prompt and working rules. The [tool reference](TOOL_REFERENCE.md) documents every exposed operation. Read [troubleshooting](TROUBLESHOOTING.md) before using the tools on production content.

## Limitations

- Requires Epic's experimental Mesh Partition plugin in UE 5.8. Mesh Terrain and Landscape are different systems.
- No terrain, heightmap dataset, project level or material pack is included.
- This integration uses Mesh Partition editor interfaces and an engine Internal include path. Compatibility must be rechecked for each engine update.
- Heightmap inputs are files on the editor machine. An MCP client running elsewhere must make those files available locally.

## Validation and release availability

This documentation accompanies release preparation. It is not a claim that Fab has approved this product or that visual, gameplay or performance acceptance is complete. See [VALIDATION.md](VALIDATION.md) for the latest recorded technical checks. Until a Fab product link is added here, this product has no confirmed public purchase link.

## Support

Report reproducible problems through [GitHub issues](https://github.com/ITemre/MeshTerrainAIToolset/issues). Include the engine version, plugin version, tool name, minimal arguments and error text. Remove credentials and private project data from reports.

## Licence

The commercial plugin is intended for sale under the [Fab Standard License](https://www.fab.com/eula), with Personal and Professional price tiers. Public documentation does not make the plugin open source or grant rights to redistribute it. Copyright 2026 Corivo. All rights reserved.
