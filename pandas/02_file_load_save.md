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