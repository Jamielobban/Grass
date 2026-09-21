# GPU Grass

A custom **GPU-driven vegetation system for Unity URP** built around compute shaders, procedural geometry, indirect rendering, editor painting tools, terrain conformity, LODs, wind flow, stylised grass variants, and procedural flowers.

The project is designed as both a rendering experiment and a small vegetation framework: large grass fields are generated and culled on the GPU while Unity-side tooling controls placement, painting, terrain interaction, wind, styles, and demo scenes.

---

## Visual Showcase

### Default Meadow

A baseline meadow scene showing dense procedural vegetation, painted grass variety, flowers, terrain conformity, and the winding path layout.

![Default Meadow](Images/normal-meadow.png)

### Anime Meadow

A stylised grass variant using more exaggerated blade shapes while keeping the same underlying GPU vegetation pipeline.

![Anime Meadow](Images/anime-meadow.png)

### Curled Ribbons

A more extreme procedural style demonstrating that the system is not limited to traditional grass blade geometry.

![Curled Ribbons](Images/curled-ribbons.png)

### Reed Bed

A specialised vegetation style using tall reed-like forms and a very different silhouette from the standard meadow grass.

![Reed Bed](Images/reed-bed.png)

### LOD Visualizer

Debug view used to inspect distance-based LOD regions, distribution, and large-scale vegetation behaviour.

![LOD Visualizer](Images/lod-visualizer.png)

---

## Features

### GPU-Driven Rendering

- Up to **1,000,000 grass blade candidates**
- Compute-shader placement and visibility processing
- GPU frustum culling
- Three distance-based LOD groups
- `Graphics.DrawProceduralIndirect`
- No individual grass GameObjects or prebuilt blade meshes
- Stable distance transitions designed to reduce visible popping

The system is designed to keep the majority of vegetation work on the GPU.

---

## Procedural Blade Geometry

Grass geometry is created directly in the shader instead of relying on traditional mesh assets.

LOD complexity changes depending on distance from the camera:

| LOD | Standard Grass |
| --- | --- |
| Near | 5 segments |
| Mid | 2 segments |
| Far | 1 segment |

More stylised vegetation modes can use their own procedural geometry rules.

A debug LOD mode is included so the active regions can be visualised directly in the scene.

---

## Grass Styles

The project currently includes multiple visual styles:

- Soft Meadow
- Classic Meadow
- Pastel Storybook
- Low Poly
- Dry Prairie
- Moonlit Fantasy
- Cel Field
- Curled Ribbons
- Reed Bed
- Crystal Garden
- Anime Meadow

Styles control more than colour.

Depending on the selected mode, they can alter:

- Root colour
- Tip colour
- Blade height
- Blade width
- Curvature
- Wind response
- Shading
- Emission
- Procedural geometry shape

The more stylised modes use a separate sculpted vegetation shader to create shapes such as ribbons, reeds, crystals, and exaggerated anime-style foliage.

---

## Grass Painting

Vegetation can be painted directly in the **Unity Scene View** using a custom editor workflow.

Each paint map stores vegetation distribution and density.

For grass, the RGB channels represent different vegetation types:

- **Short Turf**
- **Meadow**
- **Golden Tall**

The Scene View painting workflow supports:

- Left-drag painting
- `Shift + Drag` erase
- Adjustable brush radius
- Adjustable brush strength
- Undo / redo
- Fill selected type
- Clear grass
- Automatic asset saving

If no paint map is assigned, the system can fall back to procedural meadow generation.

---

## Winding Path Preset

The project includes a preset designed to quickly create a meadow with multiple vegetation regions and a visible path.

The setup includes:

- Short turf
- Broad meadow grass
- Golden tall grass
- A winding grass-free path through the field

This makes it possible to create more intentional compositions instead of relying entirely on random distribution.

---

## Procedural Flowers

A separate GPU vegetation layer can render flowers on top of the grass.

Current flower types include:

- Daisy
- Poppy
- Tall Cosmos

Flowers use their own vegetation data, allowing them to be painted independently without replacing the grass underneath.

The flower shader procedurally constructs:

- Stems
- Leaves
- Petals
- Flower centres

Flower geometry also reduces in complexity with distance.

---

## Terrain Conformity

The vegetation system can render on non-flat surfaces.

`GrassValley` generates a procedural valley mesh and exposes terrain height and normal data to the GPU vegetation pipeline.

The grass system can then:

- Place blades at the correct terrain height
- Align vegetation to the terrain normal
- Control slope alignment strength
- Reject vegetation on slopes above a configurable angle

This allows the same vegetation system to work across rolling hills and valleys instead of only flat surfaces.

---

## Wind System

The vegetation uses a shared world-space wind system rather than a single uniform sine-wave animation.

Wind controls include:

- Global wind strength
- Base wind direction
- Direction variation
- Pattern scale
- Wind-flow-map influence

The shader combines larger directional movement with local blade motion so the field does not move as one perfectly synchronized surface.

---

## Wind Flow Maps

A wind-flow texture can control local wind behaviour.

The texture stores:

- **R / G** — world-space wind direction
- **B** — wind strength

An editor tool can generate a swirling wind map automatically.

Wind settings can also be shared between grass and flower layers so all vegetation reacts coherently.

---

## GPU Pipeline

The system is split into two main compute stages.

### Generate

The generation stage creates candidate blades and determines values such as:

- Position
- Grass type
- Density
- Height
- Width
- Orientation
- Random seed
- Terrain position
- Terrain normal
- Slope rejection
- Paint-map influence

Blades are distributed in a way that creates small natural clusters while avoiding completely uniform spacing.

### Cull

Every rendered frame, the culling stage:

1. Tests vegetation against the camera frustum
2. Rejects blades outside the maximum render distance
3. Assigns visible blades to Near, Mid, or Far LOD buffers
4. Updates indirect draw arguments
5. Draws only the surviving vegetation

This keeps CPU-side overhead low even when working with very large vegetation counts.

---

## Demo Scenes

The repository includes example scenes under:

```text
Assets/GPUGrass/Demo/
