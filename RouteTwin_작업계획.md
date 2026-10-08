# RouteTwin 작업 계획

> 물류센터 AGV·AMR 운영 시뮬레이션 디지털트윈 · Unreal Engine 5.7 · 3ds Max 2026
> 기준: 하루 3~4시간 작업 / 언리얼 첫 프로젝트 기준의 현실적인 기간
> 작성일: 2026-10-08

## 전체 일정 요약

| 단계 | 내용 | 기간 | 누적 |
|---|---|---|---|
| 0 | 프로젝트·Git 환경 준비 | 2~3일 | 0.5주 |
| 1 | 기획 + CAD 평면도 | 3~4일 | 1주 |
| 2 | 1차 모델링 (팔레트·박스·랙·AGV) | 1.5주 | 2.5주 |
| 3 | 언리얼 임포트 + 씬 구성 | 1주 | 3.5주 |
| 4 | AGV 주행 로직 → **MVP 영상** | 1주 | 4.5주 |
| ★ | **MVP로 비전스페이스 등 지원** | | |
| 5 | 2차 모델링 (AMR·선반·컨베이어·충전소·도크) | 1.5주 | 6주 |
| 6 | AMR·충돌 회피·배터리·주문 데이터 | 1.5주 | 7.5주 |
| 7 | 시나리오 비교 + KPI 대시보드 | 1주 | 8.5주 |
| 8 | 패키징·영상·노션 정리 | 1주 | 9.5주 |
| (선택) | Three.js 웹 데모 + 포트폴리오 사이트 도메인 이전 | 1~1.5주 | 약 11주 |

- 하루 6~8시간(전업)으로 하면 약 5~6주
- 가장 오래 걸릴 가능성이 큰 곳: 4단계(블루프린트 첫 경험), 6단계(AMR 회피 로직)

---

## 0단계. 프로젝트·Git 환경 준비 (2~3일)

**목표**: 나중에 꼬이지 않도록 폴더·이름·버전관리 규칙을 먼저 정한다.

1. Unreal Engine 5.7 프로젝트 생성
   - Epic Games Launcher → Unreal Engine → Library → 5.7 Launch
   - Project Browser에서 Games > Blank, Blueprint, Target Platform: Desktop, Quality: Maximum, Starter Content 해제
   - 프로젝트 이름: `RouteTwin`
2. 콘텐츠 폴더 구조
   ```
   Content/RouteTwin/
     Maps/        L_Warehouse
     Meshes/      Rack, Pallet, Box, AGV, AMR, Conveyor, Charger, Dock
     Materials/   M_Master, MI_*
     Textures/
     Blueprints/  BP_AGV, BP_AMR, BP_RackBay, BP_SimManager
     UI/          WBP_Dashboard
     Data/        DT_Orders, S_Order
   ```
3. 네이밍 규칙: `SM_`(메시) `M_`(머티리얼) `MI_`(머티리얼 인스턴스) `T_`(텍스처) `BP_`(블루프린트) `WBP_`(UI) `DT_`(데이터테이블)
4. Git (공고 자격요건 "Git 협업 경험" 대비)
   - GitHub에 `RouteTwin` 레포 생성, 언리얼용 `.gitignore` 적용 (Binaries, Intermediate, Saved, DerivedDataCache 제외)
   - Git LFS 설치 후 `*.uasset`, `*.umap`, `*.fbx`, `*.png` 추적
   - 작업 단위마다 커밋 (예: "Add SM_Rack_Upright and SM_Rack_Beam")
   - 커밋 이력 자체가 포트폴리오 증거가 됨

## 1단계. 기획 + CAD 평면도 (3~4일)

**목표**: 무엇을 측정할지, 어떤 공간인지 확정한다.

1. KPI 확정: 시간당 처리 주문 수, 평균 대기시간, 로봇 가동률, 충전 대기 횟수
2. 시나리오 확정: AGV 4대 vs 8대 (같은 주문 목록)
3. CAD 평면도 (AutoCAD 또는 무료 LibreCAD/QCAD)
   - 60m × 40m, 단위 mm, 원점은 왼쪽 아래 모서리
   - 레이어: 외벽 / 도크 / 랙 / 통로 / AGV 경로 / 충전·대기 / 치수
   - 랙 블록 1개를 그리고 배열 복사, 통로 폭 3m 기준
4. PNG로 내보내기 (외곽선에 맞춰 자르기)

**산출물**: 평면도 PNG, KPI·시나리오 메모

## 2단계. 1차 모델링 (1.5주)

**목표**: MVP에 필요한 최소 에셋만 만든다.

공통 설정 (3ds Max 2026)
- Customize → Units Setup → Display Unit: Metric/Centimeters, System Unit Setup: 1 Unit = 1 Centimeter
- 피벗: 바닥 중앙 (Hierarchy 패널 → Affect Pivot Only)
- 움직이는 부품은 별도 오브젝트

| 순서 | 에셋 | 치수(cm) | 포인트 |
|---|---|---|---|
| 1 | SM_Pallet | 110×110×15 | 박스 몇 개 조합, 300폴리 이하 |
| 2 | SM_Box_A~C | 40×30×30 등 | 테이프는 텍스처로 |
| 3 | SM_Rack_Upright | 폭 110, 높이 500 | 기둥 2 + X자 브레이싱 |
| 4 | SM_Rack_Beam | 길이 270 | 각재 1개 |
| 5 | SM_AGV_Body / SM_AGV_LiftPlate | 120×80×30 | 리프트 판 분리, 뒷면 충전 접점 2개 |

