# Mesh Terrain AI Toolset: tool reference

Operation names and descriptions below are taken from the selected plugin source. Query `describe_toolset` for the exact runtime schema, required fields and defaults. C++ metadata can alter wire names; use the runtime schema. Python toolset names retain their module and class names.

## MeshTerrainToolset

### `GetTerrains`

```text
static TArray<UE::MeshPartition::AMeshPartition*> GetTerrains()
```

The kinds of modifier that can be added to a terrain. */
UENUM(BlueprintType)
enum class EMeshTerrainModifierType : uint8
{
	/** Pulls all vertices within a radius to the height of the modifier. */
	Patch,
	/** Patch with many instance locations sharing one set of parameters. */
	InstancedPatch,
	/** Displaces vertices along the modifier's Z axis by a height texture and writes weight channels from textures. */
	TexturePatch,
	/** Raises or lowers the terrain along a spline: ridges, channels, hills and pockets. */
	Spline,
	/** Displaces the terrain with procedural noise. */
	Noise,
	/** Cuts a mesh out of the terrain or merges one into it. */
	Boolean,
	/** Deforms the terrain inside a control lattice. */
	Lattice,
	/** Remeshes or tessellates the triangles inside the modifier's box. */
	Remesh,
	/** Remeshes or tessellates the triangles near a spline. */
	SplineRemesh,
	/** Projects the terrain onto a mesh. */
	MeshProject,
	/** Holds layers of sculpted detail and projects them onto the terrain. */
	SculptLayers,
	/** Writes a radial profile with falloff to a weight channel. */
	WeightUtility,
};

/** How SetHeights combines the given heights with the existing terrain. */
UENUM(BlueprintType)
enum class EMeshTerrainHeightBlend : uint8
{
	/** The given heights are absolute world Z values. */
	Replace,
	/** The given heights are offsets added to the current world Z. */
	Add,
};

/** Summary of a terrain, returned by GetTerrainInfo. Counts cover loaded actors only. */
USTRUCT(BlueprintType)
struct FMeshTerrainInfo
{
	GENERATED_BODY()

	/** The terrain actor. Moving it moves its sections and attached modifiers. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	TObjectPtr<UE::MeshPartition::AMeshPartition> Terrain;

	/** Actor label shown in the outliner. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	FString Label;

	/** World bounds of the base sections (cm). Invalid if the terrain has no loaded section. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	FBox Bounds = FBox(ForceInit);

	/** Number of base sections, the meshes the terrain is built from before modifiers apply. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	int32 NumSections = 0;

	/** Total vertex count of the base sections. Bounds the detail SetHeights can represent. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	int32 NumVertices = 0;

	/** Number of modifiers, not counting base sections. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	int32 NumModifiers = 0;

	/** Asset that defines the material, weight channels and priority layers. Null if none is assigned. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	TObjectPtr<UE::MeshPartition::UMeshPartitionDefinition> Definition;

	/** Weight channels modifiers can write to. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	TArray<FName> Channels;

	/** Priority layers in application order. Modifiers in later layers apply on top of earlier ones. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	TArray<FName> PriorityLayers;

	/** True while the visible surface is being rebuilt after a change. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	bool bIsBuilding = false;
};

/** One modifier of a terrain, returned by GetModifiers. */
USTRUCT(BlueprintType)
struct FMeshTerrainModifierInfo
{
	GENERATED_BODY()

	/** The modifier component. Its settings are regular properties. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	TObjectPtr<UE::MeshPartition::UModifierComponent> Modifier;

	/** Actor hosting the modifier. Moving it moves the modifier. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	TObjectPtr<AActor> Actor;

	/** Class name of the modifier, for example NoiseModifier. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	FString ClassName;

	/** True for base sections. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	bool bIsBaseSection = false;

	/** Priority layer the modifier applies in. None if it has not been assigned one. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	FName PriorityLayer;

	/** Order within the priority layer. Higher applies later. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	double Priority = 0.0;

	/** True if the modifier is skipped when the terrain is built. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	bool bIsDisabled = false;

	/** World bounds of the region the modifier can affect (cm). */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	FBox Bounds = FBox(ForceInit);
};

/** A regular grid of terrain heights over a world-space rectangle. */
USTRUCT(BlueprintType)
struct FMeshTerrainHeightGrid
{
	GENERATED_BODY()

	/** World XY of the first sample (cm). */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	FVector2D Min = FVector2D::ZeroVector;

	/** World XY of the last sample (cm). */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	FVector2D Max = FVector2D::ZeroVector;

	/** Samples along X and Y, evenly spaced from Min to Max inclusive. */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	FIntPoint NumSamples = FIntPoint(2, 2);

