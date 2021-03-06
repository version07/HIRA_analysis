본 프로젝트는 보건의료 데이터를 다루기 때문에 개인정보 보호 조치로 일부 자료만 공개합니다.

# 건강보험심사평가원의 환자 표본자료 소개
2013년 기준 전 국민의 98%가 국민건강보험에 가입되어 있으므로, 건강보험 청구자료는 국내 의료서비스 이용현황을 보여주는 대표적인 자료(김록영 외, 2013).

건강보험 청구자료란 의료기관에서 환자의 진료비용 중 국민건강모험이 부담하는 부분에 대해 지급의뢰를 하기 위해 심사평가원에 청구하는 자료.
1년간 의료서비스를 이용한 모든 환자를 대상으로 성별, 연령 구간에 따른 환자 단위 층화계통 추출. 표본 추출환자의 1년간의 모든 진료내역과 처방내역을 포함하고 있음.
주민등록번호를 대체하는 키를 무작위로 할당하여 개인정보를 보호하나 동일인 구분 가능.
의료보험이나 의료급여를 받은 의료서비스 항목만을 포함하고 있음. 비급여항목에 대한 부재.

정신질환의 경우 기록이 남는 것에 대한 두려움 때문에 비급여 진료를 받는 사람의 수가 상당하다는 점이 단점. 게다가 1년간의 표본 자료이므로 대개 유병기간이 긴 정신질환의 양상변화와 치료에 대한 조사를 하기에는 제한점이 있음.

소아청소년환자 표본자료: 
- 2009-2016년 소아청소년환자 10% 추출 (약 110만명)-성인표본집단과 달리 1세 단위로 연령표기, 정신질환에 해당하는 F 상병코드를 4단 코드로 범주화 
- 2017-2018년 소아청소년환자 10% 추출(약 100만명)-5세 단위 연령구분과 0-2세, 3-5세, 6-9세, 10-12세, 13-19세 구분, 정신질환에 해당하는 F 상병코드를 3단 코드로 범주화, 이전 자료와의 호환성 떨어짐 

# EDA 과정

## 년도별 데이터의 호환성 문제 극복
F 상병코드를 2단 코드로 범주화 (단, 연령별 EDA는 2017-2018 연령구분 기준이 모호해 통합하지 않기로 결정)

## 중복자료 제거
- 수진자레벨: 동일한 수진자가 여러 질병, 동일한 질병으로 여러 번 방문

2009년 전국 남아 인구 비율            |  2009 연령군별 남아 진료건수         |  2009년 연령군별 남아 수진자 비율
:-------------------------:|:-------------------------:|:-------------------------:
<img width="449" alt="2009년 전국 남아 인구 비율" src="https://user-images.githubusercontent.com/68261338/104873924-ff687600-5994-11eb-8227-660d81506c03.png">  |  <img width="449" alt="2009 연령군별 남아 진료건수" src="https://user-images.githubusercontent.com/68261338/104873920-fc6d8580-5994-11eb-8e16-c7c1640c9768.png"> | <img width="449" alt="2009년 연령군별 남아 수진자 비율" src="https://user-images.githubusercontent.com/68261338/104873923-fe374900-5994-11eb-8de3-4063889c22ca.png">

- 명세서레벨: 보완청구/분리청구의 경우에도 명세서 조인키는 다름, 분리청구의 숫자가 700여건에 불과함

## 연도레벨

연도별 상병 Top13

