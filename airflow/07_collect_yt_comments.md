## 07_collect_yt_comments
- airflow/dags/07_collect_yt_comments.py 파일 생성
```python
from airflow import DAG
from datetime import datetime, timedelta
from airflow.operators.python_operator import PythonOperator
from utils.yt_data import * # yt_data에 구현해놨던 함수들을 모두 가져옴

def my_task():
    target_handle = 'coldplay'
    data = get_handle_to_comments(youtube, target_handle)
    save_to_hdfs(data, '/input/yt-data')

with DAG(
    dag_id='07_yt_data',
    description='yt data',
    start_date=datetime(2025, 1, 1),
    catchup=False,
    schedule=timedelta(minutes=10)
) as dag:
    t1 = PythonOperator(
        task_id='yt',
        python_callable=my_task
    )

    t1
```