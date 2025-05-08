# 코호트 분석
- 특정 조건을 만족하는 패턴 추적
- 집단 묶기

# 리텐션 분석
- 얼만큼 우리의 서비스를 계속 쓰는지

# 퍼널분석
![funnel analysis](/elasticsearch/assets/funnel.jpg)

# [eCommerce data](https://www.kaggle.com/datasets/mkechinov/ecommerce-events-history-in-electronics-store?resource=download)
- 파일 다운로드
- `damf2/data`에 `events.csv`파일 옮기기
- `damf2/kibana/ecommerce.py` 폴더와 파일 생성
    - 방문일자와 이후에도 방문했는지
    - 사이트에 지속적으로 방문한 사용자 수

- 파일 불러오기
```python
# pip install pandas
import pandas as pd

# parse_dates=['event_time'] : 시간 형태로 바꿔서 불러옴
df = pd.read_csv('/home/ubuntu/damf2/data/events.csv', parse_dates=['event_time'])
print(df.head(5))
```

- event_time 값의 UTC제거
```python
# event_time 값의 UTC제거
df['event_time'] = df['event_time'].dt.tz_localize(None)
print(df.head(5))
```

- 방문한 년도, 월만 있는 새로운 컬럼(`event_month`) 생성
```python
# 방문한 년도, 월만 있는 새로운 컬럼 생성
df['event_month'] = df['event_time'].dt.to_period('M')
```

- 사용자별 최초의 방문시기의 새로운 컬럼(`cohort_month`) 생성
    - transform('min') : event_month의 가장 작은 값을 찾아서 저장
```python
df['cohort_month'] = df.groupby('user_id')['event_month'].transform('min')
```

- 몇 달 차이가 나는지 새로운 컬럼(`cohort_index`) 생성
```python
# 방문 년도, 월
invoice_year = df['event_month'].dt.year
invoice_month = df['event_month'].dt.month
# 최초 방문 년도, 월
cohort_year = df['cohort_month'].dt.year
cohort_month = df['cohort_month'].dt.month

# 몇 달 차이가 나는지
df['cohort_index'] = (invoice_year - cohort_year) * 12 + (invoice_month - cohort_month) + 1
```

- 문자형으로 유형 바꾸기
```python
# 문자형으로 유형 바꾸기
df['event_month'] = df['event_month'].astype('str')
df['cohort_month'] = df['cohort_month'].astype('str')
```

- csv파일로 저장
```python
# csv파일로 저장
df.to_csv('/home/ubuntu/damf2/data/cohort_event.csv', index=False)
```

## cohort - logstash
- `damf2/logstash/cohort.conf` 파일 생성
```python
input {
  file {
    path => "/home/ubuntu/damf2/data/cohort_event.csv"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  csv {
    separator => ","
    columns => [
      "event_time", "event_type", "product_id", "category_id", "category_code", "brand", "price", "user_id", "user_session",
      "event_month", "cohort_month", "cohort_index"
    ]
  }
  date {
    match => ["event_time", "yyyy-MM-dd HH:mm:ss"]
    target => "@timestamp"
    timezone => "UTC"
  }

  mutate {
    convert => {
      "price" => "float"
      "cohort_index" => "integer"
    }
  }
}

output {
  elasticsearch {
    hosts => ["https://localhost:9200"]
    user => "elastic"
    password => "elastic1234"
    ssl_enabled => true
    ssl_certificate_authorities => "/home/ubuntu/elasticsearch-8.18.0/config/certs/http_ca.crt"
    index => "events_with_cohort"
  }

  stdout {
    codec => rubydebug
  }
}
```

- logstash 실행
```bash
# 위치이동
cd ~/logstash-8.18.0/

# 파싱 실행
bin/logstash -f ~/damf2/logstash/cohort.conf 
```

- elasticsearch → Discover → create a data view → events_with_cohort → 기간 2020년 9월부터로 설정
![alt text](/elasticsearch/assets/events_cohort.png)

# Dashboard
## 01. 월별 records
1. create dashboard
2. create visualization
3. Line
4. Horizontal axis : @timestamp, month
5. Vertical axis : records
6. save and return

## 02. event_type별 사용자 수
1. create visualization
2. Bar
3. Horizontal axis : Top values, event_type.keyword
4. Vertical axis : Unique count, user_id.keyword
5. save and return

## 03. 월별 cohort_index의 사용자 수
1. create visualization
2. Heat map
3. Horizontal axis : cohort_index
4. Vertical axis : cohort_month, Month
5. Cell value : Unique count, user_id.keyword
6. cohort_month가 date여야하는데 text형식이여서 알파벳순으로 바꾼 후 Ascending
![alt text](/elasticsearch/assets/event_dashboard.png)