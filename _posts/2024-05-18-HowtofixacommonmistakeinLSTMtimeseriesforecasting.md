---
title: "LSTM 시계열 예측에서 흔히 발생하는 오류를 수정하는 방법"
description: ""
coverImage: "/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_0.png"
date: 2024-05-18 19:35
ogImage:
  url: /assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_0.png
tag: Tech
originalTitle: "How to fix a common mistake in LSTM time series forecasting"
link: "https://medium.com/@srmousavi25/how-to-fix-a-common-mistake-in-lstm-time-series-forecasting-4d4d51d9948f"
---

LSTM을 시계열 예측에 사용할 때, 사람들은 흔히 범할 수 있는 함정에 빠지곤 합니다. 이를 설명하기 위해서는 회귀자와 예측자의 작동 방식을 살펴볼 필요가 있습니다. 예측 알고리즘은 시계열 데이터를 다루는 방법을 아래와 같이 보여줍니다:

![How a forecasting algorithm works](/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_0.png)

한편, 회귀 문제는 다음과 같이 보일 것입니다:

![How a regression problem looks](/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_1.png)

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

LSTM이 회귀기이므로 시계열을 회귀 문제로 변환해야 합니다. 이를 수행하는 여러 방법이 있지만, 이 섹션에서는 창(Window) 및 다중 단계(Multi-Step) 방법에 대해 설명하고, 어떻게 작동하는지와 특히 이를 활용할 때 발생할 수 있는 일반적인 실수를 피하는 방법에 대해 논의할 것입니다.

창 방법(Window Method)에서, 시계열은 이전 각 시간 단계의 값과 결합되어 창이라고 불리는 가상 특성으로 되어 있습니다. 여기서 창 크기가 3인 창이 있습니다:

![창 방법 이미지](/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_2.png)

다음 함수는 단일 시계열에서 창 방법 데이터 세트를 생성합니다. 사용자는 이전 값의 수(보통 look back이라고 함)를 선택해야 합니다. 결과 데이터 세트에는 대각선 반복이 있으며, look-back 값에 따라 샘플의 수가 달라집니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
def window(sequences, look_back):
    X, y = [], []
    for i in range(len(sequences)-look_back-1):
        x = sequences[i:(i+look_back)]
        X.append(x)
        y.append(sequences[i + look_back])
    return np.array(X), np.array(y)
```

이제 결과를 살펴보겠습니다. 모델을 훈련한 후에는 테스트 세트에서 테스트됩니다. 다양한 소스와 튜토리얼에서는 비슷한 방법을 사용하여 결과를 컴파일하는 것을 제안했습니다. 그러나 나중에 설명할 것처럼 이 방법은 신빙성이 없습니다. 그럼 지금은 코드와 결과가 어떻게 보이는지 살펴보겠습니다:

```js
look_back = 3
X, y = window(ts_data, look_back)

# 훈련-테스트 분할
train_ratio = 0.8
train_size = int(train_ratio * len(ts_data))
X_train, X_test = X[:train_size-look_back], X[train_size-look_back:]
y_train, y_test = y[:train_size-look_back], y[train_size-look_back:]

# LSTM 모델 생성 및 훈련
model = Sequential()
model.add(LSTM(units=72, activation='tanh', input_shape=(look_back, 1)))
model.add(Dense(1))
model.compile(loss='mean_squared_error', optimizer='Adam', metrics=['mape'])

model.fit(x=X_train, y=y_train, epochs=500, batch_size=18, verbose=2)

# 예측 생성
forecasts = model.predict(X_test)
lstm_fits = model.predict(X_train)

# 메트릭스 계산
mape = mean_absolute_percentage_error(y_test, forecasts)
r2 = r2_score(y_train, lstm_fits)

# 날짜 초기화
date_range = pd.date_range(start='1990-01-01', end='2023-09-30', freq='M')

# 맞춤 값에 원래 시계열과 맞추기 위한 비어있는 값 추가
fits = np.full(train_size, np.nan)
for i in range(train_size-look_back):
    fits[i+look_back] = lstm_fits[i]

