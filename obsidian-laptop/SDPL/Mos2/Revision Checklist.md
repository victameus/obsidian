# MoS₂ PRC 논문 리비전 — 신규 소자 측정 체크리스트

  


1번  우, 상 -27 
    좌, 하 이상
2번 








**소자**: D1 (기존) + D2–D5 (신규 4개) = 총 5개

**목표**: R1-1(소자 재현성) · R1-3(멀티디바이스 PRC) · R2-3(재료 분석) 동시 충족

**비교 가능성 원칙**: 모든 측정은 기존과 동일 조건 — V_DS = 1 V, V_GS = −20 V, R/G/B irradiance·pulse 동일 캘리브레이션

  

> **측정 전략**: 전 소자(D1–D5)에 ① 기본 전기 ② 상온 PPC ③ 주요 시냅스 ④ PRC 노드를 돌려 재현성·멀티디바이스 데이터 확보. 시간이 큰 ⑤ 온도 의존 Arrhenius / XPS / endurance는 **대표 1–2개**만.

  

---

  

## 0. 사전 준비 · 일관성

- [ ] 소자 로그 시트 작성 (D2–D5: flake 위치, 채널 L/W, 측정일, 비고)

- [ ] 채널 L/W 광학현미경 기록 → 기존 ~9 µm와 비교

- [ ] LED irradiance·pulse 캘리브레이션 재확인 (Thorlabs S120VC)

- [ ] 측정 조건 고정 확인 (V_DS = 1 V, V_GS = −20 V)

  

## 1. 구조·재료 분석 (R1-1, R2-3) — *전기측정 전에 먼저*

- [ ] **AFM 두께** (D2–D5 전부) → few-layer 정량화, 소자 간 두께 일관성

- [x] **Raman** (A₁g–E¹₂g 피크 간격) → 층수·결정성, 소자 간 일관성

- [ ] **PL 스펙트럼** → 결정 품질

- [ ] **XPS** (대표 1–2개) → Mo:S 화학량론, **S vacancy 증거** ← deep-trap 주장 직접 보강

  

## 2. 기본 전기 특성 (스크리닝)

- [ ] Output (I_DS–V_DS), dark

- [ ] Transfer (I_DS–V_GS), dark → 추출: **on/off, V_th, SS, μ, hysteresis**

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

  
