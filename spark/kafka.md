# kafka
- 스트리밍 데이터를 사용할 때는 스파크 하나만 쓰지 않고 보통 kafka와 같이 사용
![kafka](/spark/assets/kafka.png)
- publisher가 데이터를 계속해서 보내면 TOPIC이라는 테이블이 가지고있다가 subscriber가 달라하면 순서대로 보냄

## download
- install
```shell
wget https://dlcdn.apache.org/kafka/3.9.0/kafka_2.13-3.9.0.tgz
```

- unzip
```shell
tar -zxvf kafka_2.13-3.9.0.tgz
```

## 실행
```shell
# zookeeper 실행
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties

# kafka 실행
bin/kafka-server-start.sh -daemon config/server.properties
```

## TOPIC 생성
- 이름이 temp인 TOPIC 생성 ⇒ `localhost:9092` : kafka의 기본 포트
```shell
bin/kafka-topics.sh --create --topic temp --bootstrap-server localhost:9092
```

## kafka 실행
- 터미널 창 2개로 나누기(2개 다 WSL)

### producer(publisher)
```shell
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic temp
```

### consumer(subscriber)
- `--from-beginning` : 과거부터 쌓였던 내가 수신하지 않은 메세지를 볼 수 있는 옵션
```shell
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic temp
```

## python - kafka
- `damf2/kafka` 폴더 생성 -> `upbit-producer.py`파일 생성
- 가상환경 설정
```shell
# 위치 이동
cd ../damf2/kafka/

# 가상환경 생성
python -m venv venv

# 가상환경 활성화
source venv/bin/activate
```

- `confluent-kafka` install
```shell
pip install confluent-kafka
```

### 연습
- producer -> `upbit-producer.py`
```python
from confluent_kafka import Producer

conf = {
    'bootstrap.servers': 'localhost:9092'
}

p = Producer(conf)

p.poll(0) # 0초 => 바로 최신화 해주세요
p.produce('temp', 'python-message-test') # (토픽이름, 넣고싶은 문장)
p.flush()
```

- consumer 실행
```shell
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic temp
```
=> 위의 파일(producer) 실행하면 python-message-test가 나타남

### Upbit
- `upbit-producer.py` -> 5초에 한번씩 api에 요청 보내서 데이터 가져오기
    - `pip install requests`
```python
from confluent_kafka import Producer
import time
import requests # api 외부로 요청 보냄

UPBIT_URL = 'https://api.upbit.com/v1/ticker'
params = {
    'markets': 'KRW-BTC'
}

conf = {
    'bootstrap.servers': 'localhost:9092'
}

p = Producer(conf)

# p.poll(0) # 0초 => 바로 최신화 해주세요
# p.produce('temp', 'python-message-test') # (토픽이름, 넣고싶은 문장)
# p.flush()

while True:
    res = requests.get(UPBIT_URL, params=params)
    bit_data = res.json()
    print(bit_data)
    time.sleep(5) # 5초에 한번씩 요청을 보내서 데이터를 가져옴
```

- `upbit-producer.py` → 필요한 데이터 선택해서 보내기
```python
from confluent_kafka import Producer
import time
import requests # api 외부로 요청 보냄

UPBIT_URL = 'https://api.upbit.com/v1/ticker'
params = {
    'markets': 'KRW-BTC'
}

conf = {
    'bootstrap.servers': 'localhost:9092'
}

p = Producer(conf)

# p.poll(0) # 0초 => 바로 최신화 해주세요
# p.produce('temp', 'python-message-test') # (토픽이름, 넣고싶은 문장)
# p.flush()

while True:
    res = requests.get(UPBIT_URL, params=params)
    bit_data = res.json()[0]
    result = f"{bit_data['market']}, {bit_data['trade_date']}, {bit_data['trade_time']}, {bit_data['trade_price']}"
    print(result)

    p.poll(0)
    p.produce('temp', result)
    p.flush()

    time.sleep(5) # 5초에 한번씩 요청을 보내서 데이터를 가져옴
```
⇒ 파일을 실행하면 consumer에 result가 뜸

- `upbit-consumer.py` 파일 생성
```python
from confluent_kafka import Consumer

conf = {
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'upbit-consumer',
}

c = Consumer(conf)
c.subscribe(['temp'])

try:
    while True:
        msg = c.poll(1) # 1초에 한번씩

        if msg == None: # None이면 출력하지 않음
            continue

        result = msg.value().decode('utf-8')
        print(result)

except  KeyboardInterrupt:
    c.close()

print('--end--')
```