- UV: Unwrap UVW → Flatten Mapping
- LOD1: 랙·박스만 (ProOptimizer로 50%)
- FBX 내보내기: File → Export → Export Selected, Units Centimeters, Up Axis Z-up, 정적 메시는 Animation 해제

**산출물**: FBX 5~6종

## 3단계. 언리얼 임포트 + 씬 구성 (1주)

**목표**: 물류센터가 "보이는" 상태까지.

1. FBX 임포트 (Content Browser → Import), 머티리얼은 새로 만들 것이므로 임포트 머티리얼 생성 해제 가능
2. 바닥: 60m×40m Plane(Scale 60×40)에 평면도 PNG 머티리얼 → 그 위에 에셋 배치
3. 마스터 머티리얼 `M_Master`
   - 파라미터: BaseColor, Roughness, Metallic, (선택) Wear 마스크
   - 인스턴스: MI_RackBlue, MI_BeamOrange, MI_AGVYellow, MI_Wood, MI_Cardboard
4. 랙 모듈화: `BP_RackBay` (기둥 2 + 빔 N단, 층 수를 변수로 노출)
5. 대량 배치: 박스·팔레트는 Instanced Static Mesh로 성능 확보 → 배치 전후 FPS 기록 (stat fps)
6. 조명: 천장 조명 + Lumen, 바닥 반사

**산출물**: 정적 물류센터 씬, 성능 수치 메모

## 4단계. AGV 주행 로직 → MVP (1주)

**목표**: "AGV 1대가 경로를 돌다 랙 앞에서 멈추고 리프트를 올리는" 30초 영상.

1. `BP_AGV` (Actor): 본체·리프트 메시 컴포넌트
2. 경로: `BP_AGVPath` (Spline 컴포넌트) → 평면도의 초록 루프를 따라 점 배치
3. 이동: Event Tick에서 거리 증가 → Get Location/Rotation at Distance Along Spline
4. 정차 지점: 스플라인 위 거리값 배열로 지정 → 도착 시 정지
5. 리프트: Timeline으로 리프트 판 Z 0→15cm, 팔레트 Attach
6. 상태 표시: LED 머티리얼 색 변경 (이동=초록, 작업=주황)
7. 녹화: Movie Render Queue 또는 OBS

**★ MVP 체크포인트**: 영상 + 스크린샷을 노션에 올리고 비전스페이스 3D 모델러/TA에 지원

## 5단계. 2차 모델링 (1.5주)

| 에셋 | 치수(cm) | 포인트 |
|---|---|---|
| SM_AMR (본체·원형 리프트) | 80×80×30 | LED 링 분리 |
| SM_Shelf (이동식 선반) | 100×100×200 | 바구니는 복제 |
| SM_Conveyor_Straight / _Curve | 300×60×75 | 롤러 1개를 복제, 벨트는 UV 스크롤 |
| SM_Charger | 60×30×40 | 구리 접점 |
| SM_Dock (문틀·도어·레벨러) | 300×300 | 도어 패널 분리 (개폐) |
| (선택) SM_PickStation | 작업대·모니터 | 로우폴리 |

## 6단계. AMR·충돌 회피·배터리·주문 데이터 (1.5주)

1. AMR: Character 또는 Pawn + AI Controller, Nav Mesh Bounds Volume 배치 → AI Move To
2. 선반 운반: 선반 밑 도착 → 리프트 → Attach → 피킹 스테이션 이동
3. 충돌 회피 (AGV·AMR 공통): 전방 Box Collision에 다른 로봇이 들어오면 감속·정지
4. 배터리: 이동 거리만큼 감소, 20% 이하면 충전소로 복귀, 충전 중 파란 LED
5. 주문 데이터: Structure `S_Order`(주문ID, 랙ID, 목적지) → CSV 작성 → DataTable `DT_Orders`로 임포트
6. 관리자 `BP_SimManager`: 주문을 대기 중 로봇에 배정

## 7단계. 시나리오 비교 + KPI 대시보드 (1주)

1. 시뮬레이션 배속: Set Global Time Dilation (1/2/5/10배)
2. KPI 집계: SimManager가 처리 주문 수·대기시간·가동률 기록
3. `WBP_Dashboard` (UMG): 로봇 목록·상태·배터리, KPI 3개, 알림, 배속·시나리오 버튼
4. 로봇 클릭 시 카메라 추적
5. 4대 vs 8대 같은 주문으로 각각 실행 → 결과를 표로 정리 → **결론 한 줄** 작성

## 8단계. 패키징·영상·노션 (1주)

1. 패키징: Platforms → Windows → Package Project (Shipping), 실행 테스트
2. GitHub Releases에 zip 업로드
3. 2~3분 영상: 문제 정의 → 평면도 → 모델링 → 시뮬레이션 → 시나리오 결과
4. 노션: 기술적 의사결정, 문제·해결, 성능 수치, 결론
5. 포트폴리오 사이트 RouteTwin 카드 갱신

## (선택) 웹 데모 + 도메인 이전 (1~1.5주)

- 3ds Max → glTF 내보내기, Three.js로 물류센터 + AGV 1대 주행 + KPI 패널
- 포트폴리오 사이트를 개인 도메인(GitHub Pages)으로 이전하고 세부 수정
