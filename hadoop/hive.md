# [Hive](https://hive.apache.org/docs/latest/)
- SQL과 유사한 언어를 사용해 데이터를 처리할 수 있게 해주는 데이터 웨어하우스 시스템
- HDFS와 맵리듀스를 기반으로 개발됨
- MapReduce 자동 변환
- 대용량 분석에 적합

- **메타스토어(MetaStore)**
    - 하이브가 스키마를 저장하는 핵심 공간 => 스키마를 부를 때마다 
    - 스키마를 정의한다 -> 데이터의 규격을 정함

- **읽기 스키마**
    - 
    - 데이터를 읽을 때 비교

- **쓰기 스키마**
    - 데이터를 쓰고 저장할 때 사용
    - 저장된 데이터를 불러오는 과정에서 스키마가 맞는지 검증


# [Hive 설치](https://echo-edu.notion.site/install-1d082d1dd35f801883c7dacf7034ded1)
## 1. download & unzip
1.  Hive 검색 => Apache Hive 접속
- [설치링크](https://hive.apache.org/docs/latest/adminmanual-installation_27362077/)
2. Hive Releases
3.  archives
4. hive-3.1.3/
5. `apache-hive-3.1.3-bin.tar.gz` 링크 주소 복사\
6. 터미널로 Ubuntu 열고 `code .`입력해서 vscode 실행
7. 설치
```shell
wget https://archive.apache.org/dist/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
```
8. 압축 풀기 : `tar`을 사용해서 unzip
```shell
tar -zxvf apache-hive-3.1.3-bin.tar.gz # 파일이름 탭으로 자동완성하기
# -xzvf의 순서는 옵션이기 때문에 상관없음
```
9. 폴더이름 `hive-3.1.3`으로 변경


## 2. 환경변수 등록
- `.bashrc`
```bashrc
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/home/ubuntu/hadoop-3.3.6
export HIVE_HOME=/home/ubuntu/hive-3.1.3 # 추가, 경로설정

export PATH=$PATH:$HADOOP_HOME/bin:$HIVE_HOME/bin # 추가, 명령어 추가
```
- `source ~/.bashrc` : `.bashrc` 파일 새로고침\
=> `source` : 내가 설정한 파일을 터미널에 적용시킨다는 의미\
=> 터미널이 새로 시작될 때는 `.bashrc`를 적용시킴\
=> 터미널 창이 켜진 채로 `.bashrc`파일을 수정하면 터미널창을 껐다 새로 키거나 새로고침 시켜야함

## 3. 메타 데이터 생성
- `cd hive-3.1.3/` : 위치 이동
- `schematool -dbType derby -initSchema`

## 4. safe 모드 해제(하둡이 실행중이어야함)
- `~/hadoop-3.3.6/sbin/start-all.sh ` : 하둡(hdfs, yarn) 실행 - `localhost:9870` 링크로 들어갈 수 있음
- `hdfs dfsadmin -safemode leave`

## 5. /temp/hive 디렉토리 생성
```shell
hdfs dfs -mkdir -p /tmp/hive
hdfs dfs -chmod +w /tmp/hive # group user한테 write(쓰기)권한을 주겠다 => 오류로 hive가 실행안돼서 전체한테 권한 주기
```

## 6. warehouse 디렉토리 생성
```shell
hdfs dfs -mkdir -p /user/hive/warehouse
hdfs dfs -chmod g+w /user/hive/warehouse
```

## 7. hive 실행 확인
- `hive`

# Hive Server
## 1. Hive Server 설정
- `hive-3.1.3/conf/hive-site.xml`파일 생성 : 서버 설정
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>hive.server2.enable.doAs</name>
        <value>false</value>
    </property>
</configuration>
```

## 2. Hive Server 실행
- `hiveserver2 --hiveconf hive.server2.thrift.port=10000 --hiveconf hive.root.logger=DEBUG,console` : 서버 실행 -> 계속 띄워둘 것

## 3. 새로운 터미널(WSL) 생성 => `beeline`실행
```shell
beeline> !help
beeline> !connect jdbc:hive2://localhost:10000
# default user : scott
# default password : tiger
```

## beeline
- SQL코드를 작성하기 위한 공간
```shell
SHOW databases; # 우리가 사용 가능한 데이터베이스를 보여줌

SHOW tables; # 우리가 만든 테이블을 보여줌 -> 없으니까 아무것도 안뜸
```

# [HiveQL](https://echo-edu.notion.site/HiveQL-1d082d1dd35f8044a9b9c72a6a856921)
## 1. data download
- `cd damf2/data/` : 새로운 터미널(WSL)창 열어서 위치 이동
- `wget -O employees wget https://www.dropbox.com/scl/fi/v4ce4uz8jo82sr3yg792o/employees?rlkey=npfh5ok3pm0tr63kmtc3oayid&st=88bzfjo9&dl=0`

## 2. managed table
### 2-1. 테이블 생성 => beeline 터미널창
```sql
CREATE TABLE employees
(
    emp_no     INT,
    birth_date DATE,
    first_name STRING,
    last_name  STRING,
    gender     STRING,
    hire_date  DATE,
    dept_no    STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n';
```
- 테이블 확인
```shell
SHOW tables;
```

### 2-2. load data
```sql
LOAD DATA LOCAL INPATH '/home/ubuntu/damf2/data/employees'
INTO TABLE employees;
```
- 데이터 확인 => 10줄만 출력
```sql
SELECT * FROM employees LIMIT 10;
```
- 전체 데이터 갯수 확인
```sql
SELECT COUNT(*) FROM employees; => 맵리듀스 작업이 실행됨
```
- 생일이 같은 사람의 수 확인
```sql
SELECT birth_date, COUNT(birth_date)
FROM employees
GROUP BY birth_date
LIMIT 10;
```
- 테이블 삭제 => HDFS에 올라간 파일도 같이 삭제됨
```sql
DROP TABLE employees; -> 로컬은 지우지 않고 hdfs만 삭제
```