## spark - kafka
- spark-kafka 연결 설정
    - anonymous → interpreter → spark 검색
    ```
    spark.jars.packages => 'org.apache.spark:spark-sql-kafka-0-10_2.12:3.4.4' 넣기
    ```

### Upbit
- subscribe
```python
%pyspark
from pyspark.sql.functions import split, col

df_stream_kafka = spark.readStream.format('kafka')\
                .option('kafka.bootstrap.servers', 'localhost:9092')\
                .option('subscribe', 'temp')\
                .load()

# 원하는 데이터 뽑기
# col('value') : 데이터 한 줄
df_parsed = df_stream_kafka.select(
    split(col('value'), ',')[0].alias('market'),
    split(col('value'), ',')[1].alias('trade_date'),
    split(col('value'), ',')[2].alias('trade_time'),
    split(col('value'), ',')[3].alias('trade_price'),
)

# 지속적으로 들어오는 데이터 기록
query = df_parsed.writeStream\
        .trigger(processingTime='10 seconds')\
        .outputMode('append')\
        .format('csv')\
        .option('path', 'hdfs://localhost:9000/output/upbit-stream')\
        .option('checkPointLocation', 'hdfs://localhost:9000/output/upbit-temp')\
        .start()
```

- 데이터 읽기
```python
%pyspark
df = spark.read.csv('hdfs://localhost:9000/output/upbit-stream', inferSchema=True)
```

- 데이터 확인
```python
%pyspark
# z.show(df)
df.count()
```

- _c3별 평균 -> `format_number()` : 보기좋게 출력
```python
%pyspark
from pyspark.sql.functions import format_number

# format_number : 사람이 보기 좋게 숫자 출력
result = df.select('_c3').groupBy().avg('_c3')
result.select(format_number('avg(_c3)', 2)).show()
```

### log data
- publisher → `log-producer.py` 파일 생성
    - `pip install faker`
```python
import random
from faker import Faker
from datetime import datetime
from confluent_kafka import Producer
import time

producer = Producer({'bootstrap.servers': 'localhost:9092'})
topic = 'logs'

fake = Faker()

def generate_log_line(timestamp):
   
    ip = fake.ipv4()
    method = random.choice(['GET', 'POST'])
    if random.random() < 0.5:
        path = f'/product/{random.randint(1000, 9000)}'
    else:
        path = random.choice(['/index', '/login', '/contact'])
    protocol = 'HTTP/1.1'
    status_code = random.choice([200, 301, 400, 404, 500])
    response_size = random.randint(200, 5000)

    return f'{ip} {timestamp} {method} {path} {protocol} {status_code} {response_size}'

def send_one_log():
    now = datetime.now().strftime('%Y-%m-%d:%H:%M:%S')

    log_line = generate_log_line(now)
    print(log_line)

    producer.poll(0)
    producer.produce(topic, log_line.encode('utf-8'))
    producer.flush()


while True:
    send_one_log()
    time.sleep(1)
```

- 이름이 logs인 TOPIC 생성
```shell
bin/kafka-topics.sh --create --topic logs --bootstrap-server localhost:9092
```

- subscribe
```python
%pyspark
raw = spark.readStream\
        .format('kafka')\
        .option('kafka.bootstrap.servers', 'localhost:9092')\
        .option('subscribe', 'logs')\
        .option('startingOffsets', 'latest')\
        .load()
        
df = raw.select(
    split(col('value'), ' ')[0].alias('ip'),
    split(col('value'), ' ')[1].alias('timestamp'),
    split(col('value'), ' ')[2].alias('method'),
    split(col('value'), ' ')[3].alias('path'),
    split(col('value'), ' ')[4].alias('protocol'),
    split(col('value'), ' ')[5].alias('status_code'),
    split(col('value'), ' ')[6].alias('size'),
)

query = df.writeStream\
        .trigger(processingTime='10 seconds')\
        .outputMode('append')\
        .format('csv')\
        .option('path', 'hdfs://localhost:9000/output/log-stream')\
        .option('checkPointLocation', 'hdfs://localhost:9000/output/log-temp')\
        .option('header', 'true')\
        .start()
```

- 쿼리 멈추기
```python
%pyspark
query.stop()
```

- 데이터 읽기
```python
%pyspark
logs_df = spark.read.csv('hdfs://localhost:9000/output/log-stream', header=True)
```

- 데이터 확인
```python
logs_df.show()
logs_df.count()
```

- `status_code`별 개수
```python
%pyspark
logs_df.groupBy('status_code').count().show()
```