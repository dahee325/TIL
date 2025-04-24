## 02_python_operator
- `airflow/dags/02_python_operator.py` 파일 생성
```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta

def hello():
    print('hello world!')

def bye():
    print('bye..')

with DAG(
    dag_id='02_python',
    description='python test',
    start_date=datetime(2025, 1, 1),
    catchup=False,
    schedule=timedelta(minutes=1)
) as dag:
    t1 = PythonOperator(
        task_id='hello',
        python_callable=hello # python으로 실행할 수 있는 함수(dag밖에서 함수 선언)
    )

    t2 = PythonOperator(
        task_id='bye',
        python_callable=bye
    )

    t1 >> t2
```

- `DAGs` → `02_python_operator` → 실행시키고 들어가기
![alt text](/airflow/assets/02_python_operator.png)