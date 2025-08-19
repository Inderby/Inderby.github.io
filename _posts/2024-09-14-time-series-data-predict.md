---
title: [ 단일 시계열 모델 분석-ARIMA ]
author: excelsiorKim
date: 2024-09-14 13:30:03 +0900
categories: [AI, ARIMA]
tags: [AI, ARIMA]
keywords: [AI, ARIMA]
description: 시계열 전력 데이터 분석
toc: true
toc_sticky: true
---

# 시계열 데이터의 속성
### 시계열 데이터의 정의
- 데이터는 관측 시점과 변수에 따라 크게 2종류로 구분될 수 있다.
  - 횡단면(비 시계열) 데이터(Cross-Sectional)
    - 한 시점에 여러 변수들에 대한 자료를 수집한 것.
  - 시계열(종단면) 데이터(Time-Series)
    - 시간의 흐름에 따라 한 변수에 대한 자료를 수집한 것
    - Longitudinal Data 라고도 불림
- 횡단면 데이터와 시계열 데이터가 병합된 데이터를 Panel 데이터라고 부른다.
  - 관측치(변량) 2개 이상을 시점 2개 이상으로 누적
  - 일반적으로 패널 데이터 들은 Truncation 되거나 Censoring되어 있는 경우가 많은데, 이를 Unbalanced Panel Data라고 부름

### 시계열 데이터의 특징과 형태
- 시계열 자료는 추세변동, 계절 변동 등 크고 작은 다양한 변동 요인들이 다중적으로 중첩되어 있다.
- 시계열 데이터의 구성 요소
  - 계절성(Seasonal)
  - 추세성(Trend)
  - 순환성(Cycle)
  - 불규칙요소(Irregular, Residual)
- 시계열 데이터의 형태는 아래와 같이 분류한다.
  - 계절 변동
  - 추세 변동
  - 계절적 추세 변동
  - 순환 변동
  - 우연 변동(White Noise라고도 부름)

### 백색 잡음(White Noise)
- **백색 잡음이란 패턴이 남아있지 않고 무작위로 야기되는 잡음을 의미한다.**
  - 백색 잡음은 시간에 따라 상관관계가 없는 랜덤한 오차 항이라는 것. (각 관측치가 서로 독립적이며, 평균이 0이고 일정한 분산을 가진다.)
- 백색 잡음은 2가지 속성을 만족해야 하고, 하나라도 만족하지 않으면 모델에 대한 개선 여지가 있음을 의미한다.
- 모델링에서의 백색 잡음
  - 많은 시계열 모델(예: ARIMA)에서는 모델의 오차항이 백색 잡음의 특성을 가져야 한다고 가정하고 있다. 이는 모델이 데이터의 모든 유의미한 패턴을 잘 포착했다는 것을 의미한다.
  - 잔차들은 평균이 0인 정규 분포이며, 일정한 분산을 가져야한다.
  - 잔차들은 시간의 흐름에 따른 상관성이 없어야 한다.(잔차 = 관측값 - 예측값)
    - 이러한 상관성을 자기상관성(Autocorrelation)이라 부른다.
    - 각 데이터 포인트가 각각 독립성을 지니는 것은 시계열 데이터의 기본적인 가정이기도 하며 이를 i.i.d(individually and independently distributed)
    - 이러한 상관성은 ACF, PACF 등과 같은 함수들을 통해 확인해볼 수 있다.(해당 내용은 뒤에서 자세히 다룬다)
    - 잔차에서 패턴이 발견되면 모델 개선의 여지가 있다는 것을 의미한다.
  - 시계열 예측 모델이 실제 현상의 트렌드 및 주기를 잘 반영할 수록 잔차의 번동이 작아지게 되고 이를 바탕으로 모델이 개선되었는지의 여부를 파악할 수 있다.
    - 잔차 진단의 결과는 주로 시각화로 확인할 수 있다

