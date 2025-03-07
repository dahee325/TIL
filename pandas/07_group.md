# 07_ group
```python
import seaborn as sns
import pandas as pd

df = sns.load_dataset('titanic')
```

## groupby()
- `groupby()` : 하나의 컬럼을 기준으로 묶어서 데이터 출력
    - `numeric_only=True` 옵션을 지정하면 계산할 수 없는 데이터를 제외한 숫자형 데이터만 계산산
```python
df_copy.groupby('sex').mean(numeric_only=True) # 'sex'을 기준으로 데이터의 평균값 계산
df_copy.groupby(['sex', 'pclass']).mean(numeric_only=True) # 여러개의 컬럼을 기준으로 할 때는 [](리스트) 사용
df_copy.groupby(['sex', 'pclass'])[['survived']].mean(numeric_only=True) # 'sex'과 'pclass'를 기준으로 'survived'의 평균 계산
```

## reset_index()
- `reset_index()` : 데이터프레임의 인덱스를 다시 설정, 기존 인덱스는 새로운 열로 이동
    - `drop=True`옵션을 지정하면 기존 인덱스를 데이터프레임에서 삭제
```python
df_temp = df_copy.groupby(['sex', 'pclass'])[['survived']].mean()
df_temp.reset_index()
```

## pivot_table()
- `pivot_table(index='', values='')` : 각 컬럼이 어떤 관계를 갖고 있는지, corr()와 비슷
```python
df_copy.pivot_table(index='who', values = 'survived')
df_copy.pivot_table(index=['who', 'pclass'], values='survived')
```