# 04_statistics
```python
import pandas as pd
import seaborn as sns

df = sns.load_dataset('titanic')
```

## describe()
- `describe()` : 통계량 요약, 숫자만 가능
    - `include='object'`옵션을 지정하면 문자형인 통계량 요약
```python
df.describe()
df.describe(include='object')
```

## count()
- `count()` : 데이터의 개수, Null값은 제외
```python
df.count()
df['age'].count() # 원하는 컬럼에 직접 적용 가능
```

## mean()
- `mean()` : 데이터의 평균 계산
    - `numeric_only=True` 옵션을 지정하면 평균을 계산할 수 없는 데이터를 제외한 숫자형 데이터만 평균 계산
    - `skipna=False` 옵션을 지정하면 NaN이 속해있으면 그 데이터는 계산하지 않고 넘어감
```python
df['age'].mean() # 나이 평균 계산
df.mean() # 평균을 계산할 수 없는 데이터가 포함되어있기 때문에 오류
df.mean(numeric_only=True)
df.mean(numeric_only=True, skipna=False)
```

## median()
- `median()` : 데이터의 중앙값 출력
- 이상값이 너무 클 떄 사용
- 비율적으로 가운데에 있는 데이터에 접근
- 데이터의 개수가 짝수개면 중간 두개의 데이터의 평균 출력
```python
df['age'].median()
```

## sum()
- `sum()` : 데이터의 총합 계산
    - `numeric_only=True` 옵션을 지정하면 평균을 계산할 수 없는 데이터를 제외한 숫자형 데이터만 평균 계산
- `cumsum()` : 누적합 출력
```python
df.sum(numeric_only=True)
df['fare'].cumsum() # 누적합
df['age'].cumprod() # 누적곱
```

## 표본분산 var()
- `var()` : 편차 제곱의 평균, 데이터가 얼만큼 떨어져있는지
```python
fare_mean = df['fare'].mean()

total = ((df['fare'] - fare_mean) ** 2).sum() # 편차제곱의 합
total_count = df['fare'].count() - 1 # 모분산은 n으로 나누고 표본분산은 n-1을 나눔

my_var = total / total_count
```
```python
df['fare'].var()
```

## 표준편차 std()
```python
import numpy as np
np.sqrt(df['fare'].var())

df['fare'].std()
```

## min(), max()
- `min()` : 데이터의 최솟값 출력
- `max()` : 데이터의 최댓값 출력
```python
df['age'].min()
df['age'].max()
```

## agg() (aggregation)
- `agg()` : 컬럼을 선택하고 계산하고 싶은 함수가 여러개일 떄 사용
```python
df['age'].agg(['max', 'min', 'count', 'mean'])
df[['age', 'fare']].agg(['min', 'max']) # 대괄호를 한번 더 하면 데이터프레임으로 출력
```

## quantile()
- `quantile()` : 분위수 계산
```python
df['age'].quantile(0.1) # 나이기준 하위 10%
df['age'].quantile(0.8) # 나이기준 상위 20%
df['age'].quantile(0.5) # 중앙값과 같은 코드
```

## unique()
- `unique()` : 컬럼의 고유값(문자형인 데이터 값의 종류)
- `nunique()` : 컬럼별 고유값의 개수(문자형인 데이터 값 종류의 개수)
    - `dropna=False` 옵션을 지정하면 결측값도 하나의 값으로 카운트
```python
df['who'].unique()
df['who'].nunique()
```

## mode()
- `mode()` : 데이터의 최빈값 출력
```python
df['deck'].mode()
```

## corr()
- `corr()` : 상관계수, 각 컬럼끼리의 상관관계를 -1 ~ 1 사이로 표현
```python
df.corr(numeric_only=True)
df.corr(numeric_only=True)['survived'] # 'survived'와 다른 컬럼 사이의 상관계수
```