### 잔차 진단(시계열 데이터 모델의 기본 가정들)
- 잔차에 대한 진단은 정상성, 정규 분포, 자기 상관성, 등분산성 총 4가지를 결정하게 되며, 이러한 특성은 시계열 데이터 모델의 기본가정들이다.
- **정상성**
  - 시계열 데이터가 시간에 관계없이 일정한 통계적 특성을 유지하는 상태.(시계열이 다음 조건을 만족할 때 정상성을 가진다고 말한다.)
    - 평균이 일정함 (시간에 따라 변하지 않음)
    - 분산이 일정함 (시간에 따라 변하지 않음)
    - 자기공분산이 시차에만 의존하고 시간에는 의존하지 않음
  - 정상성이 중요한 이유
    - 예측 가능성: 정상 시계열은 미래 행동을 더 쉽게 예측할 수 있음
    - 일반화: 한 기간에서 얻은 통계적 추론을 다른 기간에도 적용할 수 있음
    - 모델 적합성: 많은 시계열 모델(예: ARIMA)이 정상성을 가정함
  - 내재적인 패턴의 존재를 의미하는 단위근(Unit Root) 존재 여부를 검정한다.
  - 대표적으로 ADF 검정, KPSS 검정이 있다(이 둘의 귀무가설은 서로 반대이다)
    - ADF 검정의 귀무가설 : 단위근이 존재한다.(비정상)
    - KPSS 검정의 귀무가설 : 단위근이 없다.(정상)
  - **정상성 확보 방법**
    - 차분(Differencing): 연속된 관측치의 차이를 계산
    - 로그 변환: 분산을 안정화시키는 데 도움
    - 추세 제거: 선형 또는 비선형 추세를 제거
    - 계절성 조정: 계절적 패턴을 제거
- **정규 분포**
  - 귀무가설 : 정규분포를 따른다.
  - 대립가설 : 정규분포를 따르지 않는다.
- **자기 상관성(Autocorrelation)**
  - 귀무가설 : 시간이 지나면 autocorrelation은 0이다.
  - 대립가설 : 시간이 지나면 autocorrelation은 0이 아니다. 
- **등분산성**
  - 귀무가설 : 시간이 지나면 등분산이다.
  - 대립가설 : 시간이 지나면 등분산이 아니다. 발산하는 분산이다.

### 시계열 데이터의 처리
- 시계열 데이터는 추세변동, 계절 변동 등 크고 작은 다양한 변동요인들이 다중적으로 중첩되어 있다.
- 전처리의 목적은 데이터에서 추세, 계절성, 주기성 등의 패턴을 제거하여 위에서 말한 정상성(stationarity)을 확보하는 것이다.
- 이러한 시계열 데이터의 분석을 수행할 때는 2가지 접근법을 사용할 수 있다.
  - 요소 분해법(Decomposition): 내포된 변동 요인이 고정적인 패턴을 보이는 경우
    - 가법 모형 : 선형적이고, 트렌드, 계절성, 순환이 서로 독립적이라 가정할 수 있는 경우
    - 승법 모형 : 선형적이고, 트렌드, 계절성, 순환이 서로 영향을 주고 받는 경우
  - 평활법(Smoothing): 다양한 변동 요인이 고정적인 패턴을 보이지 않는 경우
    - 이동 평균 평활법 : 과거로부터 현재까지의 시계열 자료를 대상으로 일정 기간별 이동평균을 계산하고 이들의 추세를 통해 다음 기간을 예측
    - 지수 평균 평활법 : 모든 시계열 자료를 사용하여 평균을 구하고, 시간의 흐름에 따라서 최근 시계열 데이터에 더많은 가중치를 부여하여 미래의 값을 예측
- 시계열 데이터 처리 방법들
  - 변동폭이 일정하지 않은 경우 : log 변환
  - 추세, 계졀성이 존재하는 경우 : 차분(differencing)

- 즉, 정상성은 시계열 모델링의 기본 가정이며, 많은 통계적 기법들이 정상성을 전제로 한다. 따라서 시계열 분석을 할 때는 먼저 데이터가 정상성을 가지는지 확인하고, 필요하다면 위의 방법들을 사용하여 정상성을 확보한 후 분석을 진행하는게 맞다.

# 시계열 확률 모형
- 분석 모형에 따라 추정값과 관측값의 차이인 잔차(Residual)의 시계열 자료(Residual Error Series)는 확률 과정을 통해 나타낼 수 있다.
  - 이때 고려되는 모델이 확률모형(Stochastic Model)이다.
  - 시계열 분석 모델이 실제 현상과 적합한 경우에 시계열 자료는 백색잡음(White Noise)이 된다는 가정을 바탕으로 한다.
  - 즉, 잔차는 서로 독립적인 경우에 시계열 분석 모델이 실제 현상을 잘 설명하고 있다고 해석할 수 있음을 의미한다.

