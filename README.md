# 장애인 콜택시 대기시간 예측 
---
## 프로젝트 개요
- 주제 : 장애인 이동권 개선을 위한 장애인 콜택시 대기시간 예측
- 학습 과목 : 데이터전처리, 데이터분석, 머신러닝
- 데이터 출처 : <https://www.data.go.kr/data/15057705/openapi.do>
- 중점 사항 : 시계열 데이터 전처리, 단변량/이변량 분석, ML 모델링
- 프로젝트 목표 : 장애인 콜택시를 이용하는 고객들의 불편사항을 개선하고 서비스의 품질을 높이기 위해 콜택시 운행이 종료된 시점에 다음 날의 기상 예보를 바탕으로 기상 상황에 따른 장애인 콜택시의 평균 대기 시간을 예측하고 예상 대기시간을 제공할 수 있는 모델 완성
---

## 도메인 이해 
![image](https://github.com/user-attachments/assets/e98b8d49-f9b8-465f-b54a-70cced5fc309)
![image](https://github.com/user-attachments/assets/8b47a077-f8c3-441b-8d7c-6b5eb539cc30)
![image](https://github.com/user-attachments/assets/5b28fa39-52a6-4997-98ab-9653c477368f)
![image](https://github.com/user-attachments/assets/ce59a873-a7b3-4e52-8300-011d52d88683)
![image](https://github.com/user-attachments/assets/df600ce8-f2f1-4544-a668-e1e70d906a99)
![image](https://github.com/user-attachments/assets/450669e2-3a86-4a8d-92e3-8e156b8c3980)
---

## 데이터 소개 

![image](https://github.com/user-attachments/assets/7677a115-687a-446d-bfc5-1fb65c6c9236)

- 장애인 콜택시 운행정보
![image](https://github.com/user-attachments/assets/ef7247f2-b312-4568-a5b6-3b5c850d2f50)


- 날씨 정보
![image](https://github.com/user-attachments/assets/8e8bc27a-7185-4a1c-a433-8bfca16e1602)
---

## 핵심 사항 

![image](https://github.com/user-attachments/assets/e599475a-551a-43d4-8bbd-3b583e66d15f)

---
## 데이터 분석 및 전처리 
- 익일의 대기시간(waiting_time)을 오늘의 데이터를 활용해 예측해야 하는 대상(target)으로 설정

![image](https://github.com/user-attachments/assets/52a3736f-3010-472e-9eab-17dbf8932712)

- target과 다른 변수들간의 상관 관계 확인
![image](https://github.com/user-attachments/assets/7f10655a-01d5-4783-b22d-f927d95b2af6)

- target 과 ride_ratio간의 산점도

  ![image](https://github.com/user-attachments/assets/a5d7a8d9-268a-4dae-8302-59173f11e0a8)

- 운임요금과 요일별 target 산점도

  ![image](https://github.com/user-attachments/assets/f8aa2550-5e54-417b-9a45-45ba2e8b4b7b)

## 범주형 변수들과 target 간의 관계 
- 휴일 여부

  ![image](https://github.com/user-attachments/assets/6711b43b-681b-4971-b38c-fa8fd60ae64f)
  ### Ttest_indResult(statistic=5.078160958101015, pvalue=4.0505005123594406e-07)
---
- 주말 여부

  ![image](https://github.com/user-attachments/assets/003d00db-c02f-4ced-b0de-bc5f094ed601)
  ### Ttest_indResult(statistic=7.917032903397524, pvalue=3.42488756704735e-15)
---
- 코로나 여부

  ![image](https://github.com/user-attachments/assets/cbb89c1d-382b-4324-8785-8d665d1f7098)
  ### Ttest_indResult(statistic=31.718933261457867, pvalue=5.512162412795066e-190)
---
- 요일 여부

  ![image](https://github.com/user-attachments/assets/8ced0774-2a49-4ec0-b046-79cf89ec6535)
  ### F_onewayResult(statistic=15.668738200566109, pvalue=8.766163819661817e-18)
---
- 계절 여부

  ![image](https://github.com/user-attachments/assets/7c3d921f-b0a2-402b-8932-1541a064841e)
  ### F_onewayResult(statistic=34.73562423868687, pvalue=4.719868115759872e-22)
---

## 변수 정리 
### 강한 관계의 변수
- 'count_taxi': 운행 된 차량 수 , 'receipt': 접수 건수, 'boarding': 이용 건수, 'avg_rate': 평균요금, 'avg_ride_distance': 평균 거리, 'ride_ratio': 
- 'covid_19': 코로나 발생 시점 ~ 집합 금지, 집합 금지 ~ 사회적 거리두기, 그 이후로 3분류
- 'holiday', 'day7_avg_wait_time', 'ride_ratio', 
- 'weekend_holiday', 'weekday_Saturday', 'weekday_Sunday', 'rainyday'
### 약한 관계의 변수
- 'temp_max', 'temp_min' -> 'temp_avg'로 치환
- 'humidity_max(%)', 'humidity_min(%)' -> 'humidity_avg'로 치환
- 'month'
- 'weekend', 'weekday_Monday', 'weekday_Tuesday', 'weekday_Wednesday', 'weekday_Thursday', 'weekday_Friday' -> 평일의 요일 간 차이는 없다고 보고 평일/토요일/일요일로 나눔  

### 관계가 없는 변수 
- 'season_Spring', 'season_Summer', 'season_Fall', 'season_Winter' -> 날씨 정보와 상관없이 '달'로 구분했기 때문에 관계가 적다고 생각
- 'sunshine(MJ/m2)'

---
## 파생변수 추가
### Rainy dat 변수 생성
- rain(mm)이 2이상이면 1 아니면 0으로 rainyday 파생변수 생성
### 코로나 파생 변수 생성
- 코로나 발생 시점 ~ 집합 금지 기간 (2020-01-20~2020-04-19) -> 2
- 집합 금지 해제 ~ 사회적 거리두기 (2020-04-20 ~ 2022-04-18) -> 1
- 코로나 기간이 아닌 기간 -> 0

  ![image](https://github.com/user-attachments/assets/38132ecf-3cfb-4068-b24e-3c62c01096c0)
  ![image](https://github.com/user-attachments/assets/6f2cbbec-a9fc-44a5-ad7f-541869b43d5f)

---
## 모델링
- 스케일링 작업시 ['boarding', 'receipt'] 에는 MinMaxScaler진행 / ['avg_ride_distance', 'avg_rate', 'count_taxi'] 에는 Robust_Scailing(이상치가 있는 feature에서 좋은 결과를 보여줌)진행
1) Linear Regression
![image](https://github.com/user-attachments/assets/11bf271b-0963-412a-84e5-2202f2e81653)
![image](https://github.com/user-attachments/assets/5d4825d4-0692-4ce5-be52-29abe7e1f7c5)


2) Lasso Regression
![image](https://github.com/user-attachments/assets/09ff1240-54a8-4925-84a0-b06245124c0e)
![image](https://github.com/user-attachments/assets/52379464-156f-46e1-b494-36749431c45a)


3) Ridge Regression
![image](https://github.com/user-attachments/assets/7cd18837-5a1b-4dac-84a2-052eee53c100)
![image](https://github.com/user-attachments/assets/2fa3186f-50d8-43a9-a65b-d464c721fa34)


4) ElasticNet
![image](https://github.com/user-attachments/assets/3874277f-ec49-4313-a0fe-ffe6eaa9c21b)
![image](https://github.com/user-attachments/assets/7601b9ce-5f73-4c86-8651-a5376aa279f2)


5) KNN
![image](https://github.com/user-attachments/assets/9072f62f-d8cf-4afc-a9b2-cd5243cf3926)
![image](https://github.com/user-attachments/assets/9263b0c9-d66a-4453-bf30-f6ebcaa84505)


6) Decision Tree
![image](https://github.com/user-attachments/assets/bb526bea-ee42-4ac5-88d4-4d1d6202cf07)
![image](https://github.com/user-attachments/assets/2f755f6a-469c-4c08-8a9e-40afbab060db)


7) Random Forest
![image](https://github.com/user-attachments/assets/2f1420ed-ebda-4407-8a34-876671105041)
![image](https://github.com/user-attachments/assets/26316b26-40da-49eb-bc1c-1539d32f6fab)


8) LightGBM
![image](https://github.com/user-attachments/assets/30c1b8e5-2137-4b4b-8244-6f6462937bc4)
![image](https://github.com/user-attachments/assets/decde94b-6d7b-4001-a6ad-f793e5d5b0eb)


9) XGBoost
![image](https://github.com/user-attachments/assets/60f5c6a8-b5ee-4cb5-9559-270627d4b7e8)
![image](https://github.com/user-attachments/assets/f81d250c-7509-4294-bc52-264f2b9b3b11)


10) Gradient Boosting
![image](https://github.com/user-attachments/assets/7ca32345-5873-433d-9364-383c4245af5b)
![image](https://github.com/user-attachments/assets/786c2d1f-83bd-4238-9a46-6aa07d26a3c3)



### 최종 모델
- **총 10개의 모델을 돌려본 결과 Ridge 모델의 성능이 가장 좋았다.**

  ![image](https://github.com/user-attachments/assets/48813c6b-9312-4c22-82f5-bcefe4297588)



