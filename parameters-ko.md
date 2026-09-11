# Universal PCG Scatter — 전체 파라미터 참조

01~09의 모든 공개 옵션, Elements의 하위 설정, Scatter Actions를 코드 정의와 대조한 목록입니다. 영어 옵션명으로 검색할 수 있습니다. cm, 도, 초를 사용하며 XYZ는 축 성분, Pitch/Yaw/Roll은 회전 성분입니다. 기본값은 블루프린트·인스턴스에 따라 달라질 수 있으므로 이 페이지는 기능을 설명합니다. 생성 관련 옵션을 바꾸면 Generate Scatter를 실행하세요. 흐름은 절차적 연출이며 유체 물리·군집 AI·장애물 회피가 아닙니다.

## 01 Sources

| Option | 설명 |
|---|---|
| Source (`Source`) | 배치 소스: Mesh Surface는 메쉬 LOD 0 삼각형, Landscape는 지면 투영, Spline은 경로, Volume은 부피 안에서 생성합니다. |
| Surface Actors (`SurfaceActors`) | 표면 소스 액터 목록입니다. 투영 시에도 이 목록으로 대상을 제한합니다. 비워 두면 투영은 조건에 맞는 충돌 표면을 검색합니다. |
| Spline Actor (`SplineActor`) | 외부 스플라인을 가진 액터입니다. 미지정이면 내장 Guide Spline을 사용합니다. |
| Spline Component Name (`SplineComponentName`) | 외부 액터에 스플라인이 여러 개일 때 사용할 컴포넌트 이름입니다. None은 첫 스플라인이며, 일치하지 않으면 실패합니다. |
| Spline Mode (`SplineMode`) | Along: 중심선, Ribbon: 중심선 좌우 띠, Interior: 닫힌 스플라인의 월드 XY 내부입니다. Interior도 Region Extent의 후보 영역 안에서 샘플링합니다. |
| Spline Width (`SplineWidth`) | Ribbon의 반폭(cm)입니다. 200은 전체 폭 400cm입니다. 경로 소스의 밀도 추정에도 사용합니다. |
| Spline Start Inset (`SplineStartInset`) | 초기 배치에서 경로 시작 부분을 제외할 거리입니다. 이후 Flow 이동을 이 구간으로 제한하지는 않습니다. |
| Spline End Inset (`SplineEndInset`) | 초기 배치에서 경로 끝부분을 제외할 거리입니다. 이후 Flow 이동은 전체 경로를 사용합니다. |
| Project To Surface (`ProjectToSurface`) | 후보 위치를 충돌 표면에 투영합니다. Landscape 모드는 이 값과 무관하게 투영합니다. |
| Projection Direction (`ProjectionDirection`) | 투영 방향 벡터입니다. 정규화하며 0 벡터면 월드 아래쪽 (0,0,-1)을 사용합니다. |
| Projection Distance (`ProjectionDistance`) | 후보 위치 중심으로 양방향 절반씩 검사하는 전체 투영 길이(cm)입니다. |
| Trace Channel (`TraceChannel`) | 표면의 해당 채널 응답이 Ignore면 투영 대상에서 제외합니다. 표면에 query collision이 필요합니다. |

## 02 Region

| Option | 설명 |
|---|---|
| Limit To Region (`LimitToRegion`) | 최종 후보가 지정 영역 내부에 있는지 제한합니다. 꺼도 Volume 등의 최초 후보 생성 범위는 Region Extent를 사용합니다. |
| Region (`Region`) | Box: 로컬 박스, Sphere: Extent 최솟값이 반지름, Cylinder: XY 최솟값 반지름과 Z 반높이, Closed Spline: 닫힌 경로의 XY 내부와 로컬 Z 높이 제한입니다. |
| Region Extent (`RegionExtent`) | 액터 로컬 기준 반쪽 크기(cm)입니다. (500,500,250)은 스케일 적용 전 10×10×5m 박스입니다. |
| Edge Falloff (`EdgeFalloff`) | 0~1 범위의 경계 밀도 감쇠 폭입니다. 0은 감쇠 없음. Limit To Region이 꺼져 있거나 Closed Spline이면 감쇠하지 않습니다. |
| Exclusion Actors (`ExclusionActors`) | 일반 액터는 바운드 내부, 닫힌 스플라인은 XY 내부, 열린 스플라인은 Padding 거리 이내를 제외합니다. |
| Exclusion Padding (`ExclusionPadding`) | 일반 액터 바운드를 확장하거나 열린 스플라인 주변을 제외할 거리(cm)입니다. 닫힌 스플라인 내부에는 적용하지 않습니다. |

## 03 Elements

| Option | 설명 |
|---|---|
| Elements (`Elements`) | 배치할 요소 배열입니다. 각 항목에 메쉬와 가중치·변형·움직임을 설정합니다. 아래 하위 항목 표를 참고하세요. |
| Sequential Selection (`SequentialSelection`) | 가중 랜덤 대신 유효한 요소를 순서대로 선택합니다. Enabled, 유효 메쉬, 양수 Weight는 여전히 필요하며 필터 탈락으로 순서가 건너뛸 수 있습니다. |
| Elements → Label (`Label`) | 요소를 구분하는 이름입니다. 배치 확률이나 움직임을 바꾸지는 않습니다. |
| Elements → Enabled (`Enabled`) | 이 요소를 생성 후보에 포함할지 설정합니다. |
| Elements → Type (`Type`) | Static Mesh는 인스턴스 컴포넌트, Skeletal Mesh는 개별 스켈레탈 컴포넌트를 생성합니다. |
| Elements → Static Mesh (`StaticMesh`) | Type이 Static Mesh일 때 배치할 메쉬입니다. |
| Elements → Skeletal Mesh (`SkeletalMesh`) | Type이 Skeletal Mesh일 때 사용할 메쉬입니다. 애니메이션과 스켈레톤이 호환되어야 합니다. |
| Elements → Weight (`Weight`) | 요소 선택의 상대 가중치입니다. 0이면 제외합니다. Sequential Selection에서는 양수 여부만 사용합니다. |
| Elements → Seed Offset (`SeedOffset`) | 이 요소의 변형 랜덤과 단일 애니메이션 랜덤에 더하는 시드 값입니다. 전역 Seed와 함께 결과를 결정합니다. |
| Elements → Scale (`Scale`) | 요소의 XYZ 기본 크기 배율입니다. 전역 랜덤 크기 및 다른 크기 배율과 곱합니다. |
| Elements → Scale Multiplier (`ScaleMultiplier`) | 요소별 균일 랜덤 크기 배율의 최소·최대값입니다. |
| Elements → Rotation Offset (`RotationOffset`) | 메쉬별 회전 보정입니다. 옆을 보는 물고기는 Yaw ±90°부터 확인하세요. 진행 방향 정렬에도 유지됩니다. |
| Elements → Position Offset (`PositionOffset`) | 요소별 위치 보정(cm)입니다. 전역 위치 랜덤과 합산하며 Position In Surface Space 설정을 따릅니다. |
| Elements → Separation Radius (`SeparationRadius`) | 요소의 배치 간격 판정용 구 반지름입니다. 생성 크기에 따라 확대됩니다. 실제 메쉬 충돌 검사나 이동 중 회피가 아닙니다. |
| Elements → Materials (`Materials`) | 머티리얼 슬롯 순서대로 덮어쓸 목록입니다. 비어 있는 항목은 원본 슬롯을 유지합니다. |
| Elements → Cast Shadow (`CastShadow`) | 생성 컴포넌트의 그림자 표시 여부입니다. |
| Elements → Collision (`Collision`) | 켜면 Query and Physics 충돌을 활성화합니다. 물리 시뮬레이션이나 장애물 회피를 자동으로 켜지는 않습니다. |
| Elements → Cull Distance (`CullDistance`) | 거리 컬링 한계(cm)입니다. 스태틱은 인스턴스 컬링, 스켈레탈은 컴포넌트 컬링을 사용합니다. 0은 거리 제한 해제입니다. |
| Elements → Motion (`Motion`) | Static: 절차적 이동 없음, Spline Flow: 스플라인 이동, Turbulence: 초기 위치 주변 변위입니다. Static이어도 스켈레탈 애니메이션은 별도로 재생될 수 있습니다. |
| Elements → Animation (`Animation`) | 단일 애니메이션 에셋을 반복 재생합니다. Animation Blueprint가 지정되면 이 설정보다 우선합니다. |
| Elements → Animation Blueprint (`AnimationBlueprint`) | 스켈레탈 메쉬에 적용할 Anim Instance 클래스입니다. 재생 속도·위상은 해당 블루프린트 로직에서 제어합니다. |
| Elements → Animation Play Rate (`AnimationPlayRate`) | 단일 Animation 에셋 재생 속도의 랜덤 최소·최대 배율입니다. Animation Blueprint에는 적용되지 않습니다. |
| Elements → Random Animation Phase (`RandomAnimationPhase`) | 단일 Animation의 시작 시간을 임의로 분산합니다. Animation Blueprint에는 적용되지 않습니다. |

## 04 Distribution

| Option | 설명 |
|---|---|
| Amount Mode (`AmountMode`) | Count는 목표 개수, Density는 추정 면적×㎡당 밀도로 목표를 계산합니다. |
| Target Count (`TargetCount`) | Count 모드의 목표 개수입니다. 필터·간격·최대 개수·시도 상한으로 실제 결과는 줄어들 수 있습니다. |
| Density Per Square Meter (`DensityPerSquareMeter`) | Density 모드에서 추정 ㎡당 개수입니다. Volume도 부피가 아닌 XY 면적 기준이며 잘린 영역의 정확한 밀도가 아닙니다. |
| Max Instances (`MaxInstances`) | 전체 목표 개수 상한입니다. 이후 Low Density Preview 비율이 적용됩니다. |
| Max Skeletal Instances (`MaxSkeletalInstances`) | 생성할 스켈레탈 개수 상한입니다. 성능 보장 수치는 아닙니다. |
| Distribution (`Distribution`) | Random: 랜덤 후보, Minimum Distance: 간격 검사, Jittered Grid: 지터 격자, Clusters: 군집입니다. 메쉬 표면은 삼각형 랜덤 샘플이며, 스플라인 Along/Ribbon의 Grid는 경로 순서 배치입니다. |
| Seed (`Seed`) | 전체 랜덤 시드입니다. 같은 입력·시드로 재현 가능한 배치를 만듭니다. |
| Minimum Spacing (`MinimumSpacing`) | 점 중심 사이 최소 거리(cm)입니다. 0보다 크면 다른 분포 모드에도 적용하며 요소 반지름 합과 비교해 큰 값을 사용합니다. |
| Grid Spacing (`GridSpacing`) | 공간 격자의 XY 간격(cm)입니다. Volume·Landscape·Spline Interior에 사용하며 Along/Ribbon 경로 간격은 목표 개수로 정해집니다. |
| Grid Jitter (`GridJitter`) | 격자 위치 흔들림 비율(0~1)입니다. 경로 Grid에서도 사용합니다. |
| Cluster Count (`ClusterCount`) | 군집 중심 개수입니다. Volume·Landscape·Spline Interior의 Clusters에 적용합니다. |
| Cluster Radius (`ClusterRadius`) | 군집 중심 주변 후보 반지름(cm, 액터 로컬 공간)입니다. |
| Attempts Per Point (`AttemptsPerPoint`) | 목표 개수당 후보 시도 횟수(1~50)입니다. 필터가 엄격하면 늘릴 수 있지만 생성 시간이 늘며 전체 시도는 200만 회로 제한됩니다. |

## 05 Transform

| Option | 설명 |
|---|---|
| Uniform Scale (`UniformScale`) | 켜면 Scale Min/Max의 X값만 뽑아 XYZ에 동일하게 적용합니다. 요소의 Scale은 여전히 비균일할 수 있습니다. |
| Scale Min (`ScaleMin`) | 전역 랜덤 크기 배율 하한입니다. Uniform Scale이 꺼지면 축별로 샘플링합니다. |
| Scale Max (`ScaleMax`) | 전역 랜덤 크기 배율 상한입니다. 요소 Scale·Scale Multiplier·필드·스플라인 크기 커브와 곱해집니다. |
| Position Min (`PositionMin`) | 전역 랜덤 위치 보정의 축별 최소값(cm)입니다. |
| Position Max (`PositionMax`) | 전역 랜덤 위치 보정의 축별 최대값(cm)입니다. 랜덤 범위는 양 끝 순서를 자동 정렬합니다. |
| Position In Surface Space (`PositionInSurfaceSpace`) | 켜면 현재 Alignment가 만든 좌표계로 위치 보정을 회전합니다. 끄면 월드 XYZ 보정입니다. |
| Rotation Min (`RotationMin`) | 전역 랜덤 Pitch/Yaw/Roll 하한(도)입니다. 초기 정렬 뒤에 적용합니다. |
| Rotation Max (`RotationMax`) | 전역 랜덤 Pitch/Yaw/Roll 상한(도)입니다. Flow의 Orient To Velocity는 초기 랜덤 방향을 대체할 수 있습니다. |
| Normal Offset (`NormalOffset`) | 샘플 노멀 방향 위치 보정의 최소·최대 거리(cm)입니다. |
| Alignment (`Alignment`) | World Up: 월드 위, Surface Normal: 표면 노멀, Spline Tangent: 경로 접선, Direction: 지정 벡터, Look At Target: 타깃 액터 방향입니다. |
| Normal Influence (`NormalInfluence`) | Surface Normal 모드의 노멀 기울기 영향(0~1)입니다. 0은 월드 위 기준, 1은 노멀을 최대한 따릅니다. |
| Max Tilt (`MaxTilt`) | Surface Normal 모드의 노멀 정렬 기울기 한계(도)입니다. 이후 랜덤 회전까지 제한하는 값은 아닙니다. |
| Normal Axis (`NormalAxis`) | World Up/Surface Normal에서 위로 향하게 할 메쉬 축 X/Y/Z입니다. 다른 정렬 모드에는 적용하지 않습니다. |
| Align Direction (`AlignDirection`) | Alignment = Direction에서 메쉬 X축이 바라볼 월드 방향 벡터입니다. |
| Look At Target (`LookAtTarget`) | Alignment = Look At Target에서 바라볼 액터입니다. |

## 06 Filters

| Option | 설명 |
|---|---|
| Slope Degrees (`SlopeDegrees`) | 허용 표면 경사의 최소·최대값(도)입니다. 월드 위와 노멀 사이 각도이며 0은 위쪽 수평, 90은 수직입니다. |
| World Height (`WorldHeight`) | 허용 월드 Z 높이 최소·최대(cm)입니다. 위치 보정 후 검사합니다. |
| Use Noise Mask (`UseNoiseMask`) | 월드 위치 기반 Perlin 노이즈로 후보를 거르고 수락 확률을 조절합니다. |
| Landscape Paint Layer (`LandscapePaintLayer`) | 지정 랜드스케이프 페인트 레이어의 가중치로 거릅니다. 에디터 생성 전용이므로 결과를 저장해 사용하세요. |
| Minimum Layer Weight (`MinimumLayerWeight`) | 지정 페인트 레이어의 최소 허용 가중치(0~1)입니다. |
| Noise Size (`NoiseSize`) | 노이즈의 공간 크기(cm)입니다. 큰 값은 넓고 완만한 패턴을 만듭니다. |
| Noise Threshold (`NoiseThreshold`) | 0~1 노이즈 값이 이보다 작은 후보를 제외합니다. 통과 후에도 노이즈 값으로 밀도를 조절합니다. |
| Distance Field Actor (`DistanceFieldActor`) | 액터 중심에서의 거리로 0~1 필드를 만듭니다. Unreal Mesh Distance Field/SDF 기능과는 별개입니다. |
| Field Inner Radius (`FieldInnerRadius`) | 필드가 1인 내부 반지름(cm)입니다. Invert 시 반전됩니다. |
| Field Outer Radius (`FieldOuterRadius`) | 필드가 0이 되는 외부 반지름(cm)입니다. Inner보다 크게 설정하세요. |
| Invert Distance Field (`InvertDistanceField`) | 거리 필드 0과 1을 뒤집어 중심 대신 바깥쪽을 선택합니다. |
| Field Scale (`FieldScale`) | 필드 0에서 X, 필드 1에서 Y의 크기 배율을 보간합니다. Distance Field Actor가 없으면 필드 1이므로 Y를 사용합니다. |
| Spline Density Curve (`SplineDensityCurve`) | 경로 배치 진행률 0~1에 따른 수락 확률 커브입니다. 출력은 0~1로 제한합니다. Interior에서는 입력이 항상 0입니다. |
| Spline Scale Curve (`SplineScaleCurve`) | 경로 배치 진행률 0~1에 따른 크기 배율입니다. 이동 중 계속 재평가하지 않습니다. Interior에서는 입력 0입니다. |