- 확률모형의 분석은 다음의 4가지 단계를 걸쳐 수행한다.
  - 모델 설정(Model Identification)
    - ACF, PACF 같은 지표와 ARMA, ARIMA와 같은 프로그램을 이용해 적합한 모형을 선택
  - 모수 추정(Parameter Estimation)
    - 설정한 모델에 따라 모수(계수)를 추청하고 적합성을 검토
  - 통계적 검정(Statistical Testing)
    - 모델의 적합도에 따른 잔차의 정상성과 분석결과에 대한 검정을 수행
  - 예측(Forecasting)
    - 시계열 분석 모델을 통해 미래의 값을 예측

## 단일 변량 시계열 모델
- 하나의 변수에 대한 시계열 데이터를 분석하는 모델은 기본적으로 AR, MA, ARMA, ARIMA 모델이 있다.
### AR 모델
- 자기 상관성을 시계열 모형으로 구성한 거승로, 변수의 과거 관측값의 선형 결합을 통해 변수의 미래값을 예측하는 모델.
- 이전의 관측값이 이후의 관측값에 영향을 준다는 아이디어에 기반하고 있다.

### MA 모델
- 예측 오차를 이용하여 미래값을 예측하는 모델이다.
- 파라미터 값으로는 q를 이용하며 MA(q)의 형태로 나타낼 수 있다.

### ARMA 모델(안정 시계열 모델)
- p개의 자기 자신의 과거값과 q개의 과거백색 잡음의 선형조합을 의미하고, AR(p) 모형과 MA(q)모형이 합쳐진 모델이다.
- AR(p)모형의 정상성, MA(q)모형의 가역성 조건을 만족해야 한다.
- 파라미터 값으로는 p,q를 이용하며, ARMA(p,q)의 형태로 나타낼 수 있다.

### ARIMA 모델(불안정 시계열 모델)
- ARMA 모델에서 d차 차분을 추가적으로 적용시킨 모델로 비정상시계열에도 바로 적용할 수 있다는 장점이 있다.
- ARIMA(p,d,q) : d차 차분한 데이터에 AR(p)모형과 MA(q)모형을 합친 모델
  - I는 Integrated를 뜻하고 이는 '누적'을 의미함.

## 자기상관함수(ACF, PACF)
### ACF(AutoCorrelation Function)
- 시차(lag)에 따른 연속적인 관측값 사이의 상호 연관관계를 의미하며, 시차가 커질 수록 0에 수렴하게 된다
- 동일한 변수를 시점을 달리하여 관측했을 때 시점에 따라 다른값 사이의 상호 연관관계를 척도로 나타낸 것이다.
- ACF를 통해서 시계열 자료의 정상성을 파악할 수 있다.
- 정상시계열인 경우엔 상대적으로 빠르게 0에 수렴하고, 비정상시계열은 천천히 감소한다.
### PACF(Partial AutoCorrelation Function)
- 부분자기상관함수
- 시차(lag)에 따른 편자기상관을 의미하며, 시차가 다른 두 시계열간의 순수한 상호연관관계를 의미한다.
- AR, ARMA, ARIMA에서 p값을 구하는 경우에 PACF 그래프를 통해서 구할 수 있으며, 0으로 수혐하게 되는 시점을 p값으로 잡으면 된다.


# ARIMA 모델 학습 코드 구현

### 라이브러리 불러오기

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

```

### 데이터 불러오기


```python
df = pd.read_csv('./household_power_consumption.txt', sep=';', parse_dates=[['Date', 'Time']])

```
- 이때 데이터 셋의 형태가 Date와 Time column으로 나눠져 있기 때문에 이를 하나로 합친 `Date_Time`으로 만든다.
- 데이터 확인하기(결측치 여부 column 타입)


```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 2075259 entries, 0 to 2075258
Data columns (total 8 columns):
 #   Column                 Dtype         
---  ------                 -----         
 0   Date_Time              datetime64[ns]
 1   Global_active_power    object        
 2   Global_reactive_power  object        
 3   Voltage                object        
 4   Global_intensity       object        
 5   Sub_metering_1         object        
 6   Sub_metering_2         object        
 7   Sub_metering_3         float64       
