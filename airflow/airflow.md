# [AirFlow](https://airflow.apache.org/)
- 초기 에어비엔비(Airfbnb) 엔지니어링 팀에서 개발한 워크플로우 오픈 소스 플랫폼
- 워크플로우를 만드는게 목표
- 시각적으로 보기 좋게 만들어줌
- 각각의 태스크들을 연결
- 파이썬으로 작성해놓은 코드들을 자동으로 특정 시간에 맞춰서 반복 → 오류를 한번에 보여줌
- 모든 워크플로우를 유향비순환 그래프(DAG)로 구성

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