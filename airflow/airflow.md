# [AirFlow](https://airflow.apache.org/)
- 초기 에어비엔비(Airfbnb) 엔지니어링 팀에서 개발한 워크플로우 오픈 소스 플랫폼


## install
- `.bashrc` 환경변수 설정
```bashrc
# 추가
export AIRFLOW_HOME=~/airflow
```

- `.bashrc` 새로고침
```shell
source ~/.bashrc
```

- 2.10.5 버전 설치
```shell
AIRFLOW_VERSION=2.10.5

# Extract the version of Python you have installed. If you're currently using a Python version that is not supported by Airflow, you may want to set this manually.
# See above for supported versions.
PYTHON_VERSION="$(python -c 'import sys; print(f"{sys.version_info.major}.{sys.version_info.minor}")')"

CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"
# For example this would install 2.10.5 with python 3.8: https://raw.githubusercontent.com/apache/airflow/constraints-2.10.5/constraints-3.8.txt

pip install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"
```

- 실행 => `localhost:8080`
```shell
airflow standalone
```

- 로그인 
    - username : admin
    - password : `airflow/standalone_admin_password.txt`에 초기 비밀번호 나와있음
    - 비밀번호 바꾸기 : `your profile` -> `Reset my password` => 1234


### tutorial
```python
with DAG(
    "tutorial",

    # 여러가지 설정
    default_args={
        "depends_on_past": False,
        "email": ["airflow@example.com"],
        "email_on_failure": False,
        "email_on_retry": False,
        "retries": 1,
        "retry_delay": timedelta(minutes=5),
        ...
    },

    description="A simple tutorial DAG", # 설명서
    schedule=timedelta(days=1), # 실행 주기, 하루에 한번씩 실행
    start_date=datetime(2021, 1, 1), # 실행 시작 날짜
    catchup=False, # 중간에 안했던걸 그냥 패스할 것인지
    # True : 21년부터 오늘까지 한번씩 실행해달라는 것
    tags=["example"],
) as dag:


    # 첫번째 태스트 -> print_date
    t1 = BashOperator(
        task_id="print_date",
        bash_command="date",
    )
		# 두번째 태스크 -> sleep
    t2 = BashOperator(
        task_id="sleep",
        depends_on_past=False,
        bash_command="sleep 5",
        retries=3,
    )

    ...
		# 세번째 태스크 -> templated
    t3 = BashOperator(
        task_id="templated",
        depends_on_past=False,
        bash_command=templated_command,
    )
		# t1이 실행된 후 t2, t3실행
    t1 >> [t2, t3]
```


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


## 03_generate_review
- `airflow/dags/03_generate_review.py` 파일 생성
```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta
import csv
import os
import random

def generate_random_review():
    now = datetime.now()
    file_name = now.strftime('%H%M%S') + '.csv'
    BASE = os.path.expanduser('~/damf2/data/review_data')

    file_path = f'{BASE}/{file_name}'

    review_data = []
    for _ in range(20):
        user_id = random.randint(1, 100)
        movie_id = random.randint(1, 1000)
        rating = random.randint(1, 5)
        review_data.append([user_id, movie_id, rating])

    # 만약 review_data폴더가 없으면 만들어주세요
    os.makedirs(BASE, exist_ok=True)

    with open(file_path, mode='w', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(['user_id', 'movie_id', 'rating'])
        writer.writerows(review_data)

with DAG(
    dag_id='03_generate_review',
    description='movie review',
    start_date=datetime(2025, 1, 1),
    catchup=False,
    schedule=timedelta(minutes=1)
) as dag:
    t1 = PythonOperator(
        task_id='review_generate',
        python_callable=generate_random_review
    )

    # 후속 태스크가 없음
    t1
```

- 실행시키면 vscode의 damf2/data/review_data 폴더가 생기고 그 안에 csv파일들이 쌓임
![alt text](/airflow/assets/03_review_data.png)


## 04_upload_to_hdfs
- 하둡 실행
```shell
~/hadoop-3.3.6/sbin/start-all.sh
```

