# 1-3
```python
import pandas as pd

df = pd.read_csv('data/Fish.csv')
```
## 학습시킬 데이터 만들기
- `tolist()` : 시리즈로 되어있던 형태를 리스트로 바꿔줌
```python
bream_df = df.loc[df['Species'] == 'Bream']

bream_length = bream_df['Length2'].tolist()
bream_weight = bream_df['Weight'].tolist()
```

### matplotlib.pyplot
- `matplotlib` : 그래프 도구를 제공하는 라이브러리
- `scatter()` : 산점도
- `bar()` : 막대
- `plot()` : 선
```python
import matplotlib.pyplot as plt

plt.scatter(bream_length, bream_weight)
```
```python
smelt_df = df.loc[df['Species'] == 'Smelt']

smelt_length = smelt_df['Length2'].tolist()
smelt_weight = smelt_df['Weight'].tolist()


plt.scatter(bream_length, bream_weight)
plt.scatter(smelt_length, smelt_weight)

length = bream_length + smelt_length
weight = bream_weight + smelt_weight

fish_data = [[l, w] for l, w in zip(length, weight)]
```

## 정답지 만들기
```python
fish_target = [1] * len(bream_weight) + [0] * len(smelt_weight)
```

### sklearn.neighbors
- `KNeighborsClassifier()` : K-최근접 이웃 분류 정의
- `fit()` : 모델을 학습시키는 함수
- `score()` : 학습결과를 점수화 시키는 함수
- `predict()` : 학습한 데이터를 기반으로 예측하는 함수
```python
from sklearn.neighbors import KNeighborsClassifier
kn = KNeighborsClassifier() # 인스턴스화
kn.fit(fish_data, fish_target)
kn.score(fish_data, fish_target)  
kn.predict([[30, 600]]) # 2차원이라 리스트를 두번 감싸야함
```
```python
kn. predict([[10, 20]]) # 빙어로 나옴
kn49 = KNeighborsClassifier(n_neighbors=49)
kn49.fit(fish_data, fish_target)
kn49.score(fish_data, fish_target) 
```
- `score()` 결과가 35/49와 같은 값으로 도미만 다 맞추고 빙어는 맞추지 못함
    - => 전체 데이터를 다 바라보는 것은 좋은 방법이 아님

# 2-1
## K-NN K-최근접 이웃
```python
import pandas as pd
df = pd.read_csv('data/Fish.csv')

cond = df['Species'].isin(['Bream', 'Smelt'])
lw_df = df.loc[cond, ['Length2', 'Weight']]
fish_data = lw_df.values.tolist()

fish_target = [1] * 35 + [0] * 14

from sklearn.neighbors import KNeighborsClassifier
kn = KNeighborsClassifier()

train_input = fish_data[:35] # 학습할 때 쓰는 데이터
train_target = fish_target[:35] # 학습할 때 쓰는 정답 데이터
test_input = fish_data[35:] # 테스트할 때 쓰는 데이터
test_target = fish_target[35:] # 테스트할 때 쓰는 정답 데이터

kn.score(test_input, test_target) # => 0점 나옴
```
## 랜덤으로 train data, test data 구분하기
```python
import numpy as np
input_arr = np.array(fish_data)
target_arr = np.array(fish_target)

np.random.seed(42) # 랜덤번호 고정
index = np.arange(49)
np.random.shuffle(index)
print(index)

input_arr[[1, 3]] # fancy indexing

train_input = input_arr[index[:35]]
train_target = target_arr[index[:35]]
test_input = input_arr[index[35:]]
test_target = target_arr[index[35:]]

import matplotlib.pyplot as plt
plt.scatter(train_input[:, 0], train_input[:, 1])
plt.scatter(test_input[:, 0], test_input[:, 1]) # train데이터와 test데이터가 골고루 분리됨

kn.fit(train_input, train_target)
kn.score(test_input, test_target)
kn.predict(test_input)
```

# 2-2
- `train_test_split()` : 데이터를 무작위로 섞고 자르기까기 실행하는 함수
    - 기본적으로 training data 75%, testing data 25%
```python
import pandas as pd
df = pd.read_csv('data/Fish.csv')

cond = df['Species'].isin(['Bream', 'Smelt'])
fish_data = df.loc[cond, ['Length2', 'Weight']]
fish_target = df.loc[cond, ['Species']]

from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(fish_data, fish_target)
# fish_data와 fish_target데이터를 각각 train 데이터의 데이터와 결과값, test 데이터의 데이터와 결과값 총 4개의 데이터로 나눔

from sklearn.neighbors import KNeighborsClassifier
kn = KNeighborsClassifier()

kn.fit(train_input, train_target['Species'])
kn.score(test_input, test_target['Species'])
kn.predict([[25, 150]])
```

