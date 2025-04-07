# 하둡(Hadoop)
- 대용량 데이터를 분산 저장하고 처리하는 오픈소스 프레임워크
- **HDFS**(Hadoop Distributed File System) : 하둡에서 사용하는 분산 파일 시스템, 대용량 파일을 블록 단위로 쪼개어 여러 노드에 저장
- **EMR**(Elastic MapReduce) : AWS에서 제공하는 관리형 하둡 클러스터 서비스
- 클러스터 : 컴퓨터 여러대를 묶는 것
- 인스턴스 : 하나의 컴퓨터
- **NameNode**(대장 컴퓨터) : 실제 데이터를 저장하지 않고, 어디에 어떤 데이터가 있는지만 관리
- **DataNode**(하위 노드들) : 실제 데이터를 저장하는 컴퓨터들

# Amazon EMR
1. **클러스터 생성**
2. **클러스터 이름 지정** : my-cluster
3. **애플리케이션 선택**(Amazon EMR) : 이미 묶어놓은 EMR (Spark를 쓰는게 main이면 설치해야될 앱들을 알아서 선택해줌), 커스텀도 가능
4. **노드 설정** : 프라이머리 1대(대장 컴퓨터 -> 관리), 코어 3대(실제 데이터 저장 및 처리) => 컴퓨터를 총 4대 빌림
5. **키페어 생성** -> 등록 : `my-key.pem`
6. **나머지 필수항목 선택**
7. **클러스터 생성** => 4대의 컴퓨터를 빌리고 aws가 4대의 컴퓨터를 서로 연결하여 하나의 Hadoop 클러스터로 구성


