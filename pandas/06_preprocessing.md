# 06_preprocessing(전처리)
```python
import seaborn as sns
import pandas as pd

df = sns.load_dataset('titanic')
df_copy = df.copy()
```

## 컬럼 추가
```python
df_copy['VIP'] = False # 이름이 'VIP'이고 값이 False인 컬럼 추가
df_copy['family'] = df_copy['sibsp'] + df_copy['parch']
df_copy['gender'] = df_copy['sex'] + '-' + df_copy['who']
df_copy['f/a'] = round(df_copy['fare'] / df_copy['age'], 2) # 반올림해서 소수점 2자리수까지 출력
```

## 삭제
### 행 삭제
- 지우고싶은 인덱스 지정, 원본수정X
```python
df_copy.drop(1)
df_copy.drop(range(5)) # 0-4번까지의 데이터 삭제
```

### 열 삭제
```python
df_copy.drop('VIP', axis=1) # axis=1은 컬럼을 의미
df_copy.drop('VIP', axis='columns')
df_copy.drop(['deck', 'VIP', 'alive'], axis=1, inplace=True) # inplace=True 옵션은 원본에 저장함
```

## 데이터 타입
- `value_counts()` : NaN값을 제외한 데이터의 개수
- `astype()` : 강제적으로 데이터 형변환
- `dtype` : 데이터 타입 확인
```python
df_copy['who'].value_counts()
df_copy['who'] = df_copy['who'].astype('category')
df_copy['who'].dtype
```
- `cat.codes` : 카테고리에 저장되어있는 정보를 자체적으로 숫자로 변경해서 봄
- `cat.rename_categories()` : 카테고리의 이름 수정
```python
df_copy['who'].cat.codes # 'male', 'female', 'child'을 자체적으로 숫자로 변경해서 봄
df_copy['who'] = df_copy['who'].cat.rename_categories(['아이', '남자', '여자'])
```

## datetime
- `pd.date_range()` : 날짜데이터를 여러개 만들 떄 사용
    - `periods=` 옵션은 생성할 날짜의 개수
    - `freq=15h`은 날짜 간격 설정
```python
dates = pd.date_range('20250101', periods=df.shape[0], freq='15h') # 15시간 간격으로 행개수만큼 날짜데이터 생성
```
- `periods=df.shape[0]`은 생성할 날짜의 개수로 df의 행개수
- `freq=15h`은 날짜 간격 설정으로 15시간 간격을 의미
- `dt` : datetime 객체에 접근하기 위한 함수
- `dt.day` : 몇 일인지 출력
- `dt.dayofweek` : 요일, 0(월요일) ~ 6(일요일)
```python
df_copy['date'].dt.day
df_copy['date'].dt.dayofweek

cond = df_copy['date'].dt.dayofweek == 6
df_copy.loc[cond].head(2)
```

## 자전거 데이터 활용
```python
df = pd.read_csv('data/seoul_bicycle.csv')
```
- `pd.to_datetime()` : 정보의 객체를 datetime으로 변환
    - `format=` : datetime으로 바꿀 떄 형식 지정
```python
df_copy['대여일자'] = pd.to_datetime(df_copy['대여일자'], format='%b-%d-%Y') 

cond = df_copy['대여일자'].dt.dayofweek == 3 # 대여일자의 요일이 3(수요일)인 데이터만 출력
df_copy.loc[cond]
```
- `pd.to_numeric()` : 컬럼을 숫자로 바꿈
    - `errors=''`옵션으로 에러가 생겼을 때 처리하는 방법을 지정
        - `errors='coerce'` : 숫자로 바꿀 수 없는 값을 nan으로 지정
```python
df_copy['운동량'] = pd.to_numeric(df_copy['운동량'], errors='coerce')
```

## cut()
- `cut(나눌 대상의 데이터, 구간 데이터)` : 구간 나누기
- 이상치가 나오므로 나중에 이상치를 처리해야함
```python
bins = [0, 100, 200, 300, df_copy['운동량'].max()]
pd.cut(df_copy['운동량'], bins)
pd.cut(df_copy['운동량'], bins=10) # 자동으로 10개의 구간 나누기
```
- `qcut(나눌 대상의 데이터, 구간 데이터)` : 비율대로 구간 나눔
    - `labels=[]` : 나눈 구간의 이름 설정
```python
pd.qcut(df_copy['운동량'], q=10) # 10%(q=10)의 비율대로 나눔
qcut_bins = [0, 0.2, 0.8, 1] # 0~20%, 20~80%, 80~100%
df_copy['운동량_한글'] = pd.qcut(df_copy['운동량'], qcut_bins, labels=['적음', '보통', '많음'])
```