## 표본점수
- 표본점수 = (원본데이터 - 평균) / 표준편차
```python
mean = train_input.mean()
std = train_input.std()

train_scaled = (train_input - mean) /  std
test_scaled = (test_input - mean) / std

plt.scatter(train_scaled['Length2'], train_scaled['Weight'])
kn.fit(train_scaled, train_target['Species'])
kn.score(test_scaled, test_target['Species'])
```

# 3-1
## K-최근접 이웃 회귀
-  ex) 샘플의 타깃값 : 100, 80, 60 => 샘플의 예측 타깃값 : (100 + 80 + 60) / 3 = 80

### 데이터 불러오기
```python
import pandas as pd
df = pd.read_csv('data/Fish.csv')
```

### 농어만 분류하기
```python
cond = df['Species'] == 'Perch'
perch_data = df.loc[cond]
```

### 농어의 길이와 농어의 무게 관계 그래프로 확인하기
```python
import matplotlib.ptplot as plt
plt.scatter(perch_data['Length2'], perch_data['Weight'])
```

### 훈련 데이터와 테스트 데이터 분리하기
```python
from sklearn.model_selection import train_test_split
train_input, test_input, train_target, test_target = train_test_split(perch_data[[]'Length2']], perch_data[[]'Weight']]) # 2차원 데이터를 넣어야함
```

### K-최근접 회귀 모델
```python
from sklearn.neighbors import KNeighborsRegressor
knr = KNeighborsRegressor()
knr.fit(train_input, train_target)
knr.score(test_input, test_target) # 결정계수, 정확도와 같음

knr.predict([[60]]) 
# 길이가 50이건 60이건 100이건 같은 무게가 나옴 
# 값이 커지면 똑같은 이웃을 바라보기 때문에 더 큰 값의 길이를 입력해도 같은 무게가 나옴
# => 3-2. 선형회귀
```
### mean_absolute_error : 타깃과 예측의 절댓값 오차 평균 반환
```python
from sklearn.metrics import mean_absolute_error
# 테스트 세트에 대한 예측 만들기
test_prediction = knr.predict(test_input)
# 테스트 세트에 대한 평균 절댓값 오차 계산
mae = mean_absolute_error(test_target, test_prediction)
knr.score(train_input, train_target)
```

### 과대적합 & 과소적합
- 과대적합 : 훈련 세트에서 점수가 좋았는데 테스트 세트에서 점수가 안좋을 경우
- 과소적합 : 훈련 세트보다 테스트 세트의 점수가 높거나 두 점수가 모두 낮은 경우
- `knr.n_neighbors=` : 이웃의 개수 설정
```python
knr.n_neighbors = 3
knr.fit(train_input, train_target)
print(knr.score(train_input, train_target))
print(knr.score(test_input, test_target))
```
- 이웃의 개수가 각각 [1, 3, 5, 40]일 때 비교
```python
ipmort numpy as np
knr = KNeighborsRegressor()

x = np.arange(5, 45).reshape(-1, 1) # => (나머지 원소 개수, 1개)

for n in [1, 3, 5, 40]:
    knr.n_neighbors = n
    knr.fit(train_input, train_target)

    prediction = knr.predict(x)

    plt.scatter(train_input, train_target)
    plt.plot(x, prediction)
    plt.show()
```

# 3-2
## 선형 회귀
- y = ax + b

### 데이터 불러오기
```python
import pandas as pd
df = pd.read_csv('data/Fish.csv')
```

### 농어의 길이와 무게 분류
```python
perch_df = df.loc[df['Species'] == 'Perch']
perch_length = perch_df[['Length2']]
perch_weight = perch_df[['Weight']]
```

### 훈련 데이터와 테스트 데이터 분리하기
```python
from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(perch_length, perch_weight)
```

### 선형 회귀 모델
```python
from sklearn.linear_model import LinearRegression

lr = LinearRegression()

lr.fit(train_input, train_target)
lr.score(test_input, test_target)

lr.predict([[100]]) # K-최근접의 문제 해결
```
- `lr.coef_` : a, 계수 or 가중치
- `lr.intercept_` : b, y절편
```python
print(lr.coef_)
print(lr.intercept_)

import matplotlib.pyplot as plt

plt.scatter(train_input, train_target)
plt.plot([15, 50], [15 * 34 -596, 50 * 34 -596])
# plt.olit(시작점, 끝점=시작점을 y = ax + b에 대입한 점)

print(lr.score(train_input, train_target))
print(lr.score(test_input, test_target))
```