dtypes: datetime64(1), float64(1), object(6)
memory usage: 126.7+ MB

```
- 추가적으로 `df.isnull().sum()`과 같은 메서드를 활용해서 확인해봤다.

- 다음으로 활용할 column만 남기고 날짜를 인덱스로 변환했다.


```python
ts= df.loc[:, ['Date_Time', 'Global_active_power']]
ts.index = ts.Date_Time
ts = ts.drop(columns="Date_Time") # 인덱스로 변환된 뒤 남아있는 Date_Time column도 소거.
print(ts)
---출력 결과---

                    Global_active_power
Date_Time                              
2006-12-16 17:24:00               4.216
2006-12-16 17:25:00               5.360
2006-12-16 17:26:00               5.374
2006-12-16 17:27:00               5.388
2006-12-16 17:28:00               3.666
...                                 ...
2010-11-26 20:58:00               0.946
2010-11-26 20:59:00               0.944
2010-11-26 21:00:00               0.938
2010-11-26 21:01:00               0.934
2010-11-26 21:02:00               0.932

[2075259 rows x 1 columns]

```
- 데이터 가공


```python
ts = ts.dropna() # NaN값도 마저 제거
ts['Global_active_power'] = pd.to_numeric(ts['Global_active_power'], errors='coerce') # str 타입을 float 타입으로 변경
ts_resampled = ts.resample('1d').mean() # 1분 단위로 기록된 데이터를 1일 단위로 resampling
--- 출력 ---
            Global_active_power
Date_Time                      
2006-12-16             3.053475
2006-12-17             2.354486
2006-12-18             1.530435
2006-12-19             1.157079
2006-12-20             1.545658
...                         ...
2010-11-22             1.417733
2010-11-23             1.095511
2010-11-24             1.247394
2010-11-25             0.993864
2010-11-26             1.178230

[1442 rows x 1 columns]

```
- 데이터 분포 시각화


```python
def plot_ts(data, color, alpha, label):

    plt.figure(figsize=(11,5))
    plt.plot(data, color=color, alpha=alpha, label=label)
    plt.title("Mean Consumption of Electricity")
    plt.ylabel('Mean Consumption')
    plt.legend()
    plt.show()

```
- 출력


```python
plot_ts(ts_resampled, 'blue', 0.25, 'Original')

```
![graph-image](https://github.com/Inderby/Inderby.github.io/blob/master/assets/img/2024-09-16-arima/original-data-graph.png?raw=true)

### 시계열 데이터의 정상성 검정 : ADF
- statsmodels 라이브러리의 adfuller 함수를 통해 검정 실시


```python
from statsmodels.tsa.stattools import adfuller
ts = ts_resampled.dropna()
adfuller(ts, autolag='AIC')
--- 출력 결과 ---
(-3.684227530202004, # ADF 통계량
 0.004340557878211848, # p-value
 22, # 사용된 lags 수
 1410, # nobs 라는데 뭔지 모르겠음
 {'1%': -3.434996272992033,   # 신뢰도 퍼센트별 기각역
  '5%': -2.863592002111143,
  '10%': -2.5678624767365825},
 428.11600249017283) # icbest 라는데 뭔지 모르겠음

```

- ADF 결과 중 통계량, p-value, 기각역을 계산하고 출력하는 함수 정의 및 실행


```python
def ADF_test(data):
	# ADF 실시
    results = adfuller(data, autolag='AIC')
    
    # 통계량
    s = results[0]
    # p-value
    p = results[1]
    # 기각역
    cv = results[4]
    
    # 출력
    print('-'*30)
    print('Augemented Dickey-Fuller Test')
    print('H0 : 단위근이 존재한다 (비정상 시계열)')
    print('Ha : 단위근이 없다 (정상 시계열)')
    print('Critical Values : {}'.format(cv))
    print('-'*30)
    print('Test Statistics : {:.4f}'.format(s))
    print('p-value : {:.4f}'.format(p))
    print('-'*30)

# ts 데이터로 ADF 실행해보기
ADF_test(ts)
---출력---
------------------------------
Augemented Dickey-Fuller Test
H0 : 단위근이 존재한다 (비정상 시계열)
Ha : 단위근이 없다 (정상 시계열)
Critical Values : {'1%': -3.434996272992033, '5%': -2.863592002111143, '10%': -2.5678624767365825}
------------------------------
Test Statistics : -3.6842
p-value : 0.0043
------------------------------