## 07 Flow

| Option | 설명 |
|---|---|
| Enable Motion (`EnableMotion`) | 절차적 움직임의 전체 스위치입니다. 각 요소의 Motion도 Static이 아니어야 합니다. 스켈레탈 애니메이션은 별도입니다. |
| Flow Speed (`FlowSpeed`) | 기본 경로 속도(cm/초)입니다. Speed Multiplier와 곱하며 음수는 반대 방향입니다. |
| Speed Multiplier (`SpeedMultiplier`) | 생성 시 정하는 인스턴스별 속도 랜덤 배율의 최소·최대입니다. 변경 후 다시 생성하세요. |
| End Behavior (`EndBehavior`) | Loop: 반대 끝으로 순환, Ping Pong: 왕복, Stop: 끝에서 정지, Hide: 범위를 벗어나면 크기 0으로 숨김입니다. |
| Orient To Velocity (`OrientToVelocity`) | 이동 방향으로 메쉬 X축을 정렬하고 요소 Rotation Offset을 더합니다. 정지/평가 시간에서는 경로 접선을 대안으로 사용합니다. |
| Rotation Response (`RotationResponse`) | 진행 방향 회전의 응답 속도입니다. 클수록 빠르게 정렬합니다. Evaluation Time은 보간 없이 즉시 정렬합니다. |
| Path Attraction (`PathAttraction`) | 터뷸런스 변위를 감쇠하는 값입니다. 클수록 경로 주변 흔들림이 줄며 물리적 스프링 시뮬레이션은 아닙니다. |
| Motion Drag (`MotionDrag`) | 터뷸런스 변위를 줄이는 추가 감쇠입니다. Flow Speed 자체를 늦추는 값은 아닙니다. |
| Keep On Surface (`KeepOnSurface`) | 매 움직임 평가 시 표면 투영을 추가합니다. 충돌 표면이 필요하며 인스턴스 수에 따라 비용이 증가합니다. |
| Ground Offset (`GroundOffset`) | Keep On Surface 투영 성공 시 노멀 방향으로 더할 거리(cm)입니다. |
| Spin Degrees Per Second (`SpinDegreesPerSecond`) | XYZ축 회전 속도(도/초)입니다. X=Roll, Y=Pitch, Z=Yaw이며 움직이는 요소에 적용합니다. |

## 08 Turbulence

| Option | 설명 |
|---|---|
| Turbulence Strength (`TurbulenceStrength`) | Curl 노이즈 기반 위치 변위 크기입니다. 0이면 꺼집니다. Path Attraction·Motion Drag에 의해 줄어듭니다. |
| Turbulence Size (`TurbulenceSize`) | 터뷸런스 공간 크기(cm)입니다. 큰 값은 더 넓은 공간에서 비슷한 변화를 만듭니다. |
| Turbulence Speed (`TurbulenceSpeed`) | 시간에 따른 터뷸런스 노이즈 변화 속도입니다. 0이면 초기 대비 변위가 생기지 않습니다. |
| Enable Vortex (`EnableVortex`) | 움직이는 요소에 중심축 주변의 와류 회전을 적용합니다. |
| Vortex Actor (`VortexActor`) | 와류 중심 액터입니다. 미지정이면 Spline Flow는 초기 경로 위치, 그 외에는 스캐터 액터 위치를 중심으로 합니다. |
| Vortex Axis (`VortexAxis`) | 와류 회전축의 월드 방향입니다. 정규화하며 0 벡터는 월드 Z를 사용합니다. |
| Vortex Radius (`VortexRadius`) | 축에서 이 거리(cm)까지 와류 영향을 줍니다. 축에 가까울수록 강하고 경계에서 0이 됩니다. |
| Vortex Speed (`VortexSpeed`) | 와류 각속도(도/초)이며 거리 영향이 곱해집니다. 음수로 회전 방향을 반전합니다. |
| Vortex Inward Pull (`VortexInwardPull`) | 시간에 따라 반경을 줄이는 정도(0~1)입니다. 영향 범위 안에서 중심축으로 당깁니다. |

