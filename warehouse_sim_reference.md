# 물류센터 AGV·AMR 시뮬레이션 — 이미지 생성 프롬프트 & 제작 가이드

> 사용법: **1부**의 프롬프트를 ChatGPT(이미지 생성)에 한 블록씩 그대로 붙여 넣으세요.
> 생성된 이미지는 "분위기·배치 참고용"입니다. 치수와 구조는 **2부의 실제 치수**와 실제 사진을 기준으로 모델링하세요. AI 이미지는 구조가 틀리는 경우가 많습니다.
> 2부의 치수는 업계에서 흔히 쓰는 **대략적인 참고치**입니다. 제품마다 다릅니다.

---

## 0. 용어 정리 (한 줄 요약)

| 용어 | 쉬운 설명 |
|---|---|
| **AGV** (Automated Guided Vehicle, 무인운반차) | 바닥에 그려진 선(또는 자석 테이프·QR 코드)을 **정해진 길로만** 따라가는 운반 로봇. 기차처럼 레일이 정해져 있다고 생각하면 됨 |
| **AMR** (Autonomous Mobile Robot, 자율이동로봇) | 센서로 주변을 보고 **스스로 길을 찾아** 이동하는 로봇. 장애물이 있으면 돌아감. 내비게이션 켠 자동차에 가까움 |
| **TA** (Technical Artist, 테크니컬 아티스트) | 아티스트와 프로그래머 사이의 다리. 모델이 엔진에서 **예쁘게 + 가볍게** 돌아가도록 머티리얼·셰이더·최적화·임포트 규칙을 책임지는 사람 |
| **PBR** (Physically Based Rendering) | 실제 물리처럼 빛을 계산하는 렌더링 방식. 텍스처를 색(BaseColor)·거칠기(Roughness)·금속성(Metallic)·요철(Normal)로 나눠서 만듦 |
| **ORM 텍스처** | Occlusion(그늘)·Roughness·Metallic 흑백 맵 3장을 한 장의 R·G·B 채널에 합친 것. 언리얼에서 텍스처 수를 줄이는 표준 방식 |
| **UV 스크롤** | 모델은 가만히 두고 **텍스처만 한 방향으로 계속 밀어서** 움직이는 것처럼 보이게 하는 기법. 컨베이어 벨트나 흐르는 물에 사용 |
| **LOD** (Level of Detail) | 멀리 있을 때 자동으로 폴리곤이 적은 버전으로 바뀌게 하는 것. 올포랜드에서 다룬 LOD 2.0/2.5와 개념은 같고 목적이 "성능"이라는 점이 다름 |
| **피킹 스테이션** | 로봇이 가져온 선반·박스에서 작업자가 주문 상품을 꺼내 포장하는 작업대 |
| **도크** | 트럭이 건물에 붙어 짐을 싣고 내리는 출입구 |
| **랙** | 팔레트를 층층이 올려두는 철제 선반 |
| **팔레트** | 짐을 올려 지게차·AGV로 옮기는 받침대 (보통 나무·플라스틱) |

---

## 1부. GPT 이미지 생성 프롬프트

> 팁: 첫 이미지가 마음에 들면 같은 대화에서 "같은 스타일로 ~도 그려줘"라고 이어가면 스타일이 유지됩니다.
> 텍스트(글자)는 AI 이미지에서 깨지기 쉬우니 라벨은 나중에 직접 넣는 걸 권장합니다.

### 1-1. 전체 완성 모습 (조감도) — "최종 목표 이미지"

```
Create a high-quality 3D real-time render of a modern automated logistics warehouse interior,
viewed from a high isometric bird's-eye angle (about 45 degrees), as if made in Unreal Engine 5.

Layout (left to right):
- Left wall: 3 truck loading dock doors (inbound area) with yellow-black safety stripes.
- Center: rows of orange-and-blue steel selective pallet racks, 4 levels high, holding wooden pallets
  with cardboard boxes, separated by wide aisles. One cross aisle cuts through the middle.
- Right side: a picking station area with roller conveyors and two packing tables,
  then 2 outbound dock doors on the right wall.
- Bottom front: a row of 4 robot charging stations against the wall.

Robots:
- Several low, flat, rectangular yellow AGVs (about knee height) driving along a green dashed guide line
  painted on the floor that loops around the rack area; one AGV is carrying a pallet on its lifting top.
- Several small square grey AMRs carrying tall movable shelf units, moving freely through the aisles.
- Each robot has a small status light (green = moving, blue = charging, red = stopped).

Floor: polished light grey epoxy concrete with yellow aisle boundary lines and pedestrian walkway markings.
Lighting: bright, clean industrial LED ceiling lights, soft shadows, realistic PBR materials
(painted metal, worn plastic, cardboard, concrete).
Style: clean industrial digital twin visualization, no people, no text, no logos, no brand names.
Aspect ratio 16:9.
```

