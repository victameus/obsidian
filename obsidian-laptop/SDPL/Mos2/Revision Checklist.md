# MoS₂ PRC 논문 리비전 — 신규 소자 측정 체크리스트

  


1번  우, 상 -27 
    좌, 하 이상
2번  우, 상  -17 (gate pad 좌측에 많이)
     좌, 하  -22~ -23
3번  우,하 -20V, 애매  (다이아몬드 무늬가 서북쪽)
     좌, 상 -50 (전류가 엄청 큼)
 4번 우, 상 -18 정도 그러나 애매  (좌측상단 speck)
     좌, 하  -24 정도

m445f3-green
4번 좌,하 - EPSC Blue, PPF Blue (new)   EPSC all(old)
3번 좌,상 - narma all, EPSC all(old) , output/transfer under light 
1번 우,상 - 가능성


- [ ]  EPSC 정규화 -> 파장별, device별 figure -오늘 
- [ ]  PPF 재측정 (4번, blue green more relaxation time) , 3번도
- [ ] NARMA retry (4번 먼저하는게 나을듯)
- [ ]  EPSC 삼중피팅
- [ ] heat analysis 3번, 4번 -일요일
-START_POINT_FOR_TRAIN = 5 # the step # from which the learning will begin

TRAIN_SET_SIZE = 100# Total number of points (train + test)

TEST_SET_SIZE = 30

CURRENT_NORMALIZATION_FACTOR = 1E7

**소자**: D1 (기존) + D2–D5 (신규 4개) = 총 5개

**목표**: R1-1(소자 재현성) · R1-3(멀티디바이스 PRC) · R2-3(재료 분석) 동시 충족

**비교 가능성 원칙**: 모든 측정은 기존과 동일 조건 — V_DS = 1 V, V_GS = −20 V, R/G/B irradiance·pulse 동일 캘리브레이션

  

> **측정 전략**: 전 소자(D1–D5)에 ① 기본 전기 ② 상온 PPC ③ 주요 시냅스 ④ PRC 노드를 돌려 재현성·멀티디바이스 데이터 확보. 시간이 큰 ⑤ 온도 의존 Arrhenius / XPS / endurance는 **대표 1–2개**만.

  

---

  

## 0. 사전 준비 · 일관성

- [x] 소자 로그 시트 작성 (D2–D5: flake 위치, 채널 L/W, 측정일, 비고)

- [ ] 채널 L/W 광학현미경 기록 → 기존 ~9 µm와 비교

- [ ] LED irradiance·pulse 캘리브레이션 재확인 (Thorlabs S120VC)

- [ ] 측정 조건 고정 확인 (V_DS = 1 V, V_GS = −20 V)

  

## 1. 구조·재료 분석 (R1-1, R2-3) — *전기측정 전에 먼저*

- [ ] **AFM 두께** (D2–D5 전부) → few-layer 정량화, 소자 간 두께 일관성

- [x] **Raman** (A₁g–E¹₂g 피크 간격) → 층수·결정성, 소자 간 일관성

- [ ] **PL 스펙트럼** → 결정 품질

- [ ] **XPS** (대표 1–2개) → Mo:S 화학량론, **S vacancy 증거** ← deep-trap 주장 직접 보강

  

## 2. 기본 전기 특성 (스크리닝)

- [x] Output (I_DS–V_DS), dark

- [x] Transfer (I_DS–V_GS), dark → 추출: **on/off, V_th, SS, μ, hysteresis**

- [ ] 불량/이상 소자 스크리닝 → 통계용 valid n 확정

  

## 3. 파장 의존 광응답 (R1-1)

- [ ] R/G/B output + dual-sweep transfer

- [ ] I_ph/I_dark 파장별 추출 → 기존 (1.9 / 3.6 / 5.2 ×10³)와 비교

  

## 4. PPC transient (R1-1 + R1-2 피팅용)

- [ ] R/G/B 상온 decay (light-off 후 충분히 길게)

- [ ] ⚠️ **블루는 60 s 이상** 측정 — τ_dt가 측정창에 갇히는 것 방지

- [ ] 각 decay → triexp 피팅 스크립트로 τ_r/τ_st/τ_dt·w_dt 추출

- [ ] *(대표 1–2개)* 온도 의존 30–90 °C → Arrhenius E_A 재현성 확인

  

## 5. 시냅스 특성 (R1-1)

- [ ] EPSC — irradiance 스윕 (R/G/B)

- [ ] EPSC — pulse-width 스윕 250–1000 ms (R/G/B)

- [ ] PPF — Δt 250–2000 ms 스윕 → t0 추출 (R/G/B)

- [ ] PD — 25 potentiation + 25 depression (R/G/B)

- [ ] *(대표 1–2개)* Endurance 70 cycle

  

## 6. PRC 노드 측정 (R1-3) — *가장 중요*

- [ ] **동일 입력 시퀀스 u_k 고정** (시드 기록) → 소자 간 비교 가능

- [ ] 각 소자에서 R/G/B 노드 데이터 취득 (≤10 노드/파장, 동일 샘플링 시점)

- [ ] 최소 2개 소자 확보 → **n = 2 멀티디바이스 reservoir** 구성 가능

- [ ] 가능하면 3–4개 → n = 2, 3, 4 조합으로 richness 스케일링 입증

  

## 7. 분석 · 통계

- [ ] **소자 간 파라미터 분포표 (mean ± std)**: SS, V_th, μ, I_ph/I_dark, τ_dt, w_dt, EPSC index, PPF t0

