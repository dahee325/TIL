# 03_query
- 쿼리(query) : 정제된 데이터에서 특정 조건을 만족하는 결과를 추출하기 위해 사용
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