## movielens 데이터 사용
- [MovieLens](https://grouplens.org/datasets/movielens/) : 다양한 사람들이 남긴 영화 평점을 공개해놓은 사이트
- 외부의 데이터를 컴퓨터 내부에서 다운로드 받아야함
1. 데이터 가져오기
- `wget 파일의 링크 주소` : 해당 링크의 자료를 다운로드 받아주세요
- `unzip 파일이름` : 파일 압축 해제
2. 하둡에 `ratings.csv`파일 업로드
- 폴더 안의 가장 용량이 큰 `ratings.csv`파일을 하둡에 저장할 것
- `hdfs dfs -put 파일경로 /` : 하둡의 파일 시스템을 사용해서 해당위치에 있는 파일을 하둡의 최상단(`/`)에 밀어넣을거야
3. 하둡 포트 설정
- 프라이머리(대장컴퓨터)의 인바운드 규칙의 9870(하둡 프로그램에 들어갈 수 있는 포트)열어주기\
=> 하둡은 필요한 포트만 여는 것을 권장, 관리자만 열 수 있도록 권장\
=> 현재 컴퓨터의 ip주소를 인바운드규칙에 등록
4. 하둡 웹에서 `ratings.csv` 파일 확인
- 9870포트의 사이트에 들어가서 Utilities-> Browse the file system 에서 `ratings.csv`파일 확인 -> 하나의 파일이 여러개의 block으로 나뉘어져 있음
5. 복제본 생성
- `hdfs dfs -setrep -w 2 파일` : 해당 파일의 복제(replication)본을 두개 만들어주세요 => 하나의 block에 2개(복제본을 만들어서 2개임)의 파일이 담겨있음

- 대장컴퓨터에는 실제 데이터가 저장되지 않고 하위의 데이터에 저장됨 => 대장컴퓨터는 관리만


# WSL(Windows Subsystem for Linux)
- [그대로 따라하기](https://echo-edu.notion.site/DAMF2-1cb82d1dd35f80a0ba22dcca6ab81970)

- 하둡은 무조건 Linux 환경에서 해야함
- Windows에 리눅스 운영체제를 설치할 수 있게 해주는 시스템
- `Git Bash`창에 `wsl --install` : Linux용 Windowos 하위시스템 설치
- `wsl`앱에 들어가서 계정생성 : 아이디, 비밀번호 모두 ubuntu

# Windows PowerShell
- `sudo apt-get update`
- `sudo apt-get install openjdk-8-jdk` : 자바 설치
- `java -version` : 잘 설치됐는지 확인
- `vi ~/.bashrc`
    - 마지막에 `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`넣고 `:wq`작성 후 나오기
- `sudo apt-get install openssh-server` : ssh설치하기
- `sudo apt-get install ufw`
- `sudo ufw allow 22` : 22번 포트 열기
    - `ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa` : 키 생성, 비밀번호 없고 파일 위치는 `~/.ssh/id_rsa`
    - `cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys` : 키 등록
    - `chmod 600 ~/.ssh/authorized_keys`
<!-- - `cat` : 원본을 그대로 두고 복제본을 만들어서 권한 수정하려고 함 -->
- `code .` : `vscode`로 `ubuntu`열기
    - `C`드라이브가 아닌 `wsl.localhost`라는 가상환경 안에 있음

# Hadoop
- [Apache Hadoop](https://hadoop.apache.org/) => 3.3.6버전 기준
- vscode 터미널 창을 `Ubuntu`로 바꾼 후 `wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz` 입력 => 하둡 설치 => `hadoop-3.3.6.tar.gz`가 생기면 잘 설치된 것
- `tar zxvf hadoop-3.3.6.tar.gz` : 압축 풀기 => `hadoop-3.3.6`파일 생성됨
- `.bashrc`파일에 밑의 코드 추가
```bashrc
export HADOOP_HOME=/home/ubuntu/hadoop-3.3.6
export PATH=$PATH:$HADOOP_HOME/bin
```
- `source /.bashrc` : `.bashrc`파일 새로고침
- `hdfs`
- `hadoop version` : 버전이 잘 설치됐는지 확인

## configuration
- 소프트웨어나 시스템이 어떻게 동작할지 정하는 값들을 모아놓은 것
### 1. `hadoop--3.3.6/etc/hadoop/core-site.xml` : 기본 파일 시스템 정의
    - hdfs를 사용하겠다는 뜻
    - localhost:9000 : 이 컴퓨터(localhost)의 9000번 포트를 통해 하둡 파일 시스템에 접속
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
### 2. `dfs/data`와 `dfs/name` 디렉토리 생성
- `cd hadoop-3.3.6/` 위치 이동
- `mkdir` : 파일 생성, `-p` : 폴더 안에 폴더 생성
```shell
mkdir -p dfs/data # core, 데이터 저장소(DataNode)
mkdir -p dfs/name # 대장, 메타데이터 저장소(NameNode)
```
### 3. `hadoop-3.3.6/etc/hadoop/hdfs-site.xml` : HDFS 설정
- `dfs.replication` : 파일을 저장할 때 복제본의 개수 => 파일에서는 1개
- `dfs.namenode.name.dir` : NameNode가 사용하는 디렉토리(메타데이터 저장)
- `dfs.datanode.data.dir` : DataNode가 사용하는 디렉토리(실제 데이터 저장)
```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/home/ubuntu/hadoop-3.3.6/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>/home/ubuntu/hadoop-3.3.6/dfs/data</value>
    </property>
</configuration>
```
### 4. `hadoop-3.3.6/etc/hadoop/mapred-site.xml` : MapReduce 설정
- `mapreduce.framework.name` : MapReduce 작업을 실행할 환경 설정 => yarn
- `mapreduce.application.classpath` : MapReduce 실행에 필요한 라이브러리 겨로 지정
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
    </property>
</configuration>
```
### 5. `hadoop-3.3.6/etc/hadoop/yarn-site.xml` : yarn 설정
```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>
```
### 6. `hadoop-3.3.6/etc/hadoop/hadoop-env.sh` : 환경 변수 설정
- 자바 설치 경로 지정 => 하둡은 자바 기반 프로그램이기 때문
- `/usr/lib/jvm/java-8-openjdk-amd64` : Ubuntu에서 자바 8이 설치된 기본 경로 중 하나
```sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
- `hdfs namenode -format` : 네임노드 포맷

## 하둡 실행
- 하둡을 실행하기 위해서는 hdfs와 yarn 2개를 실행해야함
- `sbin/start-dfs.sh` : hdfs 실행 => `localhost:9870` 접속확인
- `sbin/start-yarn.sh` : yarn 실행 => `localhost:8088` 접속확인
- 예제 코드 실행 => `3.14120000000000000000` 출력확인 => 나오면 잘 설치된 것
```shell
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar pi 10 10000
```


## MovieLens
- 그냥 window에서 파일을 다운로드 받고 옮겨도 되지만 linux는 거의 독립적이기 때문에 `wget`을 사용하여 파일 다운


- `cd ..` : 폴더의 최상단 `~`으로 이동
- `mkdir damf2` : `damf2`폴더 생성
- `cd damf2/` : `damf2`폴더로 들어가기
- `mkdir data` : `data`폴더 생성
- `cd data/` : `data`폴더로 들어가기
- [MovieLens](https://grouplens.org/datasets/movielens/)\
=> 위의 MovieLens 데이터 사용 순서와 같음
=> 맨 아래의 25M dataset `ml-25.zip` 우클릭해서 링크 주소 복사
- `wget https://files.grouplens.org/datasets/movielens/ml-25m.zip` : 파일 업로드
- `sudo apt-get install unzip`
- `unzip ml-25m.zip` : 압축 풀기
- `localhost:9870`링크의 `Utilities` -> `Browse the file system`
- `hdfs dfs -put ml-25m/ratings.csv /`
- `9870`링크 새로고침하면 `ratings.csv` 파일이 올라감


## Hadoop 종료
- `sbin/stop-dfs.sh`
- `sbin/stop-yarn.sh`\
=> `localhost:9870`, `localhost:8088` 링크에 접속할 수 없음