### 1-2. 위에서 내려다본 평면도 (CAD 참고용)

```
Create a clean top-down 2D architectural floor plan of an automated logistics warehouse,
about 60 m wide and 40 m deep, in a flat CAD drawing style (white background, thin black lines,
light color fills only for zones).

Zones:
- Left edge: inbound dock area (light teal) with 3 dock doors on the outer wall.
- Center: 8 rack blocks arranged in 4 rows x 2 columns (grey rectangles), with a vertical cross aisle
  in the middle and horizontal aisles between rows.
- Right edge: picking station area (light coral) at top, outbound dock area (light teal) at bottom,
  2 dock doors on the outer wall.
- Bottom wall: charging station zone (light amber) and idle robot parking zone (light purple).
- A green dashed rectangular loop line around the rack area = AGV fixed route.
- A purple dotted line with an arrow going from the parking zone into the cross aisle = AMR free path.
- Simple dimension lines on the outer walls.

No perspective, no shadows, no 3D, no text labels. Aspect ratio 3:2.
```

### 1-3. 에셋 레퍼런스 시트 (에셋별로 하나씩)

공통으로 문장 끝에 붙일 문구:
```
Show it as a 3D model reference sheet on a plain light grey background:
front view, side view, top view and one 3/4 perspective view, arranged in a grid.
Realistic PBR materials, neutral studio lighting, no text, no logos, no brand names.
```

**(A) 팔레트 랙**
```
A single bay of a steel selective pallet rack for a warehouse: two blue upright frames with
diagonal bracing and perforated holes, orange horizontal box beams at 4 levels, wire mesh decking,
two wooden pallets with stacked cardboard boxes on the lower levels, base plates bolted to the floor,
yellow column guards at the bottom.
[공통 문구]
```

**(B) AGV (팔레트 리프트형)**
```
A low-profile rectangular warehouse AGV (automated guided vehicle), about 1.2 m long, 0.8 m wide,
0.3 m tall, painted industrial yellow with dark grey bumpers. Flat top lifting plate that can rise
to lift a pallet. Front and rear safety laser scanners (small black windows), an emergency stop button,
a thin LED status light strip around the body, small hidden wheels. Show one view with the lifting
plate raised and carrying a wooden pallet.
[공통 문구]
```

**(C) AMR + 이동식 선반(선반 운반형)**
```
A compact square autonomous mobile robot (AMR), about 0.8 m x 0.8 m, 0.3 m tall, light grey body
with a round lifting top, front camera and lidar sensor, blue LED ring. Next to it, the tall movable
shelf unit (about 1 m x 1 m x 2 m, 4 levels, metal frame with fabric bins) that the robot drives under
and lifts. Show one view where the robot is under the shelf, lifting it slightly off the floor.
[공통 문구]
```

**(D) 롤러 컨베이어 + 피킹 스테이션**
```
A warehouse picking station: a straight powered roller conveyor section (about 3 m long, 0.6 m wide,
0.75 m high) and a 90-degree curved roller conveyor module, plus a packing workbench with a
computer monitor, a barcode scanner, a light curtain frame, and a tote bin.
[공통 문구]
```

**(E) 로봇 충전 스테이션**
```
A wall-mounted charging dock for warehouse robots: a slim grey box with two copper charging contact
plates near floor level, a green/blue status LED, yellow-black floor marking box in front of it
showing the parking position, and a small robot docked into it.
[공통 문구]
```

**(F) 도크 도어**
```
A warehouse truck loading dock: a sectional overhead roll-up door (about 3 m x 3 m),
black rubber dock seal around the opening, a steel dock leveler plate on the floor,
yellow-black safety stripes on the edges, a traffic light (red/green) beside the door.
Show the door half open.
[공통 문구]
```

### 1-4. 관제 대시보드 UI (5단계 참고용)

