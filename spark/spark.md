# [Spark](https://spark.apache.org/)
- 데이터 분석 작업을 위한 오픈 소스 프레임워크, 빅 데이터 처리에 사용
- 잡 사이의 대용량 작업 데이터 셋을 메모리상에 유지 => 속도가 빠름
- 기본적으로 스칼라 언어 사용
- 실시간 데이터 처리

## install
- 3.4.4 버전 설치
```shell
wget https://dlcdn.apache.org/spark/spark-3.4.4/spark-3.4.4-bin-hadoop3.tgz
```
- unzip 압축풀기 => 폴더명 `spark-3.4.4`으로 수정
```shell
tar -xvzf spark-3.4.4-bin-hadoop3.tgz
```
- `.bashrc`
```bashrc
export SPARK_HOME=/home/ubuntu/spark-3.4.4

export PATH=$PATH:$HADOOP_HOME/bin:$HIVE_HOME/bin:$HBASE_HOME/bin:$SPARK_HOME/bin
```
```shell
source ~/.bashrc` # 파일 새로고침

spark-shell
#print("hello world!") # => 입력해서 잘 되는지 확인

pyspark # => 파이썬이 너무 최신이라 에러남
pyenv global 3.11.12 # 파이썬 버전 낮추고 다시 실행하면 됨
```

## JAVA 11 install
- download
```shell
sudo apt install openjdk-11-jdk
```

## [Zeppelin](https://zeppelin.apache.org/) install
- zeppelin을 설치하려면 java11버전이 필요
- 11.2-bin-netinst 버전 설치
- download
```shell
wget https://dlcdn.apache.org/zeppelin/zeppelin-0.11.2/zeppelin-0.11.2-bin-netinst.tgz
```
- unzip 압축풀기 => 폴더명 `zeppelin-0.11.2`으로 변경
```shell
tar -xvzf zeppelin-0.11.2-bin-netinst.tgz
```
- `zeppelin-0.11.2/conf`폴더안의 `zeppelin-env.sh`, `zeppelin-site.xml`로 파일명 변경
- `zeppelin-env.sh`
```sh
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export SPARK_HOME=/home/ubuntu/spark-3.4.4
```
- `zeppelin-site.xml`
```xml
<property>
  <name>zeppelin.server.addr</name>
  <value>0.0.0.0</value> <!--수정-->
  <description>Server binding address. If you cannot connect to your web browser on WSL or Windows, change 127.0.0.1 to 0.0.0.0. It, however, causes security issues when you open your machine to the public</description>
</property>
```
- 서버 실행 => `localhost:8080` 접속 잘 되는지 확인
```shell
cd zeppelin-0.11.2/

bin/zeppelin-daemon.sh start
```
- `localhost:8080` 링크 접속 -> `Notebook 화살표` -> `Spark Tutorial` ->`Spark SQL` -> `Add Paragraph`
```zpln
%pyspark
print('hello')
# shift+enter로 실행 또는 플레이버튼
```