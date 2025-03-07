# 08_concat
```python
import pandas as pd

df1 = pd.read_csv('data/sales_data1.csv')
df2 = pd.read_csv('data/sales_data2.csv')
```
## concat()
- `concat()` : 데이터프레임을 행 방향으로 연결
    - `ignore_index=True`옵션을 지정하면 각 데이터의 인덱스 번호를 무시하고 새로 지정
```python
pd.concat([df1, df2], ignore_index=True)
```
    - `axis=1` : 컬럼을 기준으로 연결
```python
sales1 = df1.iloc[:, :5] # 첫번째 컬럼부터 다섯번째 컬럼까지 출력
sales2 = df2.iloc[:, 5:] # 다섯번째 컬럼부터 마지막 컬럼까지 출력
pd.concat([sales1, sales2], axis=1)
```

## merge()
- `pd.merge(df1, df2)` : 데이터들의 교집합 데이터만 병합, 
    - `how='inner'` : 교집합, 디폴트값
    - `how='outer'` : 합집합, 비어있는 값은 NaN으로 채워줌
    - `how='left'` : left에 있는 df1의 행에 df2 출력
    - `how='right'` : right에 있는 df2의 행에 df1 출력
    - `left_on=''` : 왼쪽 기준이 되는 컬럼의 이름 지정
    - `right_on=''` : 오른쪽 기준이 되는 컬럼의 이름 지정
```python
info = {
    '고객명': ['박세리', '이대호', '손흥민', '김연아', '마이클조던'],
    '생년월일': ['1980-01-02', '1982-02-22', '1993-06-12', '1988-10-16', '1970-03-03'],
    '성별': ['여자', '남자', '남자', '여자', '남자']
}
money = {
    '고객명': ['김연아', '박세리', '손흥민', '이대호', '타이거우즈'],
    '연봉': ['2000원', '3000원', '1500원', '2500원', '3500원']
}

df1 = pd.DataFrame(info)
df2 = pd.DataFrame(money)

pd.merge(df1, df2)
pd.merge(df1, df2, how='inner') # 위의 코드와 같음
pd.merge(df1, df2, how='outer')
pd.merge(df1, df2, how='left')
pd.merge(df1, df2, how='right')
pd.merge(df1, df2, left_on='이름', right_on='고객명')
```