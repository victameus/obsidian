Main idea: low wavelength -> higher energy -> electron trapped in defect with higher energy -> takes long to decay.

Effect 
1. EPSC -> faster decay time
2. PPF -> faster decay




I-V Characteristics
[ ] Dark            [ ] Green
[ ] Red             [ ] Blue
EPSC  25min wait : 5V500ms, 8V500ms, 10V500ms, 8V250ms, 8V750ms, 8V1000ms
-> 한 Vg에 150분 : 2시간 30분 소요
Red -    [o] 17 [ ] 20 [ ] 23 
Green - [ ] 17 [ ] 20 [ ] 23 
Blue   - [ ] 17 [ ] 20 [ ] 23 
계획 
2/19 - Red 17
2/20 - Red, Green 
2/21 - Blue, output/ tranfer
2/22 - PD 맛보기

- Code 조건
-START_POINT_FOR_TRAIN = 5 

TRAIN_SET_SIZE = 100# 

TEST_SET_SIZE = 30#

CURRENT_NORMALIZATION_FACTOR = 1E7

Normalize 
일단 max값으로 나누고 복붙하고 옮겨서 끝에 하나 추가 후 0,1로 normalize

Red.csv: τₑff = 0.04 s, β = 0.325
Green.csv: τₑff = 0.25 s, β = 0.263
Blue.csv: τₑff = 0.40 s, β = 0.263