```
- 단위근이 존재하므로 비정상 시계열이다.(정상 시계열로 만들어줘야 함)
- 이동 평균 함수를 이용한 평균 및 표준 편차 분포 시각화

```python
def plot_rolling(data, roll_size):
    # 이동평균함수(rolling) - 평균, 표준편차
    roll_mean = data.rolling(window=roll_size).mean()
    roll_std = data.rolling(window=roll_size).std()
    
    # 시각화
    plt.figure(figsize=(11,5))
    plt.plot(data, color='blue', alpha=0.25, label='Original')
    plt.plot(roll_mean, color='black', label='Rolling mean')
    plt.plot(roll_std, color='red', label='Rolling std')
    plt.title('Rolling Mean & Standard Deviation')
    plt.ylabel("Mean Temperature")
    plt.legend()
    plt.show()

# 함수 실행
plot_rolling(ts, 6)

```

![rolling-deviation](https://github.com/Inderby/Inderby.github.io/blob/master/assets/img/2024-09-16-arima/rolling-mean-standard-deviation.png?raw=true)

### 차분(Differencing)으로 정상 시계열 만들기
- 방법이 두가지가 있음
  - `.shift()`함수를 이용하기
  - `.diff()`함수 이용하기
- `.diff()`함수 이용이 더 간단하고 결측치 처리도 한번에 해줌.


```python
ts_diff2 = ts.diff().dropna()

# ADF 테스트
ADF_test(ts_diff2)
--- 출력 ---
------------------------------
Augemented Dickey-Fuller Test
H0 : 단위근이 존재한다 (비정상 시계열)
Ha : 단위근이 없다 (정상 시계열)
Critical Values : {'1%': -3.4349896798463924, '5%': -2.8635890925399354, '10%': -2.567860927320659}
------------------------------
Test Statistics : -13.0966
p-value : 0.0000
------------------------------

```

### ACF, PACF 실시 및 시각화

```python
from statsmodels.tsa.stattools import acf, pacf

# ACF
acf_20 = acf(x=ts_diff2, nlags=150)
# PACF
pacf_20 = pacf(x=ts_diff2, nlags=150, method='ols')

# 95% 신뢰구간 계산하기
confidence = 1.96/np.sqrt(len(ts_diff2))

# 시각화
plt.figure(figsize=(8,4))
# ACF
plt.subplot(1,2,1)
plt.plot(acf_20)
plt.axhline(y=0, linestyle='--', alpha=0.5)
plt.axhline(y=-confidence, linestyle='--', alpha=0.5)
plt.axhline(y=confidence, linestyle='--', alpha=0.5)
plt.title('AutoCorrelation Function')
# PACF
plt.subplot(1,2,2)
plt.plot(pacf_20)
plt.axhline(y=0, linestyle='--', alpha=0.5)
plt.axhline(y=-confidence, linestyle='--', alpha=0.5)
plt.axhline(y=confidence, linestyle='--', alpha=0.5)
plt.title('Partial AutoCorrelation Function')

plt.tight_layout()

```
![acf-pacf](https://github.com/Inderby/Inderby.github.io/blob/master/assets/img/2024-09-16-arima/acf-pacf.png?raw=true)
- 그래프에서 유추하기로는 `ARIMA(p, d, q)` 중에
  - 최적의 q값(신뢰구간 최초 진입 시점) = ACF(q, MA) = 60
  - 최적의 p값(신뢰구간 최초 진입 시점) = PACF(p, AR) = 23
- 예상되는 최적의 ARIMA 모델 : `ARIMA(60, 0, 23)`

### 검증해보기 위한 벤치마크
- 우선 `ARIMA(1, 0, 1)` 로 실시해보기

```python
from statsmodels.tsa.arima.model import ARIMA

# index를 period로 변환해주어야 warning이 뜨지 않음
ts_copy = ts.copy()
ts_copy.index = pd.DatetimeIndex(ts_copy.index).to_period('D')

# 예측을 시작할 위치(이후 차분을 적용하기 때문에 맞추어주었음
start_idx = ts_copy.index[1]

# ARIMA(1,0,1)
model1 = ARIMA(ts_copy, order=(1,0,1))
# fit model
model1_fit = model1.fit()