## 다항 회귀
- y = ax^2 + bx + c 
- 선형회귀로 표현할 수 없을 때 사용
- 하나의 특성을 사용
```python
train_input['Length2_poly'] = train_input['Length2'] ** 2
test_input['Length2_poly'] = test_input['Length2'] ** 2

lr = LinearRegression()

lr.fit(train_input, train_target)
lr.score(test_input, test_target)

lr.predict([[15, 4]]) # 2차곡선으로 그래프가 바뀌니까 유효한 데이터가 나옴

print(lr.coef_, lr.intercept_) # => y = 0.95x^2 + (-20.33)x + 117.06
```

# 3-3
## 다중 회귀
- ax + by + cz + d = 0
- 서로 다른 특성을 여러개 사용, 평면 학습

### 데이터 불러오기
```python
import pandas as pd

df = pd.read_csv('data/Fish.csv')
```

### 농어의 길이, 높이, 두께와 농어의 무게 분류
```python
perch_df = df.loc[df['Species'] == 'Perch']

perch_full = perch_df[['Length2', 'Height', 'Width']]
perch_weight = perch_df[['Weight']]
```

### 훈련 데이터와 테스트 데이터 분리하기
```python
from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(perch_full, perch_weight) 
# 책처럼 정확히 하려면 `random_state = 42`를 옵션으로 넣어주면 됨
```

### PolynomialFeatures()
- 주어진 특성으로 만들 수 있는 조합을 만들어줌
- `include_bias=False` : 1을 뺴고 변환하는 옵션(선형 방정식의 절편을 항상 값이 1인 특성과 곱해지는 계수여서 1도 나옴)
- `fit()` : 특성 조합을 찾는 함수 => 학습함수 아님
- `transform()` : 실제로 데이터를 변환하는 함수
```python
from sklearn.preprocessing import PolynomialFeatures

poly = PolynomialFeatures(include_bias=False)
poly.fit([[3, 5]])
poly.transform([[3, 5]])
```
```python
poly = PolynomialFeatures(include_bias=False)
poly.fit(train_input)
train_poly = poly.transform(train_input)
train_poly.shape # => (42, 9)
```

- `get_feature_names_out()` : 어떤 조합을 만들었는지 알려주는 함수
```python
poly.get_feature_names_out()
```

### 다중 회귀 모델
```python
from sklearn.linear_model import LinearRegression

lr = LinearRegression()
lr.fit(train_poly, train_target)
lr.score(train_poly, train_target)

test_poly = poly.transform(test_input)

lr.score(test_poly, test_target)
```

### 특성 공학
- 기존의 특성을 사용하여 새로운 특성을 뽑아냄\
ex) 농어의 길이 * 농어의 높이
- `degree=` : 몇제곱까지 만들지 결정하는 `PolynomialFeatures()` 함수 옵션
```python
poly = PolynomialFeatures(degree=5, include_bias=False) # 5제곱까지 만들어줘 ex) 높이^5
poly.fit(train_input)
train_poly = poly.transform(train_input)
test_poly = poly.transform(test_input)
train_poly.shape # => (42, 55)

lr.fit(train_poly, train_target)
print(lr.score(train_poly, train_target))
print(lr.score(test_poly, test_target))
```

## 규제
- 훈련 세트를 너무 과도하게 학습하지 못하도록 훼방하는 것 => 과대적합이 되지 않도록 만드는 것
- 선형 회귀모델의 경우 특성에 곱해지는 계수(기울기)의 크기를 작게 만드는 일

### StandardScaler (정규화)
```python
from sklearn.preprocessing import StandardScaler

ss = StandardScaler()
ss.fit(train_poly)

train_scaled = ss.transform(train_poly)
test_scaled = ss.transform(test_poly)

# train_poly[:5]
```

### 릿지(Ridge)
- 계수를 제곱한 값을 기준으로 규제 적용, L2규제
```python
from sklearn.linear_model import Ridge

ridge = Ridge(alpha=0.1)
ridge.fit(train_scaled, train_target)

print(ridge.score(train_scaled, train_target))
print(ridge.score(test_scaled, test_target))
```

### 라쏘(Lasso)
- 라쏘 : 계수의 절댓값을 기준으로 규제 적용, L1규제
- 0을 만들 수 있음
```python
from sklearn.linear_model import Lasso

lasso = Lasso()
lasso.fit(train_scaled, train_target)

print(lasso.score(train_scaled, train_target))
print(lasso.score(test_scaled, test_target))

lasso.coef_
```