# 실제값, 맞춤값, 예측값 플롯
plt.figure(figsize=(10, 6))
plt.plot(date_range, ts_data, label='Actual', color='blue')
plt.plot(date_range[:train_size], fits, label='Fitted', color='green')
plt.plot(date_range[train_size:], forecasts, label='Forecast', color='red')
plt.title('FSC - Short - Passengers\nOne Step Forward Forecast')
plt.xlabel('Date')
plt.ylabel('Passengers')
plt.legend()
plt.text(0.05, 0.05, f'R2 = {r2*100:.2f}%\nMAPE = {mape*100:.2f}%', transform=plt.gca().transAxes, fontsize=12)
plt.grid(True)
plt.show()
```

<img src="/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_3.png" />

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

문제: 결과는 훌륭해 보입니다. 그러나 샘플 테스트 세트를 살펴보면 특이한 결함이 보입니다:

![이미지](/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_4.png)

예를 들어 y9를 생성하는 데에는 y8이 입력으로 사용되었습니다. 훈련에는 사용되지 않았지만 미래 값을 포함하는 것은 이상합니다. 왜냐하면 우리는 미래의 시점을 예측하고 있기 때문입니다.

해결책: 직접적으로 이전 값을 예측 값으로 대체하는 반복적 테스트 세트를 사용하면 이 문제를 해결할 수 있습니다. 이러한 배치 방식에서 모델은 자체 예측에 기반을 둔다. 일반적인 예측 알고리즘과 유사합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

아래 루프에서 다음을 수행합니다:

```js
# 반복적인 예측 및 대체
for i in range(len(X_test)):
    forecasts[i] = model.predict(X_test[i].reshape(1, look_back, 1))
    if i != len(X_test)-1:
        X_test[i+1,look_back-1] = forecasts[i]
        for j in range(look_back-1):
            X_test[i+1,j] = X_test[i,j+1]
```

결과는 완벽하지는 않지만 적어도 정허하다고 할 수 있습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

<img src="/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_6.png" />

다중 단계 방법은 창 방법과 유사하지만 더 많은 대상 단계를 갖습니다. 다음은 두 개의 전방 단계의 샘플입니다:

<img src="/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_7.png" />

사실, 이 방법에서 사용자는 n_steps_in과 n_steps_out을 선택해야 합니다. 다음 코드는 단순 시계열을 다중 단계 LSTM 훈련을 위해 준비된 데이터 세트로 변환합니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
# 단변량 시퀀스를 다단계로 샘플링하기
def split_sequences(sequences, n_steps_in, n_steps_out):
 X, y = list(), list()
 for i in range(len(sequences)):
     # 해당 패턴의 끝을 찾음
     end_ix = i + n_steps_in
     out_end_ix = end_ix + n_steps_out
     # 시퀀스를 벗어나는지 확인
     if out_end_ix > len(sequences):
         break
     # 패턴의 입력 및 출력 부분 수집
     seq_x, seq_y = sequences[i:end_ix], sequences[end_ix:out_end_ix]
     X.append(seq_x)
     y.append(seq_y)
 return np.array(X), np.array(y)
```

이제, 특성 뿐만 아니라 타겟도 대각선 반복을 가지고 있어 시계열과 비교하려면 그들을 평균화하거나 예측 중 하나를 선택해야 합니다. 아래 코드에서는 첫 번째, 마지막 및 평균 예측의 결과가 생성되며, 그에 이어 플롯이 제시됩니다. 여기서 첫 번째 예측은 한 달 전 예측을 의미하며, 마지막 예측은 12개월 전 예측을 의미합니다.

