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

## 0. RDD
- Spark RDD(Resilient Distributed Dataset)
- 분산된 불변의 데이터 집합 -> spark에서 가장 기본이 되는 구조
- `localhost:8080` 링크 접속 -> `Notebook 화살표` -> `Create new note` -> `0.RDD`이름 설정

### word_RDD
- `damf2/data/word.txt` 파일 생성
```txt
apple world
hello apple
world world world
apple hi
hihi hi
```
```zpln
%pyspark # %pyspark를 써야 zeppelin이 spark코드라는걸 인식함

# sc = SparkContext()

# 로컬 파일 읽기
file_path = 'file:///home/ubuntu/damf2/data/word.txt' # 파일이면 `file://`이라고 알려줘야함
lines = sc.textFile(file_path) # 지정한 경로의 파일을 읽어서 RDD로 반환
# print(lines.collect()) #  RDD의 모든 데이터를 드라이버로 모아서 리스트로 반환
```
![alt text](/hbase/assets/word.png)
- 하둡 실행
```shell
~/hadoop-3.3.6/sbin/start-all.sh
```
- 하둡에 파일 올리기
```shell
hdfs dfs -put ~/damf2/data/word.txt /input
# 확인
hdfs dfs -ls /input
```
```zpln
%pyspark

# HDFS에서 파일읽기
file_path = 'hdfs://localhost:9000/input/word.txt'
lines = sc.textFile(file_path)
print(lines.collect())
```
```
# 띄어쓰기를 기준으로 분리-> 일회용함수 lambda 사용
words = lines.flatMap(lambda line: line.split())
print(words.collect())
```
```
# mapreduce 과정 중 map을 끝낸 결과
mapped_words = words.map(lambda word: (word, 1))
print(mapped_words.collect())
```
```
# mapreduce 과정 중 reduce
# reduceByKey() : 키를 바탕으로 reduce해줌
reduced_words = mapped_words.reduceByKey(lambda a, b: a+b)
print(reduced_words.collect())
```
### log RDD
- `2024-01-01.logs` 로그 파일
```zpln
%pyspark

file_path = 'file:///home/ubuntu/damf2/data/logs/2024-01-01.log'
lines = sc.textFile(file_path)
print(lines.collect())
```
```
# 각 데이터 한줄씩 쪼개기 -> 한줄씩 의미가 있기 때문
mapped_lines = lines.map(lambda line: line.split())
print(mapped_lines.collect())
```
```
# 4XX status code 필터링 -> 4로 시작하는 line데이터 출력
def filter_4xx(line):
    return line[5][0] == '4'

filtered_lines = mapped_lines.filter(filter_4xx)
print(filtered_lines.collect())
```
```
# map, reduce
# method('GET', 'POST') 별 요청수 계산
method_rdd = mapped_lines.map(lambda line: (line[2], 1)).reduceByKey(lambda a, b: a+b)
print(method_rdd.collect())
```
```
# 시간대별 요청수
# :을 기준으로 line[1]데이터 쪼개기
time_rdd = mapped_lines.map(lambda line: (line[1].split(':')[1], 1)).reduceByKey(lambda a, b: a+b)
print(time_rdd.collect())
```
- 그룹으로 묶기
```
# status code, api method 별 count
# 2개의 데이터를 넣기 위해서 튜플 안에 튜플을 넣음
count_rdd = mapped_lines.map(lambda line: ((line[5], line[2]), 1)).reduceByKey(lambda a, b: a+b) 
print(count_rdd.collect())
```

## [Mockaroo](https://www.mockaroo.com/)
### 가상의 파일 다운
- 홈페이지 -> rows 50, format : CSV로 설정 -> `PREVIEW`누르면 가상의 데이터 만들어진걸 미리 볼 수 있음 -> `GENERATE DATA`파일 다운 -> `user`로 파일 이름 변경
![alt text](/hbase/assets/user.png)
- 새로운 가상의 파일 생성 -> `post`로 파일 이름 변경
![alt text](/hbase/assets/post.png)
- `/home/ubuntu/damf2/data`로 `user.csv`와 `post.csv`파일 위치 이동
```
# 파일 불러오기
%pyspark

user_file_path = 'file:///home/ubuntu/damf2/data/user.csv'
post_file_path = 'file:///home/ubuntu/damf2/data/post.csv'

user_lines = sc.textFile(user_file_path)
post_lines = sc.textFile(post_file_path)

