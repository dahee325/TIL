# 05_missing_value
```python
import pandas as pd
import seaborn as sns

df = sns.load_dataset('titanic')
```

## copy()
- `copy()` : 데이터프레임 복제하기
```python
df_copy = df.copy()
id(df_copy), id(df) # id를 확인하면 주소?가 다름
```

## 결측치
1. 결측 데이터 확인
2. 결측치가 아닌 데이터 확인
3. 결측치 채우기 or 결측 데이터 제거하기
```python
df_copy = df.copy()
df_copy.isnull().sum() # 각 컬럼들의 결측치(True)를 합산하여 개수 확인
df_copy.notna().sum() # 결측치가 아닌 값의 개수
```

### 결측 데이터 필터링
```python
cond = df_copy['age'].isnull() # 결측치를 가진 사람들의 목록
df_copy.loc[cond, 'age'] = 30
df_copy.tail()
```

## fillna()
- `fillna()` : 결측치를 채우는 함수
- 수정 결과를 저장하지 않고 결과만 출력, 재할당 해야됨
```python
df_copy['age'] = df_copy['age'].fillna(100)
```
- 카테고리 데이터는 지정하지 않은 데이터를 추가로 넣을 수 없음
    - 카테고리에 먼저 추가한 후 결측치를 수정해야함
```python
df_copy['deck'] = df_copy['deck'].cat.add_categories('Z') # 카테고리에 'Z'추가
df_copy['deck'].fillna('Z')
```
```python
age_mean = df_copy['age'].mean()
df_copy['age'] = df_copy['age'].fillna(age_mean) # 결측치를 평균값으로 바꿈
```

## dropna()
- `dropna()` : 하나라도 결측값을 갖고 있으면 제거
```python
df_copy.dropna()
df_copy.dropna(how='all') # 한 행의 전체 데이터가 결측값이면 제거
```