```js
n_steps_in = 12
n_steps_out = 12

X, y = split_sequences(ts_data, n_steps_in, n_steps_out)
X = X.reshape(X.shape[0], X.shape[1], 1)
y = y.reshape(y.shape[0], y.shape[1], 1)

# 훈련 및 테스트 세트 분리
train_ratio = 0.8
train_size = int(train_ratio * len(ts_data))
X_train, X_test = X[:train_size-n_steps_in-n_steps_out+1], X[train_size-n_steps_in-n_steps_out+1:]
y_train = y[:train_size-n_steps_in-n_steps_out+1]
y_test = ts_data[train_size:]

# LSTM 모델 생성 및 훈련
model = Sequential()
model.add(LSTM(units=72, activation='tanh', input_shape=(n_steps_in, 1)))
model.add(Dense(units=n_steps_out))
model.compile(loss='mean_squared_error', optimizer='Adam', metrics=['mape'])

model.fit(x=X_train, y=y_train, epochs=500, batch_size=18, verbose=2)

# 예측 생성
lstm_predictions = model.predict(X_test)
lstm_fitted = model.predict(X_train)

forecasts = [np.diag(np.fliplr(lstm_predictions), i).mean() for i in range(0, -lstm_predictions.shape[0], -1)]
fits = [np.diag(np.fliplr(lstm_fitted), i).mean() for i in range(lstm_fitted.shape[1]+n_steps_in - 1, -lstm_fitted.shape[0], -1)]
forecasts1 = lstm_predictions[n_steps_out-1:,0]
fits1 = model.predict(X)[:train_size-n_steps_in,0]
forecasts12 = lstm_predictions[:,n_steps_out-1]
fits12 = lstm_fitted[:,n_steps_out-1]

# Metric
av_mape = mean_absolute_percentage_error(y_test, forecasts)
av_r2 = r2_score(ts_data[n_steps_in:train_size], fits[n_steps_in:])
one_mape = mean_absolute_percentage_error(y_test[:-n_steps_out+1], forecasts1)
one_r2 = r2_score(ts_data[n_steps_in:train_size], fits1)
twelve_mape = mean_absolute_percentage_error(y_test, forecasts12)
twelve_r2 = r2_score(ts_data[n_steps_in+n_steps_out-1:train_size], fits12)

date_range = pd.date_range(start='1990-01-01', end='2023-09-30', freq='M')

# 실제, 적합 결과 및 예측을 플롯
plt.figure(figsize=(10, 6))
plt.plot(date_range, ts_data, label='Actual', color='blue')
plt.plot(date_range[:train_size], fits, label='Fitted', color='green')
plt.plot(date_range[train_size:], forecasts, label='Forecast', color='red')
plt.title('FSC - Short - Passengers\n. LSTM 12 Month Average Forecast')
plt.xlabel('Date')
plt.ylabel('Passengers')
plt.legend()
plt.text(0.05, 0.05, f'R2 = {av_r2*100:.2f}%\nMAPE = {av_mape*100:.2f}%', transform=plt.gca().transAxes, fontsize=12)
plt.grid(True)
plt.show()

plt.figure(figsize=(10, 6))
plt.plot(date_range, ts_data, label='Actual', color='blue')
plt.plot(date_range[n_steps_in:train_size], fits1, label='Fitted', color='green')
plt.plot(date_range[train_size:-n_steps_out+1], forecasts1, label='Forecast', color='red')
plt.title('FSC - Short - Passengers\n LSTM 1 Month in advance Forecast')
plt.xlabel('Date')
plt.ylabel('Passengers')
plt.legend()
plt.text(0.05, 0.05, f'R2 = {one_r2*100:.2f}%\nMAPE = {one_mape*100:.2f}%', transform=plt.gca().transAxes, fontsize=12)
plt.grid(True)
plt.show()

plt.figure(figsize=(10, 6))
plt.plot(date_range, ts_data, label='Actual', color='blue')
plt.plot(date_range[n_steps_in+n_steps_out-1:train_size], fits12, label='Fitted', color='green')
plt.plot(date_range[train_size:], forecasts12, label='Forecast', color='red')
plt.title('FSC - Short - Passengers\n LSTM 12 Months in advance Forecast')
plt.xlabel('Date')
plt.ylabel('Passengers')
plt.legend()
plt.text(0.05, 0.05, f'R2 = {twelve_r2*100:.2f}%\nMAPE = {twelve_mape*100:.2f}%', transform=plt.gca().transAxes, fontsize=12)
plt.grid(True)
plt.show()
```

이슈: 창 메서드와 동일한 문제가 여기에도 있습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![이미지](/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_8.png)

해상도: 창 법과 비슷한 방법을 사용할 수 있습니다. 그러나 n_steps_out을 test_size와 동일하게 선택할 수도 있습니다. 이렇게 하면 테스트 세트가 하나로 축소됩니다:

![이미지](/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_9.png)