# print(user_lines.collect())
```
```
# ,를 기준으로 한줄씩 쪼개기
user_rdd = user_lines.map(lambda line: line.split(','))
post_rdd = post_lines.map(lambda line: line.split(','))
print(post_rdd.collect())
```
```
# (user_id, user) -> user_id를 밖으로 뺀 후 튜플로 묶기
user_tuple = user_rdd.map(lambda user: (user[0], user)) # user_id가 0번째에 위치
print(user_tuple.collect())
# (user_id, post)
post_tuple = post_rdd.map(lambda post: (post[1], post)) # user_id가 1번째에 위치
```
- join 사용
```
# 첫 변수를 기준으로 join해줌
# 기본적으로 공통적으로 가진 데이터만 출력 -> 게시물을 작성하지 않은 사람은 빠짐
joined_rdd = user_tuple.join(post_tuple)
print(joined_rdd.collect())
```
![SQL join](/hbase/assets/sql_join.png)

## 1. DF (DataFrame)
- [spark dataframe](https://spark.apache.org/docs/latest/api/python/getting_started/quickstart_df.html)
```
%pyspark

file_path = 'file:///home/ubuntu/damf2/data/logs/2024-01-01.log'

df = spark.read.csv(file_path, sep=' ') # 띄어쓰기를 기준으로 분리, ""는 안에 띄어쓰기가 있어도 하나의 묶음으로 봄
df.show() # 해당 데이터프레임을 표로 만들어서 보여줌
```
```
%pyspark
df.show(1) # 1행 출력
```
```
%pyspark

# 모든 컬럼 출력
df.columns
```
```
%pyspark

# 스키마 구조 출력
df.printSchema()
```
```
%pyspark

# 특정 컬럼 출력
df.select('_c0', '_c1').show()
```
```
%pyspark
# 3개 행 출력
df.take(3)
```
- `pip install pandas` : 가상환경이 비활성화된 상태에서 설치
```
%pyspark
# pandas 형태의 dataframe으로 변환
pd_df = df.toPandas()
```
```
pd_df['_c0']
pd_df[['_c0', '_c2']]
```
![pandas dataframe](/hbase/assets/pandas_df.png)
```
%pyspark
# _c2만 출력
df.select(df._c2).show()
```
- `pip install pyspark`
```
# _c2는 3개의 변수값을 갖고있음 -> 쪼갤 필요가 있음

%pyspark
from pyspark.sql.functions import split, col
# _c2를 띄어쓰기를 기준으로 분리한 후 0번째 데이터를 이름이 method인 새로운 컬럼 생성
df = df.withColumn('method', split(col('_c2'), ' ').getItem(0))
df = df.withColumn('path', split(col('_c2'), ' ').getItem(1))
df = df.withColumn('protocal', split(col('_c2'), ' ').getItem(2))

df.show()
```
```
%pyspark
# method가 POST인 데이터만 출력
df.filter(df.method == 'POST').show()
```
- groupby 사용
```
%pyspark
# method별 데이터 개수
df.groupby('method').count().show()
```
```
%pyspark
from pyspark.sql.functions import min, max, mean

# method와 _c3 별 min, max, mean
df.groupby('method', '_c3').agg(min('_c4'), max('_c4'), mean('_c4')).show()
```
=> 더 정확하게 하려면 형변환하고 실행해야함
```
%pyspark
from pyspark.sql.functions import min, max, mean

# _c4의 유형을 integer로 변경
df = df.select('method', '_c3', col('_c4').cast('integer'))

# method와 _c3 별 min, max, mean
df.groupby('method', '_c3').agg(min('_c4'), max('_c4'), mean('_c4')).show()
```
```
%pyspark

file_path = 'file:///home/ubuntu/damf2/data/logs/2024-01-01.log'

df = spark.read.csv(file_path, sep=' ')

# 데이터프레임안의 데이터를 sql에 접근 가능한 테이블로 바꾸고 테이블 이름을 logs라 설정
df.createOrReplaceTempView('logs')
```
```
%pyspark
spark.sql('''
    SELECT * FROM logs
''').show()
```
- sql문을 사용하여 _c2 분리
```
%pyspark
df = spark.sql("""
    SELECT *, SPLIT(_c2, ' ')[0] AS method, SPLIT(_c2, ' ')[1] AS path, SPLIT(_c2, ' ')[2] AS protocal
    FROM logs
""")
df.show()
df.createOrReplaceTempView('logs2')
```
```
%pyspark
# _c3가 400인 데이터만 출력
spark.sql('''
    SELECT * FROM logs2
    WHERE _c3 = 400
''').show()
```
```
%pyspark
# _c3가 200이고 path가 product를 포함하는 데이터 출력
spark.sql('''
    SELECT * FROM logs2
    WHERE _c3 = 200 AND path LIKE '%product%'
''').show()
```
```
%pyspark
# method별 데이터 개수
spark.sql('''
    SELECT method, COUNT(*) FROM logs2
    GROUP BY method
''').show()
```