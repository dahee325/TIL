## 00_intro
- `airflow/dags/00_intro.py` 폴더와 파일 생성
```python
from airflow import DAG
from datetime import datetime, timedelta
from airflow.operators.bash import BashOperator

with DAG(
    dag_id='00_intro', # dag 이름
    description='first DAG', # 설명
    start_date=datetime(2025, 1, 1,), # dag를 처음 실행할 날짜, 현재보다 과거의 날짜가 들어와야함
    catchup=False, # 과거의 데이터부터 이어서 할지
    schedule=timedelta(minutes=1), # 1분에 한번씩 실행
    # schedule='* * * * *', # 매월 매일 매시간 매분 매초 실행
) as dag:
    # 태스크 정의
    # 태스크 이름 = BashOperator()
    t1 = BashOperator(
        task_id='first_task',
        bash_command='date'
    )

    t2 = BashOperator(
        task_id='second_task',
        bash_command='echo hello!!!!!!!!!!'
    )

    # 태스크 연결 : t1작업을 진행한 후 t2작업 진행
    t1 >> t2
```

- airflow 페이지 ( `localhost:8080` ) → `DAGs` → `00_intro` → 실행시키고 들어가기
    - 안생기면 airflow 껐다가 다시 켜기
![alt text](/airflow/assets/00_intro.png)
⇒ 초록색이 뜨면 실행되고 있는 것