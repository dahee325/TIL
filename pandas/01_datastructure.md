# 01_datastructure
## Series
- [pandas](https://pandas.pydata.org/docs/reference/index.html)
에서 사용하는 1차원 배열
- `pd.Series()` : 시리즈 정의
    - 위에서 아래로 데이터를 쌓음
    - 기본적으로 인덱스 접근을 할 수 있게 만들어줌(음수 인덱싱은 불가능)
    - 시리즈의 형태(전체 데이터)는 넓은 범위의 데이터 형태로 자동으로 형변환
```python
import pandas as pd
import numpy as np
```
```python
arr = np.arange(100, 110)
s = pd.Series(arr)
s = pd.Series(arr, dtype='float32') # 형을 int64에서 float32로 변환
s = pd.Series(['kim', 'lee', 'park']) # pandas에서 stsring은 object라고 부름
s = pd.Series([1, 2, 3, '1', '2', 'hello']) # 데이터는 각각 int, object, 전체 데이터는 object
```
- `pd.Series()`의 옵션 `index=[]` : 인덱스 이름 설정
```python
names = pd.Series(['kim', 'lee', 'park'], index=['a', 'b', 'c'])
```
- `iloc[]`(indexlocation) : 인덱스를 설정했지만 숫자로 접근할 때 사용하는 함수
```python
names[0] # 인덱스를 a,b,c로 지정했지만 0으로 불러와서 출력은 되지만 오류가 생김
names.iloc[0] # 오류를 해결하기 위해서 .iloc[]사용
```
- `index` : 시리즈의 index 정보 확인, 인덱스 이름을 설정한 경우 인덱스 이름 확인
- `values` : 시리즈의 값만 따로 배열로 확인
- `dtypes` : 시리즈의 데이터형 확인
- `ndim` : 시리즈의 차원 확인
- `shape` : 시리즈의 모양(몇 행, 몇 열)을 튜플로 확인
```python
s.index # => Index(['a', 'b', 'c'], dtype='object')
s.values # => array(['kim', 'lee', 'park'], dtype=object)
s.dtypes
s.ndim # => 1
s.shape # => (3,)
```

### NaN(Not a Number)
- `np.nan` : float타입으로 값이 없음을 의미

### fancy indexing(팬시 인덱싱)
```python
f = ['banana', 'apple', 'grape', np.nan]
s = pd.Series(f, index=list('abcd'))
print(s)
s[['d', 'a']] # 인덱스 이름으로 접근
s[[3, 1]] # 오류
s.iloc[[3, 1]] # 인덱스 번호로 접근
```

### boolean indexing(불리언 인덱싱)
- `[cond]` 에서 cond가가 True인 값만 반환
```python
s[[True, False, True, False]]
# => a    banana
#    c     grape
#    dtype: object
s == 'banana' # s안의 값이 'banana'면 True
s[s == 'banana']

s = pd.Series([1, 2, 3, 4, 5, 6])
s[s > 3] # 3보다 큰 값만 반환
```

### 결측치(NaN) 처리
- `isnull()`, `isna()` : 값이 NaN이면 True
- `notnull()`, `notna()` : 값이 NaN가 아니면 True
```python
s = pd.Series([1, 3, np.nan, 10, 11, np.nan])
s[s.isna()] # NaN값만 출력
s[s.notnull()] # NaN이 아닌 값만 출력
```

### slicing
- `[start:stop:step]`
- 인덱스 이름으로도 슬라이싱 가능
```python
s = pd.Series([1, 2, 3], index=list('abc'))
s[1:2]
s['a':'b']
```

## Dataframe(데이터프레임)
- `pd.DataFrame()` : 데이터프레임 정의
    - 2차원 데이터 구조(excel, sheet와 유사)
    - 행(row), 열(column)의 구조
```python
d = pd.DataFrame([
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
], columns=['가', '나', '다']) # 컬럼의 이름 설정
```
```python
info = {
    'name': ['kim', 'lee', 'park'],
    'age': [10, 20, 30]
}
df = pd.DataFrame(info) # 데이터의 길이가 맞지 않으면 맞지 않은 데이터 부분은 날아감
```
- `index` : 데이터프레임의 index 정보 확인
- `values` : 데이터프레임의 값만 따로 배열로 확인
- `dtypes` : 데이터프레임의 데이터형 확인
- `T` : 데이터프레임의 행과 열을 바꿈

### index 지정
- `rename(index = {'old_nm': 'new_nm'})`
    - 원본데이터는 저장하지 않으므로 `inplace = True`옵션을 사용하면 원본도 변환(옵션을 사용하지 않으면 재할당 해야함)
```python
df.index = list('abc') # 숫자로 하는게 일반적
```

### column 다루기
- `columns` : 컬럼의 이름 확인
- `rename(columns = {'old_nm': 'new_nm'})` : 컬럼 이름 바꾸기
    - 원본데이터는 저장하지 않으므로 `inplace = True`옵션을 사용하면 원본도 변환(옵션을 사용하지 않으면 재할당 해야함)
```python
df.columns # => Index(['name', 'age'], dtype='object')
print(df['name']) # 하나의 열을 출력하면 그 결과의 유형은 Series
df[['age', 'name']] # column의 fancy indexing
df = df.rename(columns={'name': '이름'}) # 재할당
# df.rename(columns={'name': '이름'}, inplace = True)
```