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