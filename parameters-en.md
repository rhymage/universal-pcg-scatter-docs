# Universal PCG Scatter — Complete parameter reference

Every public option in groups 00–08, nested Elements settings and Scatter Actions, checked against the source definitions. Search by English option name. Units are cm, degrees and seconds; XYZ denotes vector components, Pitch/Yaw/Roll denotes rotation components. Blueprint and instance defaults can differ, so this page explains behavior. Auto Regenerate defaults ON; use Generate manually when it is disabled. Motion is procedural, not fluid physics, flocking AI or obstacle avoidance.

Version 1.1.0 · Transform → Scatter Actions → 00 Preview Settings → 01–08.

## Scatter Actions

| Option | Description |
|---|---|
| Generate (`GenerateScatter`) | Runs the PCG graph to regenerate from current settings; PCG component needs the PCG_UniversalScatter graph. |
| Cleanup (`CleanupScatter`) | Removes this actor's generated components and PCG output; retains source actors and mesh assets. |
| FitBoundsToSources (`FitBoundsToSources`) | Fits actor position and Region Extent to combined Surface Actors and external Spline Actor bounds. Excludes the built-in Guide Spline; inspect the result with rotation/nonuniform scale. |
| Reseed (`Reseed`) | Changes the global Seed and regenerates. |

## 00 Preview Settings

| Option | Description |
|---|---|
| Animate in Editor (`AnimateInEditor`) | Default ON. Advances procedural motion in editor viewports; turn off to pause. Requires viewport Realtime. Game motion and explicit Use Evaluation Time are separate; this is not a skeletal-animation pause control. |
| Auto Regenerate (`AutoRegenerate`) | Default ON. Regenerates when editor construction runs. Disable for expensive arrangements and use Generate manually. |
| Low Density Preview (`LowDensityPreview`) | Scales requested count by Preview Fraction. Applies to the shared generation function, including runtime generation. |
| Preview Fraction (`PreviewFraction`) | Low Density Preview fraction (0.01–1); 0.2 requests about 20% of the target. |
| Show Bounds (`ShowBounds`) | Shows editor bounds guides; does not enable or disable region limiting. |
| Show Points (`ShowPoints`) | Shows cyan debug points at initial generated positions in the editor, not current animated positions. |
| Evaluation Time (`EvaluationTime`) | Explicit procedural time in seconds, available to Sequencer. Requires Use Evaluation Time and an enabled Flow/Turbulence effect. Zero restores generated transforms. |
| Use Evaluation Time (`UseEvaluationTime`) | Uses Evaluation Time instead of the live elapsed clock. Does not synchronize skeletal animation frames. |
| Generated Count (`GeneratedCount`) | Read-only actual generated instance count. |
| Generation Status (`GenerationStatus`) | Read-only generation result/target count or source/mesh configuration error message. |

## 01 Sources

| Option | Description |
|---|---|
| Source (`Source`) | Source: Mesh Surface samples LOD 0 triangles; Landscape projects to terrain; Spline uses a path; Volume samples a volume. |
| Surface Actors (`SurfaceActors`) | Surface source actors; also restricts projection targets. When empty, projection searches eligible collision surfaces. |
| Spline Actor (`SplineActor`) | Optional external spline actor. If empty, GuideSpline supplies the path for spline placement, Flow and Closed Spline regions. |
| Spline Component Name (`SplineComponentName`) | Selects a named spline component on the external actor. None uses the first; an unmatched name fails. |
| Spline Mode (`SplineMode`) | Along: centerline; Ribbon: lateral band; Interior: world-XY interior of a closed spline, sampled within the Region Extent candidate area. |
| Spline Width (`SplineWidth`) | Ribbon half-width in cm: 200 means 400 cm total width. Also used in path-source density estimation. |
| Spline Start Inset (`SplineStartInset`) | Distance excluded at the start during placement. Does not constrain subsequent flow motion. |
| Spline End Inset (`SplineEndInset`) | Distance excluded at the end during placement. Flow still uses the full path. |
| Project To Surface (`ProjectToSurface`) | Projects candidate positions onto collision surfaces. Landscape always projects regardless of this switch. |
| Projection Direction (`ProjectionDirection`) | Normalized projection direction. A zero vector falls back to world down (0,0,-1). |
| Projection Distance (`ProjectionDistance`) | Total projection trace length in cm, half on either side of the candidate position. |
| Trace Channel (`TraceChannel`) | Surfaces that Ignore this collision channel are rejected by projection. Query collision is required. |

## 02 Region

| Option | Description |
|---|---|
| Limit To Region (`LimitToRegion`) | Rejects candidates outside the region. Disabling it does not remove the Region Extent sampling bounds for sources such as Volume. |
| Region (`Region`) | Box: local box; Sphere: minimum extent as radius; Cylinder: minimum XY extent as radius and Z half-height; Closed Spline: closed path XY interior plus local Z height limit. |
| Region Extent (`RegionExtent`) | Local half-size in cm. (500,500,250) gives a 10×10×5 m box before actor scaling. |
| Edge Falloff (`EdgeFalloff`) | Normalized edge density falloff width (0–1); zero disables it. Inactive when region limiting is off or Region is Closed Spline. |
| Exclusion Actors (`ExclusionActors`) | Excludes actor bounding boxes, closed spline XY interiors, or positions within Padding of an open spline. |
| Exclusion Padding (`ExclusionPadding`) | Padding in cm for actor bounds or open spline distance exclusion. Does not expand closed spline interiors. |

## 03 Elements

| Option | Description |
|---|---|
| Elements (`Elements`) | Array of mesh elements with weights, transforms and skeletal animation settings. Actor-level Flow/Turbulence applies to all elements. |
| Sequential Selection (`SequentialSelection`) | Cycles through valid elements instead of weighted random selection. Enabled, valid mesh and positive Weight remain required; rejected candidates can skip entries. |
| Elements → Label (`Label`) | A name for identifying the element; does not affect probability or motion. |
| Elements → Enabled (`Enabled`) | Whether this element can be selected for generation. |
| Elements → Type (`Type`) | Static Mesh uses instanced components; Skeletal Mesh creates individual skeletal components. |
| Elements → Static Mesh (`StaticMesh`) | Mesh to place when Type is Static Mesh. |
| Elements → Skeletal Mesh (`SkeletalMesh`) | Mesh used for Skeletal Mesh elements; its skeleton must be compatible with the animation. |
| Elements → Weight (`Weight`) | Relative selection weight; zero excludes the element. Sequential Selection only checks that it is positive. |
| Elements → Seed Offset (`SeedOffset`) | Per-element seed variation for random transforms and single-asset animation settings, combined with the global seed. |
| Elements → Scale (`Scale`) | Base XYZ scale multiplier, multiplied by global random scale and other scale factors. |
| Elements → Scale Multiplier (`ScaleMultiplier`) | Minimum/maximum uniform random scale multiplier for this element. |
| Elements → Rotation Offset (`RotationOffset`) | Per-mesh rotation correction, also retained during velocity alignment. Try Yaw ±90° for sideways-facing fish. |
| Elements → Position Offset (`PositionOffset`) | Per-element position offset in cm, added to global position jitter; follows Position In Surface Space. |
| Elements → Separation Radius (`SeparationRadius`) | Spherical placement separation radius, scaled by generated size. Not mesh collision testing or motion avoidance. |
| Elements → Materials (`Materials`) | Material overrides by slot index. Empty entries retain the original material. |
| Elements → Cast Shadow (`CastShadow`) | Enables shadow casting on generated components. |
| Elements → Collision (`Collision`) | Enables Query and Physics collision; does not automatically enable physics simulation or obstacle avoidance. |
| Elements → Cull Distance (`CullDistance`) | Distance culling limit in cm, using instance culling for static meshes and component culling for skeletal meshes. Zero removes the distance limit. |
| Elements → Animation (`Animation`) | Loops a single animation asset. An assigned Animation Blueprint takes priority. |
| Elements → Animation Blueprint (`AnimationBlueprint`) | Anim Instance class for the skeletal mesh. Playback rate and phase are controlled by its own logic. |
| Elements → Animation Play Rate (`AnimationPlayRate`) | Random playback-rate range for a single Animation asset; not applied to Animation Blueprints. |
| Elements → Random Animation Phase (`RandomAnimationPhase`) | Randomizes starting phase of a single Animation asset; not applied to Animation Blueprints. |

## 04 Distribution

