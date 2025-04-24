## 01_bash_operator
- `airflow/dags/01_bash_operator.py` 파일 생성
```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from datetime import datetime, timedelta

with DAG (
    dag_id='01_bash_operator',
    description='bash',
    start_date=datetime(2025, 1, 1),
    catchup=False,
    schedule=timedelta(minutes=1)
) as dag:
    t1 = BashOperator(
        task_id='echo',
        bash_command='echo start!!!'
    )

    # 여러줄의 문장 사용
    my_command = '''
        {% for i in range(5) %}
            echo {{ ds }}
            echo {{ i }}
        {% endfor %}
    '''

    t2 = BashOperator(
        task_id='for',
        bash_command=my_command
    )

    t1 >> t2
```

- `DAGs` → `01_bash_operator` → 실행시키고 들어가기
![alt text](/airflow/assets/01_bash_operator.png)