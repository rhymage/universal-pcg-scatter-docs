# Universal PCG Scatter

User manual · Version 1.0.0 · rhymage

A Blueprint-controlled Unreal Engine runtime plugin for scattering your own static and skeletal meshes on surfaces, along splines, and inside a region. A PCG graph connects the native sampling and spawning nodes. Static meshes use instanced static mesh components; skeletal meshes use individual skeletal mesh components.

## Requirements and installation

- Unreal Engine 5.8, Windows 64-bit. Other engine versions and platforms are not included in this release.
- Enable **PCG** and **Universal PCG Scatter** in Edit → Plugins, then restart.
- For manual source installation, place the `UniversalScatter` folder under your project's `Plugins` folder. A compatible Visual Studio C++ toolchain is required to compile a source installation. Fab installations use Epic's build process.
- In the Content Browser settings, enable **Show Plugin Content**. Open **UniversalScatter Content** and drag **BP_UniversalScatter** into a level.
- The internal plugin/module name remains `UniversalScatter`; do not rename its source module or asset paths.

Distances are Unreal centimeters, rotation angles are degrees, flow speed is centimeters per second, and Evaluation Time is seconds. Region Extent is a **half size**: `(500,500,250)` describes a 10 × 10 × 5 meter box before actor scaling.

## Quick start: a volume of instances

1. Place BP_UniversalScatter and select the actor itself in the Outliner.
2. Set **01 Sources → Source = Volume**.
3. Expand **03 Elements → Elements**, add an entry, choose Type = Static Mesh, and assign your mesh. Remove or replace the placeholder cube.
4. Set **04 Distribution → Target Count = 100**. Adjust **05 Transform → Scale Min / Scale Max** for the real size of your mesh.
5. Expand **Scatter Actions** and click **Generate Scatter**. Check **09 Preview → Generated Count / Generation Status**.
6. After changing generation settings, click Generate Scatter again. Save your level to retain the generated arrangement.

## Surface placement: mesh and landscape

For a mesh, set Source = Mesh Surface and add the source actors to **Surface Actors** using the eyedropper. Sampling uses LOD 0 triangles and their geometric normals. For a landscape, set Source = Landscape and select the landscape in Surface Actors. Landscape sampling and projection require query collision on the target surface and a non-ignored Trace Channel.

Use **02 Region → Limit To Region** and choose Box, Sphere, or Cylinder to restrict placement. Move and scale the scatter actor, or edit Region Extent. **Fit Bounds To Sources** gives a starting box around source actors. Set Slope Degrees to exclude steep surfaces; use World Height to restrict elevation. Normal Offset lifts or lowers instances along the sampled normal. **Project To Surface** projects candidate positions using Projection Direction and Projection Distance.

For runtime regeneration on static mesh surfaces, enable **Allow CPU Access** on the source static mesh before cooking. Landscape Paint Layer filtering is available during editor generation only. Preserve generated results when relying on that filter at runtime.

## Spline placement and flow

Assign a **Spline Actor** or edit the actor's built-in **Guide Spline**. If the actor contains multiple spline components, set Spline Component Name. Choose Along for a centerline, Ribbon for a band, or Interior for the inside of a closed spline. Spline Width controls the half width of a ribbon. Start/End Inset excludes the ends. Closed-spline interiors are evaluated in the world XY plane.

For moving fish, petals, or other objects:

1. Set Source = Spline and choose Along or Ribbon.
2. In each moving Elements entry, set **Motion = Spline Flow**. The default Static mode does not move.
3. Enable **07 Flow → Enable Motion** and **Orient To Velocity**. Start with Flow Speed = 100 and Speed Multiplier = `(0.8,1.2)`.
4. Generate Scatter, then click **Scatter Actions → Start Preview** to see motion in the editor. Enable viewport Realtime if the viewport is not updating. Stop Preview pauses the preview; Reset Motion returns to the initial state.
5. In Play mode, moving elements update automatically. End Behavior offers Loop, Ping Pong, Stop, and Hide.

**Fish facing sideways:** open **03 Elements → Elements → the fish entry → Rotation Offset**. Try **Yaw = 90°** or **−90°** to correct the imported mesh's forward axis, then regenerate. Use Pitch/Roll if the model is also rotated vertically. Keep Orient To Velocity enabled. For a clean starting orientation, set global Rotation Min and Rotation Max to zero.

For turbulence, raise **08 Turbulence → Turbulence Strength** gradually; Turbulence Size sets the spatial scale and Turbulence Speed controls variation over time. An element with Motion = Turbulence moves around its initial position without traveling down the spline. Enable Vortex and assign a Vortex Actor for an explicit vortex center; adjust axis, radius, speed, and inward pull.

Path Attraction and Motion Drag reduce the turbulence displacement. This is procedural motion, not a physical fluid simulation, flocking AI, or obstacle avoidance. Large values can overlap objects and surfaces. Keep On Surface performs additional projection and increases per-frame cost.

## Animation and time control

