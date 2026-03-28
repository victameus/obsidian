Main idea: low wavelength -> higher energy -> electron trapped in defect with higher energy -> takes long to decay.

- 참고논문
	Deep level transient spectroscopy and theoretical modelling of defect states
	in few-layer MoS2 - defect 위치 분석
	

- decay mechanism
-


- Code 조건
-START_POINT_FOR_TRAIN = 5 

TRAIN_SET_SIZE = 100# 

TEST_SET_SIZE = 30#

CURRENT_NORMALIZATION_FACTOR = 1E7

Normalize 
일단 max값으로 나누고 복붙하고 옮겨서 끝에 하나 추가 후 0,1로 normaliz

Red.csv: τₑff = 0.04 s, β = 0.325
Green.csv: τₑff = 0.25 s, β = 0.263
Blue.csv: τₑff = 0.40 s, β = 0.263