# 00_numpy
- [Numpy](https://numpy.org/doc/stable/reference/index.html) 불러오기
```python
import numpy as np
```
- 0차원 : 스칼라(scala), 1차원 : 벡터(vector), 2차원 : 행렬(matrix), 3차원 이상 : 텐서(tenser)
- `%timeit` : 코드를 실행하는데 시간이 얼마나 걸리는지 측정

 ## ndarray 생성
- `np.arange()` : `range()`함수와 동일,\
 `range()`함수는 정수단위만 지원하지만 `np.arange()`함수는 실수단위도 표현가능
 ```python
np.arange(10) # 0부터 9까지 순서가 있는 배열을 만들어줌
 ```
 - `np.array()` : 리스트나 튜플 등 iterable한 객체를 numpy 배열로 변환
```python
data = [[1, 2, 3], [4, 5, 6]]
arr = np.array(data)
type(arr) # numpy.ndarray유형
```
- `ndim` : 배열의 차원 수를 알려줌, arr은 2차원
- `shape` : 배열이 몇 행 몇 열인지 튜플 형식으로 반환
- `zeros(n)` : 데이터는 모르지만 배열의 규격을 정함, 0이 n개인 배열을 반환
```python
np.zeros((5, 5)) # np.zeros 안에 튜플을 넣으면 5행 5열인 배열 반환
```
- `empty()` : `np.zeros`와 같다고 생각

## ndarray 자료형
- `dtype` : 배열의 타입을 알려줌
- `array()`함수에 옵션`dtype=np.float64`을 넣으면 float64로 형변환
```python
np.array([1, 2, 3], dtype=np.float64)
```
- `astype()` : 형변환 함수, 소수를 정수로 형변환할 때 소수점은 버림처리
    - int32, int64, object, float64 등
```python
arr = np.array([1, 2, 3])
arr.astype(np.int64)
```

## 산술 연산
```python
arr = np.array([[1, 2, 3], [4, 5, 6]])

print(arr + arr)
print(arr -1)
print(arr * arr) # 같은 위치에 있는 값끼리 곱
print(1 / arr)
print(arr ** 3) # 모든 데이터들을 세제곱 
```
```python
arr1 = np.array([[1, 2, 3], [4, 5, 6]])
arr2 = np.array([[3, 2, 1], [1, 2, 3]])

print(arr1 > arr2) # 같은 위치에 있는 데이터끼리 비교
print(arr == 3) # 데이터가 3인 데이터를 찾음
```

## 색인(indexing)과 슬라이싱(slicing)
- indexing
```python
arr = np.arange(10)
print(arr[7]) # => 7
print(arr[2:5]) # 2번째 데이터부터 4번쨰 데이터까지 출력
arr[2:5] = 10 # 2번째부터 4번째까지 데이터의 값을 10으로 변경, 원본도 변경
```
- `py_list[2:3]`의 결과는 [3]이라는 새로운 리스트로 py_list와 별개
- 원본을 복제해서 사용했기 때문에 원본은 바뀌지 않음
```python
py_list = [1, 2, 3, 4, 5]
py_list[2:3].pop()
```
- 1번째 리스트의 요소 중 1번째 요소를 가져오기 위해서는 중첩 인덱싱으로 접근
```python
arr = np.array([[1, 2, 3], [4, 5, 6]])
print(arr[1][1]) # 중첩 인덱싱
print(arr[1, 1]) # 위와 같음
```
```python
arr3d = np.array([[[1, 2, 3], [4, 5, 6], [7, 8, 9]],
                  [[11, 12, 13], [14, 15, 16], [17, 18, 19]],
                  [[11, 22, 33], [44, 55, 66], [77, 88, 99]]])
print(arr3d[2][0][1])
print(arr3d[2, 0, 1])
```
- slicing : `[start:stop:step]`
    - `[:]` : 전체 추출
    - `[start:]` : start 인덱스부터 끝까지 추출
    - `[:stop]` : 처음부터 stop 인덱스 전까지 추출
    - `[start:stop:step]` : start 인덱스부터 stop 인덱스 전까지 인덱스 번호 step칸씩 건너뛰어 추출
```python
print(arr3d[:1, 1:, 2:])
print(arr3d[0, 1:, 2:]) # 인덱싱 접근을 하면 차원 안으로 들어가기 때문에 차원을 유지하지 않고 차원을 줄여줌
```

## 불리언 값으로 선택
- 불리언(boolean) : 참(True)과 거짓(False) 중 하나의 값을 가짐
- 전체 데이터 중 내가 취하고 싶은(특정 조건에 맞는) 데이터를 고를 때 사용
```python
names = np.array(['hong', 'kim', 'hong', 'kim'])
names = np.array(['hong', 'kim', 'hong', 'kim'])
data = np.array([['math', 60], ['math', 90], ['eng', 70], ['eng', 50]])
name == 'hong' 
data[names == 'hong']
```
- `name == 'hong'`의 결과는 `array([ True, False,  True, False])`로 names가 hong이면 True, 아니면 False를 반환
- `data[names == 'hong']`의 결과는 `array([['math', '60'], ['eng', '70']], dtype='<U21')`로 트루 위치의 값만 출력
```python
print(data[names == 'hong', 1]) # 인덱스 접근
print(data[names == 'hong', :1]) # 슬라이싱
print(names != 'hong') # != 다른가요?
print(~(names == 'hong')) # 계산결과를 역 취해줌
```

## 팬시 색인(fancy indexing)
- [](인덱싱)안에 [](리스트)를 넣으면 원하는 행을 직접적으로 선택하여 출력, 순서도 지정한대로 출력
```python
arr = np.zeros((8, 4)) # 8*4
for i in range(8):
    arr[i] = i
arr[[4, 3, 0, 5]] # 4, 3, 0, 5번째 행만 출력
```
- `reshape()` : 행렬의 행과 열을 변경할 때 사용
```python 
np.arange(32).reshape(8, 4)
```

## 배열 전치
- `T` : 대각선을 기준으로 대칭되어 행과 열이 바뀜
- `@`, `np.dat` : 행렬의 내적, 행렬의 곱
```python
arr = np.arange(15).reshape(3, 5)
print(arr.T)
print(arr @ arr.T)
print(np.dot(arr, arr.T))
```

## numpy 함수
- `np.random.standard_normal()` : 랜덤하게 배열을 만들 때 사용
    - 옵션 `size=()`로 행렬의 사이즈 설정 가능
- `np.random.randn()` : 랜덤하게 배열 만들 때 사용
```python
samples = np.random.standard_normal(size=(3, 3))
samples = np.random.randn(3, 3) # 위와 같은 코드
print(samples)
```
- `np.sqrt()` : 루트, 1/2 제곱
- `np.abs()` : 절대값
- `nan`(not a number) : 숫자가 아님, none과 비슷
- `np.isnan()` : 배열 안에 nan인 값이 있나요? => 있으면 True, 없으면 False 반환
```python
arr = np.sqrt(samples)
arr2 = np.abs(samples)
np.isnan(arr)
```


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

# 02_file_load_save
```python
import numpy as np
import pandas as pd
```
## EXCEL
- `pd.read_excel('파일경로')` : 엑셀파일 불러오기
    - 시트가 여러개일 경우 `sheet_name=''`옵션을 사용해서 엑셀파일 불러오기
    - `sheet_name=None`으로 입력하면 sheet_name을 key값으로한 딕셔너리로 모든 엑셀파일 내용 출력
- `to_excel('파일 저장 위치')` : 엑셀파일로 저장하기
    - `index=False`를 옵션으로 지정하면 인덱스 번호 빠지고 저장
```python
excel = pd.read_excel('data/DAMF2.xlsx')
excel[['이름', 'github']] # 열이름이 '이름', 'gihub'인 데이터만 출력
excel = pd.read_excel('data/DAMF2.xlsx', sheet_name='menu') # 엑셀 파일의 시트 이름이 menu인 파일 불러오기

menu_df = excel[['가게이름', '메뉴']] # 열이름이 '가게이름', '메뉴'인 데이터만 출력
menu_df.to_excel('data/sample.xlsx', index=False)
```

## CSV
- csv파일 : 콤마로 구분된 데이터 파일
- `pd.read_csv('파일경로')` : csv파일 불러오기
- `to_csv('파일 저장 위치')` : csv파일로 저장하기
    - `index=False`를 옵션으로 지정하면 인덱스 번호 빠지고 저장
```python
df = pd.read_csv('data/DAMF2.csv')
df2 = df[['이름', 'email']] # 열이름이 '이름', 'email'인 데이터만 출력
df2.to_csv('data/sample.csv')
```

# 03_query
- `sns.load_dataset('데이터셋이름')` : 데이터셋 불러오기기
```python
import seaborn as sns
df = sns.load_dataset('titanic')
```

## head(), tail()
- `head()` : 파일의 처음 n개 행 출력, 디폴트는 5
- `tail()` : 파일의 마지막 n개 행 출력, 디폴트는 5
```python
df.head()
df.tail(3) # 마지막 3개 행 출력
```

## info()
- info() : 데이터프레임의 중요한 정보를 요약해서 보여줌
    - 열 이름 / non-null이 아닌 값(데이터가 잘 들어가있는 수) 수 / 데이터타입
    - dtype이 category는 문자나 정수를 입력하지만 그 값이 한정적인 데이터, 데이터의 크기가 작을 때 주로 사용
```python
df.info()
```

## value_counts()
- `value_counts()` : 해당 시리즈 안에 들어있는 값의 개수 출력력
```python
print(type(df['who'])) # 데이터프레임의 하나의 컬럼은 시리즈
df['who'].value_counts()
df['pclass'].value_counts()
```

## Attribute
- `ndim` : 데이터 프레임이 몇 차원인지 확인
- `shape` : 데이터의 개수(행, 열) 확인
- `index` : 인덱스 정보 확인
- `columns` : 열만 출력
- `values` : 값만 출력
```python
df.nim # => 2
df.shape # => (891, 15)
df.index # => RangeIndex(start=0, stop=891, step=1)
df.columns
df.values
```

## 타입 변환(astype())
```python
df['pclass'].astype('int32') # 'pclass' 유형을 'int32'로 변환
df['pclass'].astype('str') # 문자형은 `dtype`으로 유형을 확인하면 'object'지만 변환할 때 함수에는 'str'입력
```

## 정렬
### sort_index()
- 인덱스를 기준으로 정렬
- 디폴트는 오름차순, `ascending=False` 옵션을 입력하면 내림차순으로 정렬
```python
df.sort_index(ascending=False)
```

### sort_values()
- 설정한 값순으로 정렬
```python
df.sort_values('age') # 나이순으로 오름차순 정렬
df.sort_values('class') # 문자는 알파벳 순으로 정렬
df.sort_values(['fare', 'age'], ascending=[False, True])
# 요금을 기준으로 정렬한 후 나이를 기준으로 정렬
# 두개 이상의 컬럼을 정렬할 때 ascending을 사용하면 리스트를 사용해서 한개의 컬럼씩 True, False를 지정해줘야함
# 'fare'는 내림차순, 'age'는 오름차순으로 정렬
```

### indexing, slicing, 조건 필터링
- 인덱스 접근을 하면 그 출력값은 데이터 프레임이 아님
```python
df.loc[3] # location, 3번 인덱스에 있는 데이터 출력
df.loc[3, ['class', 'age']] # 3번 인덱스에 있는 데이터 중 class, age 출력
df[['class', 'age']] # 컬럼을 하나만 출력할 때 결과의 타입이 시리즈지만, 대괄호를 하나 더 하면 데이터프레임으로 출력
```
- 데이터 프레임의 슬라이싱은 마지막 데이터를 포함
```python
df.loc[2:5] # 2번째부터 5번째 데이터 출력, 마지막 데이터 포함
df.loc[2:5, ['pclass', 'who']]
df.loc[:3, 'class':'deck'] # 컬럼으로도 슬라이싱 가능
```
- 비교연산자 : True, False가 뭉쳐져있는 데이터를 만들어 주는 것
```python
df['who'] == 'man' 
# 'who'의 값이 'man'인 것을 출력하는 것이 아니라 'man'이면 True, 'man'이 아니면 False
```
```python
condition = df['who'] == 'man'
df[condition]
df.loc[condition]
df[df['who'] == 'man'] # 세 가지 모두 같은 코드
```
```python
df.loc[condition, 'age'] = 100 # condition 조건을 만족하는 데이터들 중에서 'age'의 값을 100으로 변환
```
- 조건이 2개 이상일 떄
```python
cond1 = df['fare'] > 30
cond2 = df['who'] == 'woman'

df.loc[cond1 & cond2] # 조건 cond1와 cond2를 모두 만족하는 데이터 출력
df.loc[cond1 | cond2] # 두개의 조건 중 하나만 맞아도 데이터 출력
```
```python
df.iloc[1, 3] # iloc는 []안에 무조건 숫자를 넣어야함
df.iloc[1:3, 2:5] #인덱스 접근이라 3번째 정보는 포함 x, iloc[행, 열]
```

### Where()
- 조건이 False인 데이터들을 일괄적으로 수정하는 함수
```python
cond = df['fare'] < 30
df['fare'].where(cond, 1) # 30이상인 데이터의 'fare'값을 1로 변환
```

### isin()
- 데이터프레임에 입력값이 포함되어있는지 아닌지
```python
cond1 = df['embarked'] == 'Q'
cond2 = df['embarked'] == 'S'
df.loc[cond1 & cond2]

cond = df['embarked'].isin(['Q', 'S'])
df.loc[cond] # 위와 같은 코드
```

# 04_statistics
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

# 05_missing_value
## copy()
- `copy()` : 데이터프레임 복제하기
```python
df_copy = df.copy()
id(df_copy), id(df) # id를 확인하면 주소?가 다름름
```

## 결측치
1. 결측 데이터 확인
2. 결측치가 아닌 데이터 확인
3. 결측치 채우기 or 결측 데이터 제거하기