- `airflow/dags/04_upload_to_hdfs.py` 파일 생성
```python
# review_data 읽어오기, 하둡에 업로드하기, 업로드 했으면 파일 지우기
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta
import os
import subprocess # 파이썬 코드 안에서 리눅스 명령어를 사용할 수 있는 라이브러리

def upload_to_hdfs():
    local_dir = os.path.expanduser('~/damf2/data/review_data')
    hdfs_dir = '/input/review_data'

    # hdfs dfs -mkdir -p /input/review_data
    subprocess.run(['hdfs', 'dfs', '-mkdir', '-p', hdfs_dir])

    files = []

    # 파일 읽어오기
    # os.listdir('path') : 해당 경로에 들어있는 모든 파일을 출력하는 함수
    for file in os.listdir(local_dir):
        files.append(file)

    for file in files:
        # os.path.join() : 앞의 경로와 뒤의 경로를 하나로 합쳐주는 함수
        # ~/damf2/data/review_data + 114148.csv
        local_file_path = os.path.join(local_dir, file)
        hdfs_file_path = f'{hdfs_dir}/{file}'

        # 하둡에 업로드
        # hdfs dfs -put local_file_path hdfs_file_path
        subprocess.run(['hdfs', 'dfs', '-put', local_file_path, hdfs_file_path])

        # 업로드한 파일 지우기
        os.remove(local_file_path)


with DAG(
    dag_id='04_upload_to_hdfs',
    description='upload',
    start_date=datetime(2025, 1, 1),
    catchup=False,
    schedule=timedelta(minutes=5)
) as dag:
    t1 = PythonOperator(
        task_id='upload',
        python_callable=upload_to_hdfs
    )

    t1
```

- `04_upload_to_hdfs`를 실행 → 하둡에 파일이 쌓임 → `damf2/data/review_data`에는 파일이 사라짐


### aws
- s3 → 버킷 만들기
    - 버킷이름 : damf2-ydh


## 05_bitcoin
- `airflow/dags/05_bitcoin.py` 파일 생성
    - `pip install requests` → `requests`가 설치되어있는지 확인
```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta
import time
import requests
import os
import csv

def collect_upbit_data():
    upbit_url = 'https://api.upbit.com/v1/ticker'
    params = {'markets': 'KRW-BTC'}

    collected_data = []

    start_time = time.time()
    while time.time() - start_time < 60:
        res = requests.get(upbit_url, params=params)
        data = res.json()[0]

        csv_data = [data['market'], data['trade_date'], data['trade_time'], data['trade_price']]
        collected_data.append(csv_data)

        time.sleep(5)

    # 파일 저장
    now = datetime.now()
    file_name = now.strftime('%H%M%S') + '.csv'
    BASE = os.path.expanduser('~/damf2/data/bitcoin')
    file_path = f'{BASE}/{file_name}'

    os.makedirs(BASE, exist_ok=True)

    with open(file_path, mode='w', newline='') as file:
        writer = csv.writer(file)
        writer.writerows(collected_data)

with DAG(
    dag_id='05_bitcoin',
    description='crawling',
    start_date=datetime(2025, 1, 1),
    catchup=False,
    schedule=timedelta(minutes=1)
) as dag:
    t1 = PythonOperator(
        task_id='collect_bitcoin',
        python_callable=collect_upbit_data
    )

    t1
```


## 06_upload_to_s3
- `airflow/dags/06_upload_to_s3.py` 파일 생성
```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta
import boto3
from dotenv import load_dotenv
import os

load_dotenv('/home/ubuntu/airflow/.env')

# s3접근 함수
def upload_to_s3():
    s3 = boto3.client(
        's3',
        aws_access_key_id=os.getenv('AWS_KEY'),
        aws_secret_access_key=os.getenv('AWS_SECRET'),
        region_name='ap-northeast-2'
    )

    local_dir = os.path.expanduser('~/damf2/data/bitcoin')
    bucket_name = 'damf2-och' # 내 버킷이 안만들어져서 강사님 버킷으로
    s3_prefix = 'bitcoin-ydh/' # 버킷 안의 새로운 폴더 만들기

    files = []
    for file in os.listdir(local_dir):
        files.append(file)

    for file in files:
        local_file_path = os.path.join(local_dir, file)
        s3_path = f'{s3_prefix}{file}'

        s3.upload_file(local_file_path, s3_path) # 파일의 경로, 버킷의 폴더 경로 지정

        os.remove(local_file_path)

with DAG(
    dag_id='06_upload_to_s3',
    description='s3',
    start_date=datetime(2025, 1, 1),
    catchup=False,
    schedule=timedelta(minutes=5)
) as dag:
    t1 = PythonOperator(
        task_id='upload_to_s3',
        python_callable=upload_to_s3
    )

    t1
```