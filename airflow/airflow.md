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

- DAGs → 02_python_operator → 실행시키고 들어가기
![alt text](/airflow/assets/02_python_operator.png)