## 09 Preview

| Option | 설명 |
|---|---|
| Auto Regenerate (`AutoRegenerate`) | 에디터 Construction 실행 시 자동으로 다시 생성합니다. 큰 배치는 끄고 Generate Scatter를 수동 실행하는 편이 좋습니다. |
| Low Density Preview (`LowDensityPreview`) | 목표 개수에 Preview Fraction을 곱해 적게 생성합니다. 생성 함수 공통 옵션이라 런타임 생성에도 적용됩니다. |
| Preview Fraction (`PreviewFraction`) | Low Density Preview의 생성 비율(0.01~1)입니다. 0.2는 목표의 약 20%입니다. |
| Show Bounds (`ShowBounds`) | 에디터에서 바운드 가이드를 표시합니다. 영역 제한 자체를 켜거나 끄지는 않습니다. |
| Show Points (`ShowPoints`) | 에디터에서 생성된 초기 위치에 청록색 디버그 점을 표시합니다. 이동 후 현재 위치 표시가 아닙니다. |
| Evaluation Time (`EvaluationTime`) | 명시적으로 평가할 절차적 움직임 시간(초)입니다. Use Evaluation Time과 Enable Motion을 켜며 Sequencer 키 설정이 가능합니다. |
| Use Evaluation Time (`UseEvaluationTime`) | 실시간 누적 대신 Evaluation Time을 사용합니다. 스켈레탈 애니메이션 프레임까지 동기화하지는 않습니다. |
| Generated Count (`GeneratedCount`) | 실제로 생성된 인스턴스 수를 보여주는 읽기 전용 값입니다. |
| Generation Status (`GenerationStatus`) | 생성 결과·목표 개수 또는 소스/메쉬 설정 오류를 보여주는 읽기 전용 메시지입니다. |

## Scatter Actions

| Option | 설명 |
|---|---|
| Generate Scatter (`GenerateScatter`) | PCG 그래프를 실행해 현재 설정으로 다시 생성합니다. PCG 컴포넌트에 PCG_UniversalScatter 그래프가 필요합니다. |
| Cleanup Scatter (`CleanupScatter`) | 이 액터가 생성한 컴포넌트와 PCG 생성 결과를 제거합니다. 원본 소스 액터와 메쉬 에셋은 유지합니다. |
| Reseed (`Reseed`) | 전역 Seed를 새 값으로 바꾸고 다시 생성합니다. |
| Start Preview (`StartPreview`) | 에디터 절차적 움직임의 시간 재생을 시작/재개합니다. 생성, Enable Motion, 요소 Motion, 뷰포트 Realtime도 확인하세요. |
| Stop Preview (`StopPreview`) | 에디터 실시간 미리보기 시계를 멈춥니다. 런타임이나 Use Evaluation Time 평가, 스켈레탈 애니메이션 전체를 정지하는 버튼은 아닙니다. |
| Reset Motion (`ResetMotion`) | 누적 시간과 Evaluation Time을 0으로 돌리고 절차적 변환을 시간 0으로 평가합니다. 다시 생성하지 않습니다. |
| Fit Bounds To Sources (`FitBoundsToSources`) | Surface Actors와 외부 Spline Actor 바운드를 합쳐 액터 위치·Region Extent를 맞춥니다. 내장 Guide Spline은 포함하지 않으며 회전·비균일 스케일에서는 결과를 확인하세요. |