![스크린샷 2021-01-18 오후 3 28 54](https://user-images.githubusercontent.com/68368668/104880003-385b1780-59a2-11eb-836f-844e8033d1cb.png)

### F code를 가진 수진자의 수(모집단 추정 수) 및 비율  - 성별, 연령별, 시/도별

환자표본자료 소개서에는 다음과 같이 기재되어 있음:
> *"(입원환자표본자료에만 존재) 0: 외래환자 1% 추출, 1: 입원환자 10% 추출 ※ 1% 외래환자는 해당연도내에 입원내역이 없는 외래환자를 추출"*

임의로 10% 추출된 소아청소년환자들의 추출 방식을 역추적하기 위해서 2009년 데이터로 샘플링 가중치, 추출확률과 나머지 항목들간의 상관관계를 분석한 결과:
- 샘플링 가중치와 추출확률간의 상관관계는 1임을 확인
- 연령대와 성별이 가장 높은 상관관계를 보임

![스크린샷 2021-01-18 오후 1 45 23](https://user-images.githubusercontent.com/68261338/104874191-a64d1200-5995-11eb-96b5-acd18b4384c3.png)

총 7개의 유니크한 샘플링 가중치가 존재하며, 성별과 연령대별로 데이터를 범주화해본 결과, 0~5세를 제외한 나머지 범주마자 개별적 샘플링 가중치가 부여된 것으로 추정된다

![스크린샷 2021-01-18 오후 1 46 00](https://user-images.githubusercontent.com/68261338/104873951-1313dc80-5995-11eb-8120-956ef6150b0b.png)

1세 단위로 확인해본 결과, 5세 구간마다 샘플링 가중치가 2가지가 부여된다. 이는, 데이터가 1년간 모든 진료내역을 포함하고 있기 때문에, 생일자 기준으로 샘플링 가중치가 새로 부여될 수 있을 것으로 추정된다

![스크린샷 2021-01-18 오후 1 46 33](https://user-images.githubusercontent.com/68261338/104873953-13ac7300-5995-11eb-9a10-88d374f5a87c.png)

고로, 모집단 추정 수 = 표본환자수 * 가중치(연령별, 성별로 다름) 라는 결론에 도달함

#### 2개 이상의 F code를 가진 수진자의 수(주상병+부상병)

### 전체 청소년 인구 대비 F 코드를 가진 수진자의 비율(모집단추정수/청소년인구)  - 연령별, 성별, 시/도별
전체 청소년 인구 대비 F 코드를 가진 수진자 비율의 연도에 따른 변화 (청소년 인구 중 정신질환을 가진 청소년의 비율)

#### 전체 청소년 인구 대비 F 코드를 가진 수진자의 비율
전체상병 대비 F 코드를 가진 수진자 비율의 연도에 따른 변화

아래 그래프와 같이 연령별, 성별, 시/도별 그래프를 만듬

전체상병 대비 F 코드를 가진 수진자 비율의 연도에 따른 변화 - xx성별 연령군별|
:-------------------------:|
|![전체상병 대비 F 코드를 가진 수진자 비율의 연도에 따른 변화 - 여아 연령군별](https://user-images.githubusercontent.com/68261338/104877215-b74d5180-599c-11eb-81b2-06a8a0bfa3c6.png)

(단, 연령별 그래프는 16년도까지만 포함. 그리고 시/도 그래프는 크게 서울, 광역시 그리고 도로 묶어서 시각화하는게 제일 직관적임)

### F code 종류별 (F0-9, F10-19, F20-29, F30-39, F40-48, F50-59, F90-98) 수진자의 수와 인구대비 비율 - 연령별, 성별, 시/도별

성별 수진자 수 및 비율

![스크린샷 2021-01-18 오후 3 33 34](https://user-images.githubusercontent.com/68368668/104880233-8a03a200-59a2-11eb-8ca2-e4372e8a4092.png)

연령별 수진자 비율

![스크린샷 2021-01-18 오후 3 49 54](https://user-images.githubusercontent.com/68368668/104881545-d0f29700-59a4-11eb-99ea-cad068b68875.png)

시/도별 수진자 비율

![스크린샷 2021-01-18 오후 3 50 24](https://user-images.githubusercontent.com/68368668/104881584-e2d43a00-59a4-11eb-8736-c7252e97d113.png)

### 다빈도 F code별 수진자의 수와 인구대비 비율 - 연령별, 성별, 시/도별

성별 수진자 수 및 비율

![스크린샷 2021-01-18 오후 3 38 03](https://user-images.githubusercontent.com/68368668/104880585-2af25d00-59a3-11eb-8f49-496a79dc5517.png)

연령별 수진자 비율

![스크린샷 2021-01-18 오후 3 46 44](https://user-images.githubusercontent.com/68368668/104881283-604b7a80-59a4-11eb-860a-3933f667b2bb.png)

시/도별 수진자 비율

![스크린샷 2021-01-18 오후 3 47 26](https://user-images.githubusercontent.com/68368668/104881329-7822fe80-59a4-11eb-8b48-1384ed3489f2.png)

#### bar graph: Total F code 의 수를 100으로 두었을 때(a bar), 다빈도 F code의 비율이 bar내부를 분할, 각 연령집단이 하나의 bar(전체 6개 bars), 남/녀는 서로 다른 그래프


## 상병레벨 (다빈도 F code 중심분석 + F01, F20, F42)

### 수진자 수 분석 - 상급병원 및 의원

상급병원 및 의원 F-code Top13

![스크린샷 2021-01-18 오후 3 51 16](https://user-images.githubusercontent.com/68368668/104881643-013a3580-59a5-11eb-9ba0-cd1bb8fd1310.png)

심사결정 요양급여비용총액의 상급병원과 의원의 평균차이

![스크린샷 2021-01-18 오후 3 53 42](https://user-images.githubusercontent.com/68368668/104881832-59713780-59a5-11eb-89e7-8fe2413c6930.png)

요양일수의 상급병원과 의원의 평균차이

![스크린샷 2021-01-18 오후 3 54 56](https://user-images.githubusercontent.com/68368668/104881916-845b8b80-59a5-11eb-936d-1eb58c77d868.png)

F_code 별 수진자수(sampling 추정수)/상급병원, 의원 별 총 병원수

![스크린샷 2021-01-18 오후 3 56 15](https://user-images.githubusercontent.com/68368668/104882047-b40a9380-59a5-11eb-8e4a-c88f5cb3dbe0.png)

### 상병의 총 수진자 의 수를 100으로 두었을 때(a bar), 각 연령집단의 비율이 bar 내부를 분할, 연도가 하나의 bar (2009-2016), 남/녀는 서로 다른 그래프

xx코드 상병 총 xx상병 수진자 수 연령집단 비율 그래프|
:-------------------------:|
![F51상병 총 남아 수진자 수 연령집단 비율](https://user-images.githubusercontent.com/68261338/104877745-d7314500-599d-11eb-8dc6-4c168fec464f.png)|






※ 본 자료는 건강보험심사평가원 환자표본자료(HIRA-NPS-2009-0001)를 활용한 것이며, 연구결과는 건강보험심사평가원 및 보건복지부와 관련이 없음을 밝힙니다.
