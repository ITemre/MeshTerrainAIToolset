# Corivo Mesh Terrain Toolset: agent guide

## Copy this setup prompt to your assistant

```text
Set up the purchased Corivo Mesh Terrain Toolset plugin for my Unreal Engine 5.8 project.
Find the .uproject and ask me to select it if there are several. Verify the plugin
is already installed through Fab or in Project/Plugins/MeshTerrainToolset.
Enable the plugin and its declared dependencies without replacing other settings.
Enable ModelContextProtocol, restart when necessary, and connect to the editor's
native Streamable HTTP MCP server on its configured loopback port.
Run list_toolsets and describe_toolset; verify these identifiers:
MeshTerrainToolset.MeshTerrainToolset
Read https://github.com/ITemre/MeshTerrainToolset/blob/main/README.md and the tool reference.
Start with read-only inspection in a disposable project and show me the first result.
```

## Working rules

- Inspect before changing content. Read `describe_toolset` rather than guessing arguments.
- Work on duplicates when changing meshes, bones, weight data or package references.
- Stop PIE before editor asset mutations; PIE probing is read-only unless explicitly documented.
- Check dirty levels before any workflow that opens or saves another level.
- Save only the intended assets. A tool success is not visual or gameplay acceptance.
- Keep asset paths and file paths distinct. `/Game/...` is a package path, not a Windows filename.
- Request operation-specific approval from your user for destructive changes or redistribution of assets.

## Product-specific constraints

- Requires Epic's experimental Mesh Partition plugin in UE 5.8. Mesh Terrain and Landscape are different systems.
- No terrain, heightmap dataset, project level or material pack is included.
- This integration uses Mesh Partition editor interfaces and an engine Internal include path. Compatibility must be rechecked for each engine update.
- Heightmap inputs are files on the editor machine. An MCP client running elsewhere must make those files available locally.