| Option | Description |
|---|---|
| Amount Mode (`AmountMode`) | Count uses a target number; Density derives the target from estimated area times density per square meter. |
| Target Count (`TargetCount`) | Requested count in Count mode. Filters, spacing, caps and attempt limits may reduce the actual result. |
| Density Per Square Meter (`DensityPerSquareMeter`) | Estimated count per square meter in Density mode. Volume also uses XY area, not cubic volume; not an exact density for clipped regions. |
| Max Instances (`MaxInstances`) | Cap on the total requested count, before Low Density Preview scaling. |
| Max Skeletal Instances (`MaxSkeletalInstances`) | Cap on generated skeletal instances; not a performance guarantee. |
| Distribution (`Distribution`) | Random candidates, Minimum Distance spacing, Jittered Grid, or Clusters. Mesh surfaces still use triangle-random sampling; Grid on Along/Ribbon splines distributes ordered path positions. |
| Seed (`Seed`) | Global random seed for reproducible generation with the same inputs. |
| Minimum Spacing (`MinimumSpacing`) | Minimum center distance in cm. Positive values apply across distribution modes; the larger of this distance and summed element radii is used. |
| Grid Spacing (`GridSpacing`) | XY grid spacing in cm for Volume, Landscape and Spline Interior. Along/Ribbon path spacing is determined by target count instead. |
| Grid Jitter (`GridJitter`) | Grid jitter fraction (0–1), also used by the path Grid mode. |
| Cluster Count (`ClusterCount`) | Number of cluster centers for Clusters on Volume, Landscape and Spline Interior. |
| Cluster Radius (`ClusterRadius`) | Candidate radius around each cluster center, in actor-local cm. |
| Attempts Per Point (`AttemptsPerPoint`) | Candidate attempts per requested point (1–50). Higher values help restrictive filters but cost time; total attempts are capped at two million. |

## 05 Transform

| Option | Description |
|---|---|
| Uniform Scale (`UniformScale`) | Uses only the X range of Scale Min/Max and applies it uniformly to XYZ. Element Scale can still be nonuniform. |
| Scale Min (`ScaleMin`) | Lower global random scale bound; sampled per axis when Uniform Scale is off. |
| Scale Max (`ScaleMax`) | Upper global random scale bound; multiplied by element scale, scale multiplier, field and spline scale factors. |
| Position Min (`PositionMin`) | Per-axis minimum global position jitter in cm. |
| Position Max (`PositionMax`) | Per-axis maximum position jitter in cm. Random range endpoints are automatically sorted. |
| Position In Surface Space (`PositionInSurfaceSpace`) | Rotates position offsets by the basis produced by Alignment; otherwise offsets use world XYZ. |
| Rotation Min (`RotationMin`) | Lower random Pitch/Yaw/Roll bounds in degrees, applied after initial alignment. |
| Rotation Max (`RotationMax`) | Upper random Pitch/Yaw/Roll bounds in degrees. Flow Orient To Velocity can replace initial random orientation. |
| Normal Offset (`NormalOffset`) | Minimum/maximum position offset along the sampled normal, in cm. |
| Alignment (`Alignment`) | World Up, Surface Normal, Spline Tangent, a specified Direction, or facing a Look At Target actor. |
| Normal Influence (`NormalInfluence`) | Normal tilt influence (0–1) in Surface Normal mode: zero is world up; one follows the normal subject to Max Tilt. |
| Max Tilt (`MaxTilt`) | Tilt limit in degrees for Surface Normal alignment; does not limit subsequent random rotation. |
| Normal Axis (`NormalAxis`) | Mesh axis X/Y/Z that points upward in World Up or Surface Normal mode; not applied to other alignment modes. |
| Align Direction (`AlignDirection`) | World direction faced by mesh X when Alignment is Direction. |
| Look At Target (`LookAtTarget`) | Actor to face when Alignment is Look At Target. |

## 06 Filters