```
Design a dark-themed real-time monitoring dashboard UI for a warehouse robot digital twin,
overlaid on the edge of a 3D warehouse view (the 3D view takes the center).
Left panel: a list of 8 robots with ID, type (AGV/AMR), status badge
(moving green, working orange, charging blue, idle grey, error red) and a battery bar.
Top bar: KPIs - orders per hour, average waiting time, robot utilization percentage.
Right panel: an alert feed and a small line chart of throughput over time.
Bottom: simulation speed control (1x, 2x, 5x, 10x) and a scenario selector (4 robots vs 8 robots).
Clean flat UI, readable, 16:9. Use placeholder text only, no brand names.
```

---

## 2부. 에셋별 모델링 가이드 (3ds Max 기준)

### 공통 규칙 (TA 어필 포인트)

1. **단위**: Customize > Units Setup에서 System Unit Scale을 **Centimeters**로 통일. 언리얼은 1 unit = 1 cm라서 크기가 그대로 맞음.
2. **피벗 위치**: 바닥에 놓이는 물체는 **바닥 중앙**에 피벗. 그래야 언리얼에서 바닥에 딱 붙여 배치 가능.
3. **네이밍**: `SM_랙이름` 형식 (예: `SM_Rack_Upright`, `SM_AGV_Body`, `SM_AGV_LiftPlate`). SM = Static Mesh.
4. **움직이는 부품은 따로 분리**: AGV 리프트 판, 바퀴, 도어 패널은 별도 오브젝트로. 언리얼에서 따로 움직이기 위함.
5. **로우폴리 우선**: 실시간 시뮬레이션이라 수십~수백 개가 동시에 보임. 볼트·구멍은 폴리곤이 아니라 **노멀맵/텍스처로** 표현.
6. **LOD**: 랙·박스처럼 많이 깔리는 것만 LOD1(폴리곤 50%)을 추가로 만듦.

### 에셋별 치수와 분해 방법

| 에셋 | 대략 치수 (cm) | 쪼개서 만들 부품 | 폴리곤 목표 |
|---|---|---|---|
| 팔레트 (KS T-11 규격) | 110 × 110 × 15 | 상판 널빤지, 받침 블록 (박스 몇 개로 충분) | 300 이하 |
| 박스 3~4종 | 40×30×30, 60×40×40 등 | 박스 하나 + 테이프 라인은 텍스처 | 12~50 |
| 랙 기둥(업라이트) | 폭 100~110, 높이 450~600 | 기둥 2개 + 사선 브레이싱 | 500 이하 |
| 랙 빔 | 길이 약 270 (팔레트 2개 폭) | 박스형 각재 1개 | 50 이하 |
| AGV (리프트형) | 120 × 80 × 30 | 본체 / 리프트 판 / 범퍼 / 센서창 / LED 라인 | 2,000~3,000 |
| AMR | 80 × 80 × 30 | 본체 / 원형 리프트 상판 / 센서 / LED 링 | 2,000 내외 |
| 이동식 선반 | 100 × 100 × 200 | 프레임 + 선반 4단 + 바구니 | 1,500 내외 |
| 롤러 컨베이어 | 길이 300, 폭 60, 높이 75 | 프레임 / 다리 / 롤러 1개(복제) | 모듈당 2,000 내외 |
| 충전 스테이션 | 60 × 30 × 40 | 본체 + 접촉판 | 500 이하 |
| 도크 도어 | 300 × 300 | 문틀 / 문 패널 / 고무 실링 / 레벨러 판 | 1,500 내외 |

### 모델링 순서 예시 — 랙 (가장 먼저 만들기 추천)

1. Box로 기둥 단면(약 10×7 cm)을 만들어 높이 500 cm로 늘림 → 좌우 2개 배치(간격 100 cm)
2. 얇은 Box로 사선 브레이싱을 지그재그로 연결 → 하나로 Attach → `SM_Rack_Upright`
3. 별도로 빔(270 cm) 하나 → `SM_Rack_Beam`
4. 언리얼에서 기둥 2개 + 빔 여러 개를 조립 → 블루프린트로 묶어 "랙 1칸" 프리팹 완성
   → 모듈식이라 층 수, 칸 수를 마음대로 바꿀 수 있다는 게 면접 어필 포인트

### FBX 내보내기 (개요)

