- higher EPSC -> lower respective noise
- Current sensing range -> results in noise reduction


- what makes a great reservoir?


문제점
UV 쏘면 Vth negative shift -> 달라지는 decay 특성 -> PRC 저하
	여러번 쏠때 Vth 하한 재서 그 아래로 Vth 설정
	 펄스 쏠 때 마다 Vg 변경
	
1. 데이터 디트렌딩 (Detrending)

$V_{th}$가 Negative로 밀리면 전체적인 전류 베이스라인이 우상향하는 'Trend'가 발생합니다. 이를 제거하는 것이 가장 기본입니다.

- **방법:** 각 노드의 출력값에서 **이동 평균(Moving Average)** 혹은 **선형 회귀(Linear Regression)**로 계산된 추세선을 빼줍니다.
    
- **효과:** 시간 흐름에 따른 평균값의 상승(Drift)을 제거하여, 리저버 상태가 일정한 범위 내에서 진동하는 것처럼 보이게 만듭니다.
    

### 2. 슬라이딩 윈도우 표준화 (Sliding Window Z-score Normalization)

전체 데이터를 한 번에 정규화하지 않고, 특정 시간 창(Window) 안에서만 정규화를 수행합니다.

- **방법:** 현재 시점 t의 노드 값 x(t)$를 최근 $N$개의 데이터를 기반으로 계산된 평균($\mu)과 표준편차(σ)로 정규화합니다.
    
    xnorm​(t)=σwindow​x(t)−μwindow​​
    
- **효과:** Vth​ shift로 인해 뒤쪽 데이터의 진폭이 커지거나 값이 치솟더라도, 항상 일정한 통계적 분포(평균 0, 분산 1)를 유지하게 되어 읽기 계층(Readout layer)의 부담을 덜어줍니다.
    

### 3. 상태 차분 피처 추가 (Differential Features, ΔX)

절대적인 전류값 대신 '변화량'에 집중하는 방식입니다.

- **방법:** 노드 상태 $X(t)$뿐만 아니라, 직전 상태와의 차이인 $\Delta X = X(t) - X(t-1)$를 학습 데이터 피처로 추가합니다.
    
- **효과:** $V_{th}$가 서서히 밀리는 것은 DC 성분에 가깝습니다. 차분을 취하면 이러한 저주파 Drift 성분은 사라지고, 입력에 반응하는 고주파 응답만 남게 되어 학습 안정성이 비약적으로 높아집니다.
    

### 4. 적응형 리드아웃 학습 (Adaptive / Online Learning)

학습 단계에서 뒤쪽 데이터의 가중치를 더 높게 두거나, 실시간으로 가중치를 업데이트합니다.

- **방법:** * **Online Learning:** 전체 데이터를 한 번에 학습(Batch)하지 않고, 시퀀스가 진행됨에 따라 **Recursive Least Squares (RLS)** 알고리즘 등을 사용하여 리드아웃 가중치(Wout​)를 미세하게 계속 업데이트합니다.
    
    - **Time-weighted Loss:** 뒤쪽 시퀀스 데이터에서 발생하는 오차에 더 높은 가중치(Penalty)를 부여하여 손실 함수(Loss function)를 계산합니다.
        
- **효과:** 소자의 특성이 변하는 속도에 맞춰 모델(Readout)이 함께 진화하므로 정확도 하락을 방지할 수 있습니다.
    

### 5. 가상 노드 확장 (Feature Augmentation with Time)

학습기에 "지금은 시퀀스의 뒷부분이다"라는 정보를 직접 주는 방식입니다.

- **방법:** 리저버 노드 데이터에 **현재 시간(Time index)**이나 **누적된 입력 에너지의 총합**을 나타내는 별도의 열(Column)을 추가하여 리드아웃 레이어에 입력합니다.
    
- **효과:** 리드아웃 레이어가 Vth​ shift와 시간 사이의 상관관계를 스스로 학습하여, 동일한 출력값이라도 시점 t에 따라 다르게 해석(Correction)할 수 있게 됩니다.