다음 함수는 이를 정확히 수행합니다. 이 함수는 시계열, 학습 크기 및 샘플 수를 사용합니다. 이 버전은 다른 예측 알고리즘과 비교할 수 있기 때문에 comparable로 이름 지었습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
def split_sequences_comparable(sequences, n_samples, train_size):
 # 단계
 n_steps_out = len(sequences) - train_size
 n_steps_in = train_size - n_steps_out - n_samples + 1
 # 끝 세트
 X_test = sequences[n_samples + n_steps_out - 1:train_size]
 X_forecast = sequences[-n_steps_in:]
 X, y = list(), list()
 for i in range(n_samples):
     # 이 패턴의 끝을 찾습니다
     end_ix = i + n_steps_in
     out_end_ix = end_ix + n_steps_out
     # 패턴의 입력 및 출력 부분을 수집합니다
     seq_x, seq_y = sequences[i:end_ix], sequences[end_ix:out_end_ix]
     X.append(seq_x)
     y.append(seq_y)
 return np.array(X), np.array(y), np.array(X_test), np.array(X_forecast), n_steps_in, n_steps_out
```

이 함수에서는 단계 수가 이미 고정되었기 때문에 샘플 수와 훈련 크기는 사용자가 선택하도록 하고, 최대 가능한 단계 수를 계산하도록 결정했습니다. 아래는 실행된 코드와 그 결과입니다:

```js
n_samples = 12
train_size = 321
X_train, y_train, X_test, X_forecast, n_steps_in, n_steps_out = split_sequences_comparable(ts_data, n_samples, train_size)
y_test = ts_data[train_size:]

# Reshaping
X_train = X_train.reshape(X_train.shape[0], X_train.shape[1], 1)
X_test = X_test.reshape(X_test.shape[1], X_test.shape[0], 1)
y_train = y_train.reshape(y_train.shape[0], y_train.shape[1])
y_test = y_test.reshape(y_test.shape[1], y_test.shape[0], 1)

# LSTM 모델 생성 및 훈련
model = Sequential()
model.add(LSTM(units=154, activation='tanh', input_shape=(n_steps_in, 1)))
model.add(Dense(units=n_steps_out))
model.compile(loss='mean_squared_error', optimizer='Adam', metrics=['mape'])

model.fit(x=X_train, y=y_train, epochs=500, batch_size=18, verbose=2)

# 예측
lstm_predictions = model.predict(X_test)
predictions = lstm_predictions.reshape(lstm_predictions.shape[1])
lstm_fitted = model.predict(X_train)
fits = [np.diag(np.fliplr(lstm_fitted), i).mean() for i in range(lstm_fitted.shape[1]+n_steps_in - 1, -lstm_fitted.shape[0], -1)]

# 메트릭스
mape = mean_absolute_percentage_error(y_test, predictions)
r2 = r2_score(ts_data[n_steps_in:train_size], fits[n_steps_in:])

# 실제, 적합 및 예측 플롯
plt.figure(figsize=(10, 6))
plt.plot(date_range, ts_data, label='Actual', color='blue')
plt.plot(date_range[:train_size], fits, label='Fitted', color='green')
plt.plot(date_range[train_size:], predictions, label='Forecast', color='red')
plt.title('FSC - Short - Passengers\n12 Sample Comparable LSTM Forecast')
plt.xlabel('Date')
plt.ylabel('Passengers')
plt.legend()
plt.text(0.05, 0.05, f'R2 = {r2*100:.2f}%\nMAPE = {mape*100:.2f}%\', transform=plt.gca().transAxes, fontsize=12)
plt.grid(True)
plt.show()
```

<img src="/assets/img/2024-05-18-HowtofixacommonmistakeinLSTMtimeseriesforecasting_10.png" />

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

지금까지 우리가 얻은 결과는 가장 신뢰할만한 것입니다. 그러나 제가 개발한 혁신적인 방법을 사용하면 더 나은 결과를 얻을 수 있습니다. 이 방법은 나중에 시리즈에서 (순환 방법) 자세히 설명하겠습니다. 먼저 LSTM 네트워크의 하이퍼파라미터를 조정하는 방법에 대해 설명하겠습니다.

LSTM은 모든 시간 단계를 특성으로 집계하기 때문에 시계열 데이터가 모든 이러한 방법에서 손실됩니다. 나중에 시리즈에서 (인코더/디코더 방법) 시계열 입력의 구조를 유지하는 다른 방법을 사용할 것입니다.

계속 주목해 주세요!