# 전체에 대한 예측 실시
forecast1 = model1_fit.predict(start=start_idx)

```
- 예측 시각화 및 오차함수(MSE)함수 정의 및 실시

```python
from sklearn.metrics import mean_squared_error

def plot_and_error(data, forecast):
    # MSE 계산
    mse = mean_squared_error(data, forecast)
    # 시각화
    plt.figure(figsize=(11,5))
    plt.plot(data, color='blue', alpha=0.25 , label='Original')
    plt.plot(forecast, color='red', label='predicted')
    plt.title("Time Series Forecast")
    plt.ylabel("Mean Consumption")
    plt.legend()
    plt.show()
    # MSE 출력
    print('Mean Squared Error : {:.4f}'.format(mse))

plot_and_error(ts[1:], forecast1)

```
- 출력 결과
![bench-mark](https://github.com/Inderby/Inderby.github.io/blob/master/assets/img/2024-09-16-arima/bench-mark-test.png?raw=true)

- 최적화 파라미터로 ARIMA 실시해보기


```python
# 모델 파라미터 최적화 (p=60, d=0, q=23)
model2 = ARIMA(ts_copy, order=(60,0,23))
# fit
model2_fit = model2.fit()
# 예측
forecast2 = model2_fit.predict(start=start_idx)
# 시각화 및 MSE 연산
plot_and_error(ts[1:], forecast2)

```

- 출력 결과
![optimize](https://github.com/Inderby/Inderby.github.io/blob/master/assets/img/2024-09-16-arima/optimize.png?raw=true)
- MSE가 상대적으로 줄어들었으므로(0.0955 -> 0.0765) 성능의 개선이 이루어졌음을 확인 가능하다.


### Auto ARIMA 활용
- p, d, q값을 직접 설정하는 방식으로 최적화를 진행하는 것은 다소 주관적으로 판단해야함. 때문에 Auto ARIMA 라이브러리를 활용해서 파라미터의 탐색 과정을 자동화하여 쉽고 정확하게 가장 높은 성능ㅇ을 이끌어낼 수 있도록 시도함.
  - `pmdarima` 라는 파이썬 라이브러리를 활용함

- 라이브러리 불러온 뒤 최적 차분 찾기

```python
import pmdarima as pm

train = ts['Global_active_power'][:int(0.8*len(ts))] # 훈련 데이터 80%
test = ts['Global_active_power'][int(0.8*len(ts)):] # 테스트 데이터 20%

kpss_diffs = pm.arima.ndiffs(train_1, alpha=0.05, test='kpss', max_d=5)
adf_diffs = pm.arima.ndiffs(train_1, alpha=0.05, test='adf', max_d=5)
n_diffs = max(kpss_diffs, adf_diffs)

print(f"Optimized 'd' = {n_diffs}")
--- 출력 --- 
Optimized 'd' = 0

```

### 최적 파라미터 탐색
- 필요한 파라미터를 제외하고 기본 값으로 돌리는 것이 AIC 점수가 더 낮게 나왔다.
  - 어차피 early stop 기능으로 인해 차수가 100까지 오르지도 않는다.

```python
#model = pm.auto_arima(y=train_1,		# 데이터
#                      d=n_diffs,	# 차분 (d), 기본값 = None
#                      start_p= 0,	# 시작 p값, 기본값 = 2
#                      max_p = 100,	# p 최대값, 기본값 = 5
#                      start_q= 0,	# 시작 q값, 기본값 = 2
#                      max_q = 100,	# q 최대값, 기본값 = 5
#                      m=1,			# season의 주기, 기본값 = 1
#                      seasonal=False,	# sARIMA를 실시, 기본값 = True
#                      stepwise=True,	# stepwise algorithm, 기본값 = True
#                      trace=True)		# 각 step을 출력할지, 기본값 = False