For a skeletal mesh entry, select Type = Skeletal Mesh, assign the mesh, and assign either an Animation asset or Animation Blueprint. When an Animation Blueprint is assigned, it takes precedence. Animation Play Rate and Random Animation Phase apply to the simple Animation asset path; an Animation Blueprint manages its own animation behavior. Mesh and animation skeletons must be compatible.

Enable **09 Preview → Use Evaluation Time** and change **Evaluation Time** to inspect procedural transforms at a chosen time. Evaluation Time is exposed to cinematics and can be keyed in Sequencer. Skeletal animation playback is a separate system; this control does not promise frame-exact animation scrubbing or movie-render synchronization.

## Parameter reference

| Group | Controls and purpose |
| --- | --- |
| 01 Sources | Source type, Surface Actors, Spline Actor/component, spline mode/width/insets, surface projection direction/distance/channel. |
| 02 Region | Region shape and half extents; edge density falloff for box/sphere/cylinder; exclusion actors and padding. Closed splines exclude their XY interior; other actors use their bounds. |
| 03 Elements | Enabled, mesh Type/asset, Weight, Seed Offset, per-element Scale and Scale Multiplier, Rotation/Position Offset, Separation Radius, material overrides, shadow, collision, cull distance, motion and animation. Sequential Selection cycles elements instead of weighted choice. |
| 04 Distribution | Count or approximate density per square meter; Target Count; total and skeletal limits; Seed; Random, Minimum Distance, Jittered Grid or Clusters; spacing, grid jitter, cluster count/radius and attempts. |
| 05 Transform | Uniform/nonuniform scale range; position and angle random ranges; surface-space position; normal offset; alignment to world up, surface normal, spline tangent, direction or look-at actor; normal influence, tilt limit and normal axis. |
| 06 Filters | Slope, world height, noise mask size/threshold, editor landscape layer/weight, radial actor distance field, inner/outer radii, invert, scale range, spline density/scale curves. Curve input is normalized spline progress from 0 to 1. |
| 07 Flow | Global motion switch, flow speed range, end behavior, direction alignment/response, turbulence attenuation, surface projection, ground offset and spin. |
| 08 Turbulence | Turbulence displacement strength/size/speed; vortex center, axis, radius, angular speed and inward pull. |
| 09 Preview | Auto Regenerate, reduced-density generation, preview fraction, bounds/point display, evaluation time, generated count and status. |

**Scatter Actions:** Generate Scatter rebuilds the arrangement; Cleanup Scatter removes generated components; Reseed changes the seed and regenerates; Start/Stop Preview controls editor motion; Reset Motion resets time; Fit Bounds To Sources sizes the region from sources.

## Recipes

- **Foliage:** Mesh Surface or Landscape; Static Mesh entries with Motion = Static; Minimum Distance; Surface Normal alignment; moderate scale range; constrain Slope Degrees.
- **A school of fish:** Spline Ribbon; fish entries with Spline Flow; zero global random rotation; correct each mesh Rotation Offset; modest speed variation and turbulence; Start Preview.
- **Petals around a path:** Spline Ribbon; Spline Flow; position/scale variation; small turbulence and spin. Disable collision for decorative particles.
- **A floating cluster:** Volume; Clusters distribution; Motion = Turbulence; an explicit Vortex Actor for a stable vortex center.

## Performance, limits, and troubleshooting

- Target Count is a target, not a guarantee. Filters, region boundaries, spacing, invalid assets and attempt limits reduce the result. Inspect Generation Status; widen the region, reduce spacing or relax filters before raising Attempts Per Point.
- Density is estimated from source area and is not an exact count per clipped region. Volume density uses projected area, not cubic meters.
- Grid and cluster spatial layouts apply to volume/landscape sampling. Spline grid mode orders samples along the path; triangle mesh sampling remains random. Closed-spline edge falloff is not implemented.
- Skeletal meshes are individual components, not GPU skeletal instancing. Start with small counts. Max Skeletal Instances defaults to 300; that is a safety limit, not a performance guarantee. Static moving instances also have per-frame CPU update cost.
- The included placeholder uses an Unreal Engine basic shape. The plugin does not include fish, foliage, petal models, animations, or third-party environment packs; supply assets you are licensed to use.
- If editor motion is still, select the actor, expand Scatter Actions, click Start Preview, enable viewport Realtime, and check each entry's Motion and Enable Motion. Use Evaluation Time overrides the live clock.
- If nothing is generated, check mesh assignments, Enabled, positive Weight, region overlap, Source actors, collision/projection, filters and instance caps.
- After changing geometry, element settings or distribution, regenerate. Auto Regenerate can make editing expensive; leave it off for large arrangements.
- The packaged plugin is a native C++ runtime module with Blueprint controls and a PCG graph. It is not a Blueprint-only asset, a Niagara system, or a multiplayer replication solution.

## Support

Publisher: [rhymage](https://www.rhymage.com/). Report an issue at [GitHub Issues](https://github.com/rhymage/universal-pcg-scatter-docs/issues) with engine version, source mode, a short reproduction and relevant log excerpts. Remove private paths and project data before posting publicly. The public repository contains documentation only; the plugin is distributed separately through Fab.