- 오브젝트 선택 > File > Export > Export Selected > FBX
- Units: Centimeters, Up Axis: Z-up
- 애니메이션이 없는 정적 모델은 Animation 체크 해제
- 세부 메뉴는 사용하시는 3ds Max·언리얼 버전에 맞춰 따로 안내 예정

---

## 3부. CAD 평면도 그리는 법

### 도구
- AutoCAD가 있으면 AutoCAD. 없으면 무료 **LibreCAD / QCAD**
- 측량·도시계획 실기에서 쓴 도면 감각 그대로 사용하면 됨

### 레이어 구성 (색 구분)
| 레이어 | 내용 | 색 |
|---|---|---|
| 0_외벽 | 건물 외곽 60m × 40m | 흰/검 |
| 1_도크 | 입고 3개, 출고 2개 도어 위치 (폭 3m) | 청록 |
| 2_랙 | 랙 블록 (한 줄 깊이 약 1.1m, 등을 맞댄 2열이면 약 2.3m) | 회색 |
| 3_통로 | 통로 폭 약 3m 기준 | 노랑 |
| 4_AGV경로 | 랙 영역을 도는 사각 루프 (점선) | 초록 |
| 5_충전·대기 | 충전소 4개, AMR 대기 구역 | 주황/보라 |
| 6_치수 | 외곽·통로 치수선 | 빨강 |

### 작업 순서
1. 단위 **mm**, 원점(0,0)을 건물 왼쪽 아래 모서리에 둠
2. 외벽 → 도크 → 랙 블록 → 통로 → AGV 경로 → 충전소 순으로 그림 (위 배치도 참고)
3. 랙 블록은 하나 그린 뒤 배열 복사(ARRAY)
4. **PNG로 내보내기** (위에서 본 그대로, 외곽선에 딱 맞게 잘라서)
5. 언리얼에서 60m × 40m 크기의 Plane에 이 PNG를 머티리얼로 깔고 → 그 위에 3D 에셋 배치
   → "도면 기반으로 3D 디지털트윈 구축"이라는 스토리 완성

---

## 4부. 텍스처링 가이드 (가장 쉬운 방법부터)

### 레벨 1 — 단색 PBR (첫 MVP는 이것으로 충분)
- 텍스처 이미지 없이 언리얼 머티리얼에서 색·거칠기·금속성 값만 지정
- 예: 랙 기둥 = 파랑 + Metallic 0.8 + Roughness 0.4 / AGV = 노랑 + Metallic 0 + Roughness 0.5

### 레벨 2 — 무료 텍스처 활용 (추천)
- **Poly Haven**, **ambientCG**: 무료(CC0) PBR 텍스처 사이트
- 바닥 = 콘크리트/에폭시, 랙 = 도장 금속(painted metal), 팔레트 = 나무, 박스 = 골판지
- 다운로드할 때 BaseColor, Normal, Roughness(+AO, Metallic) 세트로 받기

### 레벨 3 — TA 어필용
- **마스터 머티리얼 1개**를 만들고 색·마모도·거칠기를 파라미터로 노출
- 이것으로 **머티리얼 인스턴스** 여러 개를 생성 (파랑 랙, 주황 빔, 노랑 AGV ...)
  → "하나의 셰이더로 20개 에셋 관리" = TA 역량의 대표 사례
- 경고 스티커·바닥 화살표는 **데칼(Decal)** 로 붙이기
- 컨베이어 벨트는 **UV 스크롤**(머티리얼의 Panner 노드)로 움직임 표현

### UV 펼치기 팁
- 랙·박스처럼 단순한 형태는 3ds Max의 **Unwrap UVW > Flatten Mapping** 한 번으로 충분
- 무료 텍스처(타일링 텍스처)를 쓸 때는 UV 크기를 실제 크기에 맞추는 게 중요 (Real-World Map Size)

---

## 5부. 추천 작업 순서 (MVP 기준)

1. CAD 평면도 → PNG
2. 팔레트, 박스, 랙 (가장 단순, 감 잡기용)
3. AGV 1대
4. 언리얼 임포트 + 단색 머티리얼로 배치
5. AGV 1대가 루프 경로를 돌다가 랙 앞에서 멈추고 리프트를 올리는 영상
→ 여기까지가 지원용 MVP. 이후 AMR, 컨베이어, 충전소, 대시보드 순으로 확장