	/**
World Z per sample (cm), row by row: index = Y * NumSamples.X + X.
A value at or below -1e30 means no terrain at that sample.
/
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	TArray<float> Heights;
};

/**
Creates and edits Mesh Terrain (Mesh Partition) in the current level: terrains and their base
sections, heightmap import, the non-destructive modifier stack, and reading and writing heights as
grids. Modifier settings are plain properties of the returned modifier components.
/
UCLASS(BlueprintType, MinimalAPI)
class UMeshTerrainToolset : public UToolsetDefinition
{
	GENERATED_BODY()

public:
	/**
Returns the terrains loaded in the current level.
@return The terrain actors, or an empty array if the level has none.

### `GetTerrainInfo`

```text
static FMeshTerrainInfo GetTerrainInfo(UE::MeshPartition::AMeshPartition* Terrain)
```

Returns size, section and modifier counts, definition and build state of a terrain.
@param Terrain The terrain to describe.

### `CreateTerrain`

```text
static UE::MeshPartition::AMeshPartition* CreateTerrain( const FVector2D& Size, const FIntPoint& Resolution, const FVector& Location = FVector::ZeroVector, int32 MaxTrianglesPerSection = 500000)
```

Creates a flat rectangular terrain.
@param Size World size along X and Y (cm).
@param Resolution Number of quads along X and Y. Fixes the detail of the base sections.
@param Location World position of the terrain's center (cm).
@param MaxTrianglesPerSection The terrain is split into equal sections no larger than this.
@return The new terrain.

### `ImportHeightmap`

```text
static UE::MeshPartition::AMeshPartition* ImportHeightmap( const FString& FilePath, const FVector& Size, const FIntPoint& Resolution, const FVector& Location = FVector::ZeroVector, int32 MaxTrianglesPerSection = 500000)
```

Creates a terrain from a grayscale heightmap.
@param FilePath Absolute path of an 8 or 16 bit PNG file.
@param Size World size (cm). X and Y are the footprint, Z is the height of a white pixel above a black one.
@param Resolution Number of quads along X and Y. The heightmap is resampled to it.
@param Location World position of the terrain's center at black level (cm).
@param MaxTrianglesPerSection The terrain is split into equal sections no larger than this.
@return The new terrain.

### `DeleteTerrain`

```text
static void DeleteTerrain(UE::MeshPartition::AMeshPartition* Terrain)
```

Deletes a terrain together with the actors attached to it: its loaded sections and modifier actors.
@param Terrain The terrain to delete.

### `GetModifiers`

```text
static TArray<FMeshTerrainModifierInfo> GetModifiers( UE::MeshPartition::AMeshPartition* Terrain, bool bIncludeBaseSections = false)
```

Returns the loaded modifiers of a terrain.
@param Terrain The terrain to list modifiers of.
@param bIncludeBaseSections Also list the base sections.

### `AddModifier`

```text
static UE::MeshPartition::UModifierComponent* AddModifier( UE::MeshPartition::AMeshPartition* Terrain, EMeshTerrainModifierType Type, const FVector& Location)
```

Adds a modifier to a terrain on a new actor attached to the terrain.
Spline modifiers start with a straight two-point spline of 2000 cm along the actor's X axis.
@param Terrain The terrain the modifier affects.
@param Type The kind of modifier.
@param Location World position of the modifier (cm).
@return The new modifier component.

### `RemoveModifier`

```text
static void RemoveModifier(UE::MeshPartition::UModifierComponent* Modifier)
```

Removes a modifier from its terrain. Deletes the hosting actor if the modifier is the reason
it exists (modifier actors and base sections), otherwise only the component.
@param Modifier The modifier to remove.

### `SampleHeights`

```text
static FMeshTerrainHeightGrid SampleHeights( UE::MeshPartition::AMeshPartition* Terrain, const FVector2D& Min, const FVector2D& Max, const FIntPoint& NumSamples, bool bBaseOnly = false)
```

Reads terrain heights on a regular grid. Where surfaces overlap, the highest one is returned.
@param Terrain The terrain to sample.
@param Min World XY of the first sample (cm).
@param Max World XY of the last sample (cm). Must be greater than Min on both axes.
@param NumSamples Samples along X and Y, at least 2 each and at most 65536 in total.
@param bBaseOnly Sample the base sections without modifiers, the surface SetHeights writes to.
       Otherwise sample the built surface, which lags behind edits until the build has finished.

### `SetHeights`

```text
static int32 SetHeights( UE::MeshPartition::AMeshPartition* Terrain, const FMeshTerrainHeightGrid& Grid, EMeshTerrainHeightBlend Blend = EMeshTerrainHeightBlend::Replace, float FalloffDistance = 0.f)
```

Writes a height grid into the base sections of a terrain. Every base vertex inside the grid's
rectangle takes the bilinearly interpolated grid height, so detail finer than the base
resolution is lost. Grid samples marked as no terrain leave the vertices around them unchanged.
@param Terrain The terrain to edit.
@param Grid The heights and the rectangle they cover.
@param Blend How the heights combine with the current surface.
@param FalloffDistance Width of the band along the rectangle's border over which the edit fades in (cm). 0 for a hard edge.
@return Number of vertices changed.

### `RebuildTerrain`

```text
static void RebuildTerrain(UE::MeshPartition::AMeshPartition* Terrain)
```

Discards the built surface of a terrain and rebuilds all of it from the base sections and modifiers.
@param Terrain The terrain to rebuild.

### `WaitForBuild`

```text
static UToolCallAsyncResultVoid* WaitForBuild( UE::MeshPartition::AMeshPartition* Terrain, float TimeoutSeconds = 120.f)
```

Waits until the built surface of a terrain reflects all edits made so far.
@param Terrain The terrain to wait for.
@param TimeoutSeconds Fails if the build has not finished after this long.