| Option | Description |
|---|---|
| Slope Degrees (`SlopeDegrees`) | Allowed slope range in degrees between the normal and world up: 0 is upward horizontal, 90 is vertical. |
| World Height (`WorldHeight`) | Allowed world Z height range in cm, checked after position offsets. |
| Use Noise Mask (`UseNoiseMask`) | Filters candidates and modulates acceptance probability with world-position Perlin noise. |
| Landscape Paint Layer (`LandscapePaintLayer`) | Filters by a landscape paint-layer weight. Editor generation only; save generated results for runtime use. |
| Minimum Layer Weight (`MinimumLayerWeight`) | Minimum accepted weight (0–1) of the selected paint layer. |
| Noise Size (`NoiseSize`) | Noise spatial scale in cm; larger values create broader patterns. |
| Noise Threshold (`NoiseThreshold`) | Rejects candidates below this 0–1 noise threshold; accepted candidates still have noise-modulated density. |
| Distance Field Actor (`DistanceFieldActor`) | Creates a 0–1 distance influence around the actor location. Unrelated to Unreal Mesh Distance Fields/SDF. |
| Field Inner Radius (`FieldInnerRadius`) | Inner radius in cm where field influence is 1, before inversion. |
| Field Outer Radius (`FieldOuterRadius`) | Outer radius in cm where field influence reaches 0; set above Inner Radius. |
| Invert Distance Field (`InvertDistanceField`) | Reverses distance influence to favor outside rather than the center. |
| Field Scale (`FieldScale`) | Interpolates scale from X at field 0 to Y at field 1. Without a field actor, influence is 1 and Y is used. |
| Spline Density Curve (`SplineDensityCurve`) | Acceptance curve over normalized placement progress 0–1; output clamped to 0–1. Interior evaluates at input 0. |
| Spline Scale Curve (`SplineScaleCurve`) | Scale multiplier over normalized placement progress 0–1. Not continuously reevaluated during motion; Interior uses input 0. |

## 07 Flow

| Option | Description |
|---|---|
| Enable Flow (`EnableFlow`) | Applies spline flow to every element. Default OFF; uses external Spline Actor or the built-in GuideSpline. Independent of Enable Turbulence. |
| Flow Speed (`FlowSpeed`) | Base path speed in cm/s, multiplied by Speed Multiplier. Negative values travel backward. |
| Speed Multiplier (`SpeedMultiplier`) | Per-instance random speed multiplier range sampled at generation; regenerate after changing it. |
| End Behavior (`EndBehavior`) | Loop wraps to the other end; Ping Pong reverses; Stop clamps at an end; Hide sets scale to zero outside the path. |
| Orient To Velocity (`OrientToVelocity`) | Faces mesh X along movement plus element Rotation Offset. Falls back to the path tangent when stationary or evaluating explicit time. |
| Rotation Response (`RotationResponse`) | Rotation response rate; larger values turn faster. Explicit Evaluation Time aligns immediately without this smoothing. |
| Path Attraction (`PathAttraction`) | Damps turbulence displacement; larger values reduce wobble. Not a physical spring simulation. |
| Motion Drag (`MotionDrag`) | Additional damping of turbulence displacement; does not reduce Flow Speed itself. |
| Keep On Surface (`KeepOnSurface`) | With Enable Flow ON, projects moving positions onto collision surfaces. Adds per-instance evaluation cost. |
| Ground Offset (`GroundOffset`) | Offset along the hit normal after successful Keep On Surface projection, in cm. |
| Spin Degrees Per Second (`SpinDegreesPerSecond`) | XYZ spin in degrees per second when Enable Flow is ON. X=Roll, Y=Pitch, Z=Yaw. |

## 08 Turbulence

| Option | Description |
|---|---|
| Enable Turbulence (`EnableTurbulence`) | Applies turbulence and enabled vortex effects to every element. Default OFF. Can be used alone or layered onto Flow. |
| Turbulence Strength (`TurbulenceStrength`) | Curl-noise displacement strength; default 100. Requires Enable Turbulence; zero gives no noise displacement. Path Attraction and Motion Drag attenuate it. |
| Turbulence Size (`TurbulenceSize`) | Turbulence spatial scale in cm; larger values spread variations over larger areas. |
| Turbulence Speed (`TurbulenceSpeed`) | Rate of time variation in turbulence noise. Zero gives no displacement relative to its initial noise state. |
| Enable Vortex (`EnableVortex`) | Adds vortex motion when Enable Turbulence is ON. |
| Vortex Actor (`VortexActor`) | Vortex center actor. If unset, Spline Flow uses its initial path position; other motion uses the scatter actor location. |
| Vortex Axis (`VortexAxis`) | Normalized world-space vortex axis; zero falls back to world Z. |
| Vortex Radius (`VortexRadius`) | Vortex influence radius from the axis in cm; strongest near the axis and zero at the boundary. |
| Vortex Speed (`VortexSpeed`) | Angular speed in degrees/s multiplied by radial influence; negative values reverse rotation. |
| Vortex Inward Pull (`VortexInwardPull`) | Amount of time-dependent radial contraction (0–1), pulling toward the axis within its influence. |