- [ ] PRC: **여러 train/test split** → NMSE mean ± std ("single best case" 지적 대응)

- [ ] PRC: **regression 종류 비교** (ridge α 스윕 / linear / lasso 등)

- [ ] PRC: **n = 1 vs n = 2 (vs n = 3, 4)** 조합 NMSE → 노드 수 대비 성능 곡선

- [ ] **SOTA 비교표** 작성 (재료 / 메커니즘 / 태스크 / NMSE·accuracy / 노드 수 / multiplexing)

  

## 8. 원고 반영

- [ ] SI: 재료 분석 그림 (Raman / PL / AFM / XPS)

- [ ] SI: 소자 재현성 표·그림 (5 소자 분포)

- [ ] 본문/SI: triexp 피팅 그림 + 파라미터 표 (+ AIC/BIC 모델 선택)

- [ ] 본문: PRC 통계 반영 (Fig 6 업데이트) + SOTA 표 (Table 1)

- [ ] **Response letter** point-by-point 작성 (어디를 어떻게 고쳤는지 페이지·그림 번호까지)

  

---

  
# 리비전 남은 측정 리스트 — 마감 7/25 (D-12)

  

**완료**: ✅ Raman (Δ≈24.6, few-layer) · ✅ AFM (채널 ~5.0–5.1 nm ≈ 7–8층)

→ 리뷰어의 "thickness + Raman" 요구(R1-1, R2-3 일부)는 사실상 충족.

  

**전략**: 12일 안에 5소자 풀배터리는 불가능. 아래처럼 **TIER 1만 반드시**, TIER 2는 시간 되면, TIER 3은 본문 논증으로 대체. 그리고 절반은 *새 측정이 아니라 기존 데이터 분석/집필*임 — 측정 부담은 생각보다 작음.

  

---

  

## ⏰ 가장 먼저 (lead-time가 마감을 좌우)

- [ ] **XPS 빔타임 즉시 예약** — 외부 장비라 이게 병목. Mo:S 화학량론 + **S vacancy 증거**(deep-trap 주장 직접 뒷받침, R2-3). 1–2개 대표 소자.

  - ⚠️ 25일까지 빔타임 확보 불가 시 대체안: ① PL의 결함 관련 발광으로 부분 근거, ② 박리 MoS₂ S-vacancy XPS 문헌 인용으로 논증. 응답서에 사유 명시.

  

## 🔴 TIER 1 — 반드시 (R1·R2 최소 대응)

- [x] **PL 스펙트럼** (R1-1) — Raman과 같은 장비 세션이면 반나절. "Raman/PL" 요구 마무리.

- [ ] **다소자 transfer/output (dark)** — D1 + 신규 2개 = **최소 3소자** (R1-1 재현성). 추출: SS, V_th, μ, on/off. → mean±std 표.

- [ ] **다소자 상온 PPC transient (R/G/B)** — 위 3소자 (R1-1 변동성 + R1-2 피팅 입력).

  - ⚠️ **블루는 60초 이상** 측정 (τ_dt 측정창 갇힘 방지 — 지난 피팅에서 확인됨).

- [ ] **PRC 노드 데이터 — 최소 2소자** (R1-3의 n=2 요구, R1에서 가장 중요). 동일 입력 시퀀스 u_k 고정.

  

## 🟡 TIER 2 — 여유 되면 (설득력 강화)

- [ ] 다소자 시냅스 특성 (EPSC / PPF / PD) — R1-1 변동성 풍부화

- [ ] PRC n=3(+) 조합용 3번째 소자 노드 데이터 — richness 스케일링 곡선

- [ ] SNR 낮은 Raman 지점 재측정(적분시간↑) — 이미 대표 스펙트럼 있으면 skip 가능

  

## ⚪ TIER 3 — 마감 내 생략, 본문 논증으로 대체

- [ ] ~~신규 소자 온도 의존 Arrhenius~~ → D1 기존 온도 데이터로 메커니즘 논증

- [ ] ~~신규 소자 endurance 70 cycle~~ → 기존 소자 결과로 충분

  

---

  

## 📊 측정 아님 — 기존 데이터 분석/집필 (병렬 진행, 측정과 무관)

- [ ] **Eq.4 삼중지수 피팅** (R1-2) — 스크립트 완성됨, 상온 R/G/B decay 넣고 실행 → τ·w_dt 표 + AIC/BIC

- [ ] **PRC 통계** (R1-3) — 여러 train/test split → NMSE mean±std, regression 종류 비교 (재분석, 새 측정 X)

- [ ] **PRC SOTA 비교표** (R1-3) — 문헌 6–10편 정리

- [ ] **Novelty 문단**(R2-1) · **Abstract 개정**(R2-2) · **인용 추가**(R2-4, 관련 것만 선별)

- [ ] **Response letter** point-by-point

  

---

  

### 요약: 실제 새 측정은 5종뿐

1. XPS(예약 먼저) 2. PL 3. 다소자 transfer 4. 다소자 상온 PPC 5. 2소자 PRC 노드

→ 소자는 이미 만들어 뒀으니(D2–D5), 3·4·5는 며칠 집중 측정이면 가능. 1·2는 외부 장비 일정이 관건.

  

### 추천 순서

D-12~10: XPS 예약 + PL + 다소자 transfer/PPC 시작 (동시에 삼중지수 피팅 실행)

D-9~6: 2소자 PRC 노드 측정 → PRC 통계·비교표 분석

D-5~1: 그림·SI 완성 → novelty/abstract/인용 → response letter