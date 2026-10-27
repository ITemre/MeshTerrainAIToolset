# Data types

These fields come from the plugin source. Query `describe_toolset` for the complete wire schema, including required fields and engine-reflected types.

## FMeshTerrainInfo

```cpp
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
```

## FMeshTerrainModifierInfo

```cpp
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
```

## FMeshTerrainHeightGrid

```cpp
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
	 * World Z per sample (cm), row by row: index = Y * NumSamples.X + X.
	 * A value at or below -1e30 means no terrain at that sample.
	 */
	UPROPERTY(BlueprintReadWrite, Category = "MeshTerrain")
	TArray<float> Heights;
```