model2 = pm.auto_arima(train, d=0, seasonal=False, trace=True)
--- 출력 ---
Performing stepwise search to minimize aic
 ARIMA(2,0,2)(0,0,0)[0]             : AIC=655.274, Time=0.31 sec
 ARIMA(0,0,0)(0,0,0)[0]             : AIC=3689.862, Time=0.01 sec
 ARIMA(1,0,0)(0,0,0)[0]             : AIC=1071.916, Time=0.05 sec
 ARIMA(0,0,1)(0,0,0)[0]             : AIC=2626.813, Time=0.05 sec
 ARIMA(1,0,2)(0,0,0)[0]             : AIC=659.417, Time=0.20 sec
 ARIMA(2,0,1)(0,0,0)[0]             : AIC=672.290, Time=0.31 sec
 ARIMA(3,0,2)(0,0,0)[0]             : AIC=654.374, Time=0.37 sec
 ARIMA(3,0,1)(0,0,0)[0]             : AIC=658.800, Time=0.25 sec
 ARIMA(4,0,2)(0,0,0)[0]             : AIC=661.419, Time=0.68 sec
 ARIMA(3,0,3)(0,0,0)[0]             : AIC=616.361, Time=0.55 sec
 ARIMA(2,0,3)(0,0,0)[0]             : AIC=656.129, Time=0.38 sec
 ARIMA(4,0,3)(0,0,0)[0]             : AIC=546.862, Time=0.60 sec
 ARIMA(5,0,3)(0,0,0)[0]             : AIC=551.235, Time=1.28 sec
 ARIMA(4,0,4)(0,0,0)[0]             : AIC=548.131, Time=0.93 sec
 ARIMA(3,0,4)(0,0,0)[0]             : AIC=549.820, Time=0.68 sec
 ARIMA(5,0,2)(0,0,0)[0]             : AIC=716.507, Time=0.60 sec
 ARIMA(5,0,4)(0,0,0)[0]             : AIC=550.676, Time=1.20 sec
 ARIMA(4,0,3)(0,0,0)[0] intercept   : AIC=548.674, Time=1.06 sec

```

### 예측 및 평가
- 우선 트렌드만 고려한 예측을 실시해봤다.

```python
# 예측 -> 리스트로 변환
pred = model2.predict(n_periods=len(test)).to_list()
# 데이터프레임 생성
test_pred = pd.DataFrame({'test':test, 'pred':pred}, index=test.index)

```
- 시각화

```python
plt.plot(train, label='Train')
plt.plot(test, label='Test')
plt.plot(test_pred.pred, label='Predicted')
plt.legend()
plt.show()

```

![predict-only-trend](https://github.com/Inderby/Inderby.github.io/blob/master/assets/img/2024-09-16-arima/predict-only-trend.png?raw=true)
- 잘 따라가는 것으로 보인다.
- 이번엔 한 지점에 대한 예측을 진행하고 모델을 업데이트 하는 방식으로 예측을 진행해봤다.(트렌드 이외의 변동 요인들이 모두 반영된다.)


```python
# one point forcast 함수 정의, 신뢰구간도 함께 담아보기
def forcast_one_step():
    fc, conf = model2.predict(n_periods=1, return_conf_int=True)
    return fc.tolist()[0], np.asarray(conf).tolist()[0]

# 값들을 담을 빈 리스트를 생성
y_pred = []
pred_upper = []
pred_lower = []

# for문을 통한 예측 및 모델 업데이트를 반복함
for new_ob in test:
    fc, conf = forcast_one_step()
    y_pred.append(fc)
    pred_upper.append(conf[1])
    pred_lower.append(conf[0])
    model2.update(new_ob)

```
- 예측 결과를 데이터 프레임으로 만들기

```python
test_pred2 = pd.DataFrame({'test':test, 'pred':y_pred})
y_pred_df = test_pred2['pred']	# Series로 반환

```
- 시각화로 확인

```python
plt.plot(train, label='Train')
plt.plot(test, label='Test')
plt.plot(y_pred_df, label='Predicted')
plt.legend()
plt.show()

```
![predict](https://github.com/Inderby/Inderby.github.io/blob/master/assets/img/2024-09-16-arima/predict.png?raw=true)
- 전체적인 주기성, 경향성과 트렌드를 따라가며 합리적인 예측 결과를 보여준다.

### 모델 오차 계산

```python
# sklearn으로 MAPE 계산
from sklearn.metrics import mean_absolute_percentage_error
print(f"MAPE : {mean_absolute_percentage_error(test_1, y_pred):.3f}")


# numpy로 직접 계산
def MAPE(y_test, y_pred): 
	return np.mean(np.abs((test_1 - y_pred) / y_test)) 
print(f"MAPE : {MAPE(test_1, y_pred):.3f}")
--- 출력 ---
MAPE : 0.197
MAPE : 0.197

```