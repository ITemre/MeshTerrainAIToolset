# Runtime MCP schemas

Captured from `describe_toolset` in the native Unreal Engine 5.8.4 MCP server. These are documentation examples, not plugin source files. Refresh the schema from your own editor before automation.

## MeshTerrainToolset.MeshTerrainToolset

```json
{
  "name": "MeshTerrainToolset.MeshTerrainToolset",
  "version": "1.0",
  "description": "Creates and edits Mesh Terrain (Mesh Partition) in the current level: terrains and their base\nsections, heightmap import, the non-destructive modifier stack, and reading and writing heights as\ngrids. Modifier settings are plain properties of the returned modifier components.",
  "tools": [
    {
      "description": "Waits until the built surface of a terrain reflects all edits made so far.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.WaitForBuild",
      "inputSchema": {
        "type": "object",
        "properties": {
          "terrain": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The terrain to wait for.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          },
          "timeoutSeconds": {
            "type": "number",
            "description": "Fails if the build has not finished after this long.",
            "default": 120
          }
        },
        "required": [
          "terrain"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "description": "Always null",
            "type": "null"
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Writes a height grid into the base sections of a terrain. Every base vertex inside the grid's\nrectangle takes the bilinearly interpolated grid height, so detail finer than the base\nresolution is lost. Grid samples marked as no terrain leave the vertices around them unchanged.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.SetHeights",
      "inputSchema": {
        "type": "object",
        "properties": {
          "terrain": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The terrain to edit.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          },
          "grid": {
            "title": "MeshTerrainHeightGrid",
            "description": "The heights and the rectangle they cover.",
            "type": "object",
            "properties": {
              "min": {
                "title": "Vector2D",
                "description": "World XY of the first sample (cm).",
                "type": "object",
                "properties": {
                  "x": {
                    "type": "number"
                  },
                  "y": {
                    "type": "number"
                  }
                },
                "required": [
                  "x",
                  "y"
                ]
              },
              "max": {
                "title": "Vector2D",
                "description": "World XY of the last sample (cm).",
                "type": "object",
                "properties": {
                  "x": {
                    "type": "number"
                  },
                  "y": {
                    "type": "number"
                  }
                },
                "required": [
                  "x",
                  "y"
                ]
              },
              "numSamples": {
                "title": "IntPoint",
                "description": "Samples along X and Y, evenly spaced from Min to Max inclusive.",
                "type": "object",
                "properties": {
                  "x": {
                    "type": "integer"
                  },
                  "y": {
                    "type": "integer"
                  }
                },
                "required": [
                  "x",
                  "y"
                ]
              },
              "heights": {
                "type": "array",
                "items": {
                  "type": "number"
                },
                "description": "World Z per sample (cm), row by row: index = Y * NumSamples.X + X.\nA value at or below -1e30 means no terrain at that sample."
              }
            },
            "required": [
              "min",
              "max",
              "numSamples",
              "heights"
            ]
          },
          "blend": {
            "type": "string",
            "title": "EMeshTerrainHeightBlend",
            "enum": [
              "Replace",
              "Add"
            ],
            "description": "How the heights combine with the current surface.",
            "default": "Replace"
          },
          "falloffDistance": {
            "type": "number",
            "description": "Width of the band along the rectangle's border over which the edit fades in (cm). 0 for a hard edge.",
            "default": 0
          }
        },
        "required": [
          "terrain",
          "grid"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "type": "integer",
            "description": "Number of vertices changed."
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Reads terrain heights on a regular grid. Where surfaces overlap, the highest one is returned.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.SampleHeights",
      "inputSchema": {
        "type": "object",
        "properties": {
          "terrain": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The terrain to sample.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          },
          "min": {
            "title": "Vector2D",
            "description": "World XY of the first sample (cm).",
            "type": "object",
            "properties": {
              "x": {
                "type": "number"
              },
              "y": {
                "type": "number"
              }
            },
            "required": [
              "x",
              "y"
            ]
          },
          "max": {
            "title": "Vector2D",
            "description": "World XY of the last sample (cm). Must be greater than Min on both axes.",
            "type": "object",
            "properties": {
              "x": {
                "type": "number"
              },
              "y": {
                "type": "number"
              }
            },
            "required": [
              "x",
              "y"
            ]
          },
          "numSamples": {
            "title": "IntPoint",
            "description": "Samples along X and Y, at least 2 each and at most 65536 in total.",
            "type": "object",
            "properties": {
              "x": {
                "type": "integer"
              },
              "y": {
                "type": "integer"
              }
            },
            "required": [
              "x",
              "y"
            ]
          },
          "bBaseOnly": {
            "type": "boolean",
            "description": "Sample the base sections without modifiers, the surface SetHeights writes to. Otherwise sample the built surface, which lags behind edits until the build has finished.",
            "default": false
          }
        },
        "required": [
          "terrain",
          "min",
          "max",
          "numSamples"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "title": "MeshTerrainHeightGrid",
            "type": "object",
            "properties": {
              "min": {
                "title": "Vector2D",
                "description": "World XY of the first sample (cm).",
                "type": "object",
                "properties": {
                  "x": {
                    "type": "number"
                  },
                  "y": {
                    "type": "number"
                  }
                },
                "required": [
                  "x",
                  "y"
                ]
              },
              "max": {
                "title": "Vector2D",
                "description": "World XY of the last sample (cm).",
                "type": "object",
                "properties": {
                  "x": {
                    "type": "number"
                  },
                  "y": {
                    "type": "number"
                  }
                },
                "required": [
                  "x",
                  "y"
                ]
              },
              "numSamples": {
                "title": "IntPoint",
                "description": "Samples along X and Y, evenly spaced from Min to Max inclusive.",
                "type": "object",
                "properties": {
                  "x": {
                    "type": "integer"
                  },
                  "y": {
                    "type": "integer"
                  }
                },
                "required": [
                  "x",
                  "y"
                ]
              },
              "heights": {
                "type": "array",
                "items": {
                  "type": "number"
                },
                "description": "World Z per sample (cm), row by row: index = Y * NumSamples.X + X.\nA value at or below -1e30 means no terrain at that sample."
              }
            },
            "required": [
              "min",
              "max",
              "numSamples",
              "heights"
            ]
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Removes a modifier from its terrain. Deletes the hosting actor if the modifier is the reason\nit exists (modifier actors and base sections), otherwise only the component.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.RemoveModifier",
      "inputSchema": {
        "type": "object",
        "properties": {
          "modifier": {
            "type": "object",
            "title": "/Script/MeshPartitionEditor.ModifierComponent",
            "description": "The modifier to remove.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          }
        },
        "required": [
          "modifier"
        ]
      }
    },
    {
      "description": "Discards the built surface of a terrain and rebuilds all of it from the base sections and modifiers.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.RebuildTerrain",
      "inputSchema": {
        "type": "object",
        "properties": {
          "terrain": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The terrain to rebuild.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          }
        },
        "required": [
          "terrain"
        ]
      }
    },
    {
      "description": "Creates a terrain from a grayscale heightmap.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.ImportHeightmap",
      "inputSchema": {
        "type": "object",
        "properties": {
          "filePath": {
            "type": "string",
            "description": "Absolute path of an 8 or 16 bit PNG file."
          },
          "size": {
            "title": "Vector",
            "description": "World size (cm). X and Y are the footprint, Z is the height of a white pixel above a black one.",
            "type": "object",
            "properties": {
              "x": {
                "type": "number"
              },
              "y": {
                "type": "number"
              },
              "z": {
                "type": "number"
              }
            },
            "required": [
              "x",
              "y",
              "z"
            ]
          },
          "resolution": {
            "title": "IntPoint",
            "description": "Number of quads along X and Y, at most 4194304 (2048 x 2048) in total. The heightmap is resampled to it.",
            "type": "object",
            "properties": {
              "x": {
                "type": "integer"
              },
              "y": {
                "type": "integer"
              }
            },
            "required": [
              "x",
              "y"
            ]
          },
          "location": {
            "title": "Vector",
            "description": "World position of the terrain's center at black level (cm).",
            "type": "object",
            "properties": {
              "x": {
                "type": "number"
              },
              "y": {
                "type": "number"
              },
              "z": {
                "type": "number"
              }
            },
            "required": [
              "x",
              "y",
              "z"
            ]
          },
          "maxTrianglesPerSection": {
            "type": "integer",
            "description": "The terrain is split into equal sections no larger than this.",
            "default": 500000
          }
        },
        "required": [
          "filePath",
          "size",
          "resolution",
          "location"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The new terrain.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Returns the terrains loaded in the current level.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.GetTerrains",
      "inputSchema": {
        "type": "object"
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "type": "array",
            "items": {
              "type": "object",
              "title": "/Script/MeshPartition.MeshPartition",
              "properties": {
                "refPath": {
                  "type": "string",
                  "description": "The reference stored as a soft path string."
                }
              },
              "required": [
                "refPath"
              ]
            },
            "description": "The terrain actors, or an empty array if the level has none."
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Returns size, section and modifier counts, definition and build state of a terrain.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.GetTerrainInfo",
      "inputSchema": {
        "type": "object",
        "properties": {
          "terrain": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The terrain to describe.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          }
        },
        "required": [
          "terrain"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "title": "MeshTerrainInfo",
            "type": "object",
            "properties": {
              "terrain": {
                "type": "object",
                "title": "/Script/MeshPartition.MeshPartition",
                "description": "The terrain actor. Moving it moves its sections and attached modifiers.",
                "properties": {
                  "refPath": {
                    "type": "string",
                    "description": "The reference stored as a soft path string."
                  }
                },
                "required": [
                  "refPath"
                ]
              },
              "label": {
                "type": "string",
                "description": "Actor label shown in the outliner."
              },
              "bounds": {
                "title": "Box",
                "description": "World bounds of the base sections (cm). Invalid if the terrain has no loaded section.",
                "type": "object",
                "properties": {
                  "min": {
                    "title": "Vector",
                    "type": "object",
                    "properties": {
                      "x": {
                        "type": "number"
                      },
                      "y": {
                        "type": "number"
                      },
                      "z": {
                        "type": "number"
                      }
                    },
                    "required": [
                      "x",
                      "y",
                      "z"
                    ]
                  },
                  "max": {
                    "title": "Vector",
                    "type": "object",
                    "properties": {
                      "x": {
                        "type": "number"
                      },
                      "y": {
                        "type": "number"
                      },
                      "z": {
                        "type": "number"
                      }
                    },
                    "required": [
                      "x",
                      "y",
                      "z"
                    ]
                  },
                  "isValid": {
                    "type": "boolean"
                  }
                },
                "required": [
                  "min",
                  "max",
                  "isValid"
                ]
              },
              "numSections": {
                "type": "integer",
                "description": "Number of base sections, the meshes the terrain is built from before modifiers apply."
              },
              "numVertices": {
                "type": "integer",
                "description": "Total vertex count of the base sections. Bounds the detail SetHeights can represent."
              },
              "numModifiers": {
                "type": "integer",
                "description": "Number of modifiers, not counting base sections."
              },
              "definition": {
                "type": "object",
                "title": "/Script/MeshPartition.MeshPartitionDefinition",
                "description": "Asset that defines the material, weight channels and priority layers. Null if none is assigned.",
                "properties": {
                  "refPath": {
                    "type": "string",
                    "description": "The reference stored as a soft path string."
                  }
                },
                "required": [
                  "refPath"
                ]
              },
              "channels": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "Weight channels modifiers can write to."
              },
              "priorityLayers": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "Priority layers in application order. Modifiers in later layers apply on top of earlier ones."
              },
              "bIsBuilding": {
                "type": "boolean",
                "description": "True while the visible surface is being rebuilt after a change."
              }
            },
            "required": [
              "terrain",
              "label",
              "bounds",
              "numSections",
              "numVertices",
              "numModifiers",
              "definition",
              "channels",
              "priorityLayers",
              "bIsBuilding"
            ]
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Returns the loaded modifiers of a terrain.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.GetModifiers",
      "inputSchema": {
        "type": "object",
        "properties": {
          "terrain": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The terrain to list modifiers of.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          },
          "bIncludeBaseSections": {
            "type": "boolean",
            "description": "Also list the base sections.",
            "default": false
          }
        },
        "required": [
          "terrain"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "type": "array",
            "items": {
              "title": "MeshTerrainModifierInfo",
              "type": "object",
              "properties": {
                "modifier": {
                  "type": "object",
                  "title": "/Script/MeshPartitionEditor.ModifierComponent",
                  "description": "The modifier component. Its settings are regular properties.",
                  "properties": {
                    "refPath": {
                      "type": "string",
                      "description": "The reference stored as a soft path string."
                    }
                  },
                  "required": [
                    "refPath"
                  ]
                },
                "actor": {
                  "type": "object",
                  "title": "/Script/Engine.Actor",
                  "description": "Actor hosting the modifier. Moving it moves the modifier.",
                  "properties": {
                    "refPath": {
                      "type": "string",
                      "description": "The reference stored as a soft path string."
                    }
                  },
                  "required": [
                    "refPath"
                  ]
                },
                "className": {
                  "type": "string",
                  "description": "Class name of the modifier, for example NoiseModifier."
                },
                "bIsBaseSection": {
                  "type": "boolean",
                  "description": "True for base sections."
                },
                "priorityLayer": {
                  "type": "string",
                  "description": "Priority layer the modifier applies in. None if it has not been assigned one."
                },
                "priority": {
                  "type": "number",
                  "description": "Order within the priority layer. Higher applies later."
                },
                "bIsDisabled": {
                  "type": "boolean",
                  "description": "True if the modifier is skipped when the terrain is built."
                },
                "bounds": {
                  "title": "Box",
                  "description": "World bounds of the region the modifier can affect (cm).",
                  "type": "object",
                  "properties": {
                    "min": {
                      "title": "Vector",
                      "type": "object",
                      "properties": {
                        "x": {
                          "type": "number"
                        },
                        "y": {
                          "type": "number"
                        },
                        "z": {
                          "type": "number"
                        }
                      },
                      "required": [
                        "x",
                        "y",
                        "z"
                      ]
                    },
                    "max": {
                      "title": "Vector",
                      "type": "object",
                      "properties": {
                        "x": {
                          "type": "number"
                        },
                        "y": {
                          "type": "number"
                        },
                        "z": {
                          "type": "number"
                        }
                      },
                      "required": [
                        "x",
                        "y",
                        "z"
                      ]
                    },
                    "isValid": {
                      "type": "boolean"
                    }
                  },
                  "required": [
                    "min",
                    "max",
                    "isValid"
                  ]
                }
              },
              "required": [
                "modifier",
                "actor",
                "className",
                "bIsBaseSection",
                "priorityLayer",
                "priority",
                "bIsDisabled",
                "bounds"
              ]
            }
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Deletes a terrain together with the actors attached to it: its loaded sections and modifier actors.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.DeleteTerrain",
      "inputSchema": {
        "type": "object",
        "properties": {
          "terrain": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The terrain to delete.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          }
        },
        "required": [
          "terrain"
        ]
      }
    },
    {
      "description": "Creates a flat rectangular terrain.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.CreateTerrain",
      "inputSchema": {
        "type": "object",
        "properties": {
          "size": {
            "title": "Vector2D",
            "description": "World size along X and Y (cm).",
            "type": "object",
            "properties": {
              "x": {
                "type": "number"
              },
              "y": {
                "type": "number"
              }
            },
            "required": [
              "x",
              "y"
            ]
          },
          "resolution": {
            "title": "IntPoint",
            "description": "Number of quads along X and Y, at most 4194304 (2048 x 2048) in total. Fixes the detail of the base sections.",
            "type": "object",
            "properties": {
              "x": {
                "type": "integer"
              },
              "y": {
                "type": "integer"
              }
            },
            "required": [
              "x",
              "y"
            ]
          },
          "location": {
            "title": "Vector",
            "description": "World position of the terrain's center (cm).",
            "type": "object",
            "properties": {
              "x": {
                "type": "number"
              },
              "y": {
                "type": "number"
              },
              "z": {
                "type": "number"
              }
            },
            "required": [
              "x",
              "y",
              "z"
            ]
          },
          "maxTrianglesPerSection": {
            "type": "integer",
            "description": "The terrain is split into equal sections no larger than this.",
            "default": 500000
          }
        },
        "required": [
          "size",
          "resolution",
          "location"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The new terrain.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Adds a modifier to a terrain on a new actor attached to the terrain.\nSpline modifiers start with a straight two-point spline of 2000 cm along the actor's X axis.",
      "name": "MeshTerrainToolset.MeshTerrainToolset.AddModifier",
      "inputSchema": {
        "type": "object",
        "properties": {
          "terrain": {
            "type": "object",
            "title": "/Script/MeshPartition.MeshPartition",
            "description": "The terrain the modifier affects.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          },
          "type": {
            "type": "string",
            "title": "EMeshTerrainModifierType",
            "enum": [
              "Patch",
              "InstancedPatch",
              "TexturePatch",
              "Spline",
              "Noise",
              "Boolean",
              "Lattice",
              "Remesh",
              "SplineRemesh",
              "MeshProject",
              "SculptLayers",
              "WeightUtility"
            ],
            "description": "The kind of modifier."
          },
          "location": {
            "title": "Vector",
            "description": "World position of the modifier (cm).",
            "type": "object",
            "properties": {
              "x": {
                "type": "number"
              },
              "y": {
                "type": "number"
              },
              "z": {
                "type": "number"
              }
            },
            "required": [
              "x",
              "y",
              "z"
            ]
          }
        },
        "required": [
          "terrain",
          "type",
          "location"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "type": "object",
            "title": "/Script/MeshPartitionEditor.ModifierComponent",
            "description": "The new modifier component.",
            "properties": {
              "refPath": {
                "type": "string",
                "description": "The reference stored as a soft path string."
              }
            },
            "required": [
              "refPath"
            ]
          }
        },
        "required": [
          "returnValue"
        ]
      }
    }
  ]
}
```

