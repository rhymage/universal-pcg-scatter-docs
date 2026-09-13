# Universal PCG Scatter

[전체 파라미터 참조](https://app.rhymage.com/universal-pcg-scatter-docs/parameters-ko.html)

사용자 매뉴얼 · 버전 1.0.0 · rhymage

메쉬 표면, 랜드스케이프, 스플라인, 지정 영역에 사용자의 스태틱·스켈레탈 메쉬를 배치하는 언리얼 엔진 플러그인입니다. 블루프린트 액터에서 옵션을 조절하고, PCG 그래프가 네이티브 샘플링·생성 노드를 연결합니다. 스태틱 메쉬는 인스턴스 컴포넌트로, 스켈레탈 메쉬는 개별 컴포넌트로 생성됩니다.

## 설치와 준비

- 지원 대상은 **Unreal Engine 5.8 / Windows 64비트**입니다. 다른 엔진 버전과 플랫폼은 이번 배포에 포함되지 않습니다.
- Edit → Plugins에서 **PCG**, **Universal PCG Scatter**를 활성화하고 재시작합니다.
- 소스 수동 설치는 프로젝트의 `Plugins` 아래에 `UniversalScatter` 폴더를 넣습니다. 이 경우 호환되는 Visual Studio C++ 빌드 도구가 필요합니다. Fab 설치본은 Epic의 빌드 절차를 이용합니다.
- Fab 설치본은 콘텐츠 브라우저 설정에서 **Show Engine Content(엔진 콘텐츠 표시)**와 **Show Plugin Content(플러그인 콘텐츠 표시)**를 모두 켭니다. 소스 패널에서 **All → Engine → Plugins → Universal PCG Scatter Content**를 열고, **BP_UniversalScatter**를 찾아 레벨에 배치합니다.
- Fab 설치본의 콘텐츠는 **Engine → Plugins** 아래에 있으므로 프로젝트 Content 폴더 안에서만 찾으면 보이지 않습니다. 화면에 표시되는 폴더 이름은 **Universal PCG Scatter Content**입니다(UniversalScatter Content가 아님). 그래도 보이지 않으면 콘텐츠 브라우저 검색과 필터를 해제하고, 플러그인 활성화 및 에디터 재시작 여부를 확인하세요.
- 내부 플러그인·모듈 이름은 `UniversalScatter`입니다. 소스 모듈 이름이나 에셋 경로를 임의로 변경하지 마세요.

거리 단위는 cm, 각도는 도, Flow Speed는 cm/초, Evaluation Time은 초입니다. **Region Extent는 반쪽 크기**입니다. `(500,500,250)`이면 액터 스케일 적용 전 전체 크기가 10 × 10 × 5m인 박스입니다.

## 빠른 시작: 공간에 배치하기

1. BP_UniversalScatter를 배치하고 아웃라이너에서 액터 자체를 선택합니다.
2. **01 Sources → Source = Volume**으로 설정합니다.
3. **03 Elements → Elements**에 항목을 추가하고 Type = Static Mesh로 지정한 뒤 메쉬를 넣습니다. 기본 큐브는 교체하거나 삭제합니다.
4. **04 Distribution → Target Count = 100**으로 시작합니다. 메쉬의 실제 크기에 맞게 **05 Transform → Scale Min / Scale Max**를 조절합니다.
5. **Scatter Actions**를 펼쳐 **Generate Scatter**를 누릅니다. **09 Preview → Generated Count / Generation Status**로 결과를 확인합니다.
6. 생성 관련 옵션을 바꾼 뒤에는 Generate Scatter를 다시 누릅니다. 배치를 유지하려면 레벨을 저장합니다.

## 메쉬·랜드스케이프 위에 배치하기

메쉬는 Source = Mesh Surface로 설정하고 **Surface Actors**에 대상 액터를 스포이드로 추가합니다. LOD 0 삼각형과 삼각형의 기하학적 노멀을 사용합니다. 랜드스케이프는 Source = Landscape를 선택하고 Surface Actors에 대상을 지정합니다. 랜드스케이프 샘플링과 표면 투영에는 대상의 Query Collision 및 무시되지 않는 Trace Channel이 필요합니다.

**02 Region → Limit To Region**을 켜고 Box, Sphere, Cylinder로 범위를 정합니다. 스캐터 액터를 이동·스케일하거나 Region Extent를 변경하세요. **Fit Bounds To Sources**는 대상 전체를 감싸는 시작 범위를 만듭니다. Slope Degrees로 급경사를 제외하고 World Height로 높이를 제한합니다. Normal Offset은 표면 노멀 방향으로 띄우거나 내립니다. **Project To Surface**는 Projection Direction과 Projection Distance에 따라 표면으로 투영합니다.

패키징한 게임에서 메쉬 표면 배치를 새로 생성하려면 소스 스태틱 메쉬의 **Allow CPU Access**를 쿠킹 전에 켜세요. Landscape Paint Layer 필터는 에디터 생성에서만 동작하므로 해당 필터가 필요하면 에디터에서 만든 결과를 저장해 사용하세요.

## 스플라인 배치와 흐름

외부 **Spline Actor**를 지정하거나 액터 안의 **Guide Spline**을 편집합니다. 스플라인 컴포넌트가 여러 개면 Spline Component Name을 지정합니다. Along은 중심선, Ribbon은 선 주변 띠, Interior는 닫힌 스플라인 내부입니다. Spline Width는 띠의 반폭이며 Start/End Inset은 양 끝의 제외 거리입니다. 닫힌 스플라인 내부 판정은 월드 XY 평면 기준입니다.

물고기·꽃잎 등을 흐르게 하려면:

1. Source = Spline, Spline Mode = Along 또는 Ribbon을 설정합니다.
2. 움직일 Elements 항목마다 **Motion = Spline Flow**를 선택합니다. 기본값 Static은 움직이지 않습니다.
3. **07 Flow → Enable Motion**, **Orient To Velocity**를 켭니다. Flow Speed = 100, Speed Multiplier = `(0.8,1.2)`부터 시작합니다.
4. Generate Scatter 후 **Scatter Actions → Start Preview**를 누르면 에디터에서도 움직입니다. 화면이 갱신되지 않으면 뷰포트 Realtime을 켜세요. Stop Preview는 일시 정지, Reset Motion은 초기 상태 복귀입니다.
5. Play 모드에서는 움직임이 자동 갱신됩니다. End Behavior는 Loop, Ping Pong, Stop, Hide를 제공합니다.

**물고기가 옆을 보는 경우:** **03 Elements → Elements → 물고기 항목 → Rotation Offset**에서 **Yaw = 90° 또는 −90°**를 적용하고 다시 생성합니다. 위아래로도 돌아가 있으면 Pitch/Roll을 조절하세요. Orient To Velocity는 켜 두고, 처음에는 전역 Rotation Min/Max를 모두 0으로 맞추면 방향을 확인하기 쉽습니다.

**08 Turbulence → Turbulence Strength**를 조금씩 높이면 흐름에 흔들림을 더할 수 있습니다. Turbulence Size는 공간적 크기, Turbulence Speed는 시간에 따른 변화 속도입니다. 항목의 Motion = Turbulence는 경로를 따라 전진하지 않고 초기 위치 주변에서 움직입니다. Enable Vortex를 켜고 Vortex Actor를 지정하면 명확한 중심을 가진 와류를 만들 수 있습니다. 축, 반경, 회전 속도, 안쪽 당김을 조절합니다.

Path Attraction과 Motion Drag는 터뷸런스 변위를 줄입니다. 이 움직임은 절차적 연출이며 물리 유체 시뮬레이션, 군집 AI, 장애물 회피가 아닙니다. 큰 값에서는 물체끼리 겹치거나 표면을 통과할 수 있습니다. Keep On Surface는 추가 표면 투영을 수행하므로 매 프레임 비용이 늘어납니다.

## 스켈레탈 애니메이션과 시간 제어

Elements 항목의 Type = Skeletal Mesh로 바꾸고 메쉬 및 Animation 또는 Animation Blueprint를 지정합니다. 둘 다 지정하면 Animation Blueprint가 우선합니다. Animation Play Rate와 Random Animation Phase는 단순 Animation 에셋 방식에 적용됩니다. Animation Blueprint는 자체 로직으로 재생을 제어하며 메쉬와 애니메이션의 스켈레톤이 호환되어야 합니다.

**09 Preview → Use Evaluation Time**을 켜고 **Evaluation Time**을 변경하면 특정 시간의 절차적 변환을 확인할 수 있습니다. Sequencer에서 Evaluation Time을 키로 제어할 수 있습니다. 스켈레탈 애니메이션 재생은 별도 시스템이므로 이 값만으로 애니메이션의 프레임 단위 스크러빙이나 렌더 동기화까지 보장하지 않습니다.

## 옵션 찾아보기

| 그룹 | 주요 옵션과 역할 |
| --- | --- |
| 01 Sources | 소스 종류, 표면 액터, 스플라인 액터·컴포넌트, 스플라인 모드·반폭·끝 제외, 표면 투영 방향·거리·채널. |
| 02 Region | 영역 형태·반쪽 크기, 박스·구·원통 가장자리 밀도 감소, 제외 액터·여유 거리. 닫힌 스플라인은 XY 내부를 제외하고 일반 액터는 바운드를 이용합니다. |
| 03 Elements | 사용 여부, 메쉬 종류·에셋, 선택 가중치, 개별 시드, 크기·크기 배율, 회전·위치 보정, 분리 반경, 머티리얼, 그림자, 충돌, 컬링 거리, 움직임·애니메이션. Sequential Selection은 가중치 대신 순서대로 선택합니다. |
| 04 Distribution | 개수 또는 대략적 ㎡당 밀도, 목표·최대 개수, 스켈레탈 상한, 시드, 랜덤·최소 거리·지터 격자·클러스터, 간격·격자 흔들림·군집 수와 반경·시도 횟수. |
| 05 Transform | 균일·비균일 크기 범위, 위치·회전 랜덤 범위, 표면 좌표계, 노멀 오프셋, 월드 업·노멀·접선·지정 방향·타깃 보기 정렬, 노멀 영향·기울기 상한·노멀 축. |
| 06 Filters | 경사·월드 높이, 노이즈 마스크, 에디터 랜드스케이프 레이어, 액터 중심 거리 필드·안팎 반경·반전·크기 배율, 스플라인 밀도·크기 커브. 커브 입력은 0~1로 정규화한 경로 진행률입니다. |
| 07 Flow | 전체 움직임, 속도와 랜덤 배율, 끝 처리, 진행 방향 정렬과 반응, 터뷸런스 감쇠, 표면 유지·높이·회전 속도. |
| 08 Turbulence | 터뷸런스 강도·크기·속도, 와류 중심·축·반경·각속도·안쪽 당김. |
| 09 Preview | 자동 재생성, 저밀도 생성·비율, 바운드·포인트 표시, 평가 시간, 생성 개수와 상태. |

**Scatter Actions:** Generate Scatter는 재생성, Cleanup Scatter는 생성 컴포넌트 제거, Reseed는 시드를 바꾸고 재생성, Start/Stop Preview는 에디터 움직임 제어, Reset Motion은 시간 초기화, Fit Bounds To Sources는 소스 기준 영역 맞춤입니다.

## 활용 예시

- **폴리지:** Mesh Surface 또는 Landscape, Static Mesh와 Motion = Static, Minimum Distance, Surface Normal 정렬, 완만한 크기 변화, 경사 제한.
- **물고기 무리:** Spline Ribbon, Spline Flow, 전역 회전 랜덤 0, 메쉬별 Rotation Offset 보정, 작은 속도 편차와 터뷸런스, Start Preview.
- **경로 주변 꽃잎:** Spline Ribbon, Spline Flow, 위치·크기 편차, 약한 터뷸런스와 Spin. 장식용이면 충돌을 끕니다.
- **공중 군집:** Volume, Clusters, Motion = Turbulence. 중심이 분명한 Vortex Actor를 지정합니다.

## 성능·제약·문제 해결

- Target Count는 보장 개수가 아닙니다. 필터·범위·간격·잘못된 에셋·시도 상한으로 결과가 줄 수 있습니다. 상태를 확인하고 범위를 넓히거나 간격·필터를 완화한 뒤 시도 횟수를 조절하세요.
- Density는 소스 면적 기반 추정치이며 잘린 영역의 정확한 밀도가 아닙니다. Volume도 ㎥가 아닌 투영 면적 기준입니다.
- 공간 격자·클러스터 패턴은 Volume/Landscape 샘플링에 적용됩니다. 스플라인 격자는 경로를 따라 순서를 만들며 삼각형 메쉬는 랜덤 샘플링입니다. 닫힌 스플라인의 가장자리 페이드 기능은 구현되어 있지 않습니다.
- 스켈레탈은 개별 컴포넌트이므로 적은 수부터 시작하세요. 기본 상한 300은 성능 보장이 아닙니다. 움직이는 스태틱 인스턴스도 매 프레임 CPU 갱신 비용이 있습니다.
- 기본 플레이스홀더는 언리얼 기본 도형입니다. 물고기·폴리지·꽃잎 모델, 애니메이션, 타사 환경 팩은 포함하지 않습니다. 이용 권한이 있는 에셋을 직접 지정하세요.
- 에디터에서 멈춰 있으면 액터 선택 → Scatter Actions → Start Preview, 뷰포트 Realtime, 개별 Motion, Enable Motion을 확인하세요. Use Evaluation Time을 켜면 실시간 시계 대신 지정 시간을 사용합니다.
- 생성되지 않으면 메쉬 지정, Enabled, 양수 Weight, 영역 겹침, 소스 액터, 충돌·투영, 필터, 최대 개수를 확인하세요.
- 소스·요소·분포를 변경하면 다시 생성하세요. 큰 배치에서는 Auto Regenerate를 끄는 편이 편집 부담을 줄입니다.
- C++ 런타임 모듈과 블루프린트 컨트롤·PCG 그래프를 포함한 플러그인입니다. 블루프린트 전용 에셋, Niagara 시스템, 멀티플레이 동기화 솔루션은 아닙니다.

## 지원

퍼블리셔: [rhymage](https://www.rhymage.com/). [GitHub Issues](https://github.com/rhymage/universal-pcg-scatter-docs/issues)에 엔진 버전, 소스 모드, 재현 방법, 관련 로그를 남겨 주세요. 공개 게시 전 개인 경로와 비공개 프로젝트 정보는 제거하세요. 공개 저장소는 매뉴얼 전용이며 플러그인은 Fab을 통해 별도 배포합니다.
