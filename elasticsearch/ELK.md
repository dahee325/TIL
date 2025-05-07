# ELK
- Elasticsearch
- Logstash
- Kibana
![ELK](/elasticsearch/assets/ELK.webp)

## logstach install
- download
```bash
wget https://artifacts.elastic.co/downloads/logstash/logstash-8.18.0-linux-x86_64.tar.gz
```

- unzip
```bash
tar -zxvf logstash-8.18.0-linux-x86_64.tar.gz
```

## ELK 실행
- elasticsearch
```bash
# 위치이동
cd elasticsearch-8.18.0/
# 실행
bin/elasticsearch
```

- kibana
```bash
# 위치이동
cd kibana-8.18.0/
# 실행
bin/kibana
```

- logstash
```bash
# 위치이동
cd logstash-8.18.0/
```

## django(insta) 실행
- insta 프로젝트 clone
```bash
# 위치이동
cd damf2/
# 클론하기 -> 일단 강사님꺼 공통적으로 가져옴
git clone https://github.com/DAMF2/insta.git
```

- 필요한 라이브러리 설치
```bash
# 위치이동
# -> damf2/insta/
cd insta/ 

# 가상환경 설정
python -m venv venv
# 가상환경 실행
source venv/bin/activate

# 라이브러리 설치
pip install -r requirements.txt 
```

- 실행 => `localhost:8000`
```bash
# 마이그레이션
python manage.py migrate
# 실행
python manage.py runserver
```

## log파일 생성
- `damf2/insta/insta/settings.py` -> 맨 아래에 작성
```python
...

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,

    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': 'django.log'
        },
        'console': {
            'class': 'logging.StreamHandler'
        },
    },

    'loggers': {
        'django.server': {
            'handlers': ['file', 'console'],
            'level': 'INFO',
            'propagate': False,
        }
    }
}
```

- `damf2/insta/django.log` 파일 생성 
    - `localhost:8000`에서 동작을 하면 밑에처럼 파일에 로그가 쌓임
```bash
"GET /posts/ HTTP/1.1" 200 4282
"GET /media/profile/blue_miffy.png HTTP/1.1" 304 0
"GET /media/image/2025/05/sungoori.jpeg HTTP/1.1" 304 0
"GET /posts/create/ HTTP/1.1" 200 2336
"POST /posts/create/ HTTP/1.1" 302 0
"GET /posts/ HTTP/1.1" 200 5881
"GET /media/image/2025/05/%EB%A7%88%EC%8B%9C%EB%A7%88%EB%A1%9C3.jpeg HTTP/1.1" 200 15028
```

- `damf2/insta/posts/middelware.py` 파일 생성
```python
import logging
from datetime import datetime

loggers = logging.getLogger('mylogger')

class MyLogMiddleware:
    pass
```

- `damf2/insta/insta/settings.py` → 클래스 등록, `mylogger` 설정
```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'posts.middleware.MyLogMiddleware',
]

...

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,

    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': 'django.log'
        },
        'console': {
            'class': 'logging.StreamHandler'
        },
    },

    'loggers': {
        'django.server': {
            'handlers': ['file', 'console'],
            'level': 'INFO',
            'propagate': False,
        },
        'mylogger': {
            'handlers': ['file', 'console'],
            'level': 'INFO',
            'propagate': False,
        }
    }
}
```

- `damf2/insta/posts/middelware.py` → 클래스 설정
```python
import logging
from datetime import datetime

loggers = logging.getLogger('mylogger')

class MyLogMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
        ip = request.META.get('REMOTE_ADDR')
        user = request.user
        method = request.method
        path = request.get_full_path()

        log = f'{timestamp} {ip} {user} {method} {path}'
        
        # info 레벨의 로그를 만들어주세요
        loggers.info(log)

        return self.get_response(request)
```

- `localhost:8000`서버를 껐다가 키면 로그가 2개씩 생성됨 → `django.server`와 `mylogger`

- → `django.server`와 `mylogger`
    - `django.server`의 `handlers`를 빈 리스트로 변경

```python
LOGGING = {
    ...
    'loggers': {
        'django.server': {
            'handlers': [],
            'level': 'INFO',
            'propagate': False,
        },
        'mylogger': {
            'handlers': ['file', 'console'],
            'level': 'INFO',
            'propagate': False,
        }
    }
}
```

# logstash
- [공식 문서](https://www.elastic.co/docs/reference/logstash/first-event)

![alt text](/elasticsearch//assets/logstash.png)

- `damf2/logstash/django_log.conf` 폴더와 파일 생성
    - input : https://www.elastic.co/docs/reference/logstash/plugins/input-plugins
    - filter : https://www.elastic.co/docs/reference/logstash/plugins/filter-plugins
    - output : https://www.elastic.co/docs/reference/logstash/plugins/output-plugins

```python
# 어떤 파일을 가져올지
input {
    file {
        path => "/home/ubuntu/damf2/insta/django.log"
        start_position => "beginning"
        codec => plain { charset => 'UTF-8' }
    }
}

# 어떤 데이터를 사용할지
filter {
    grok {
        match => {
            "message" => "%{TIMESTAMP_ISO8601:timestamp} %{IP:ip} %{WORD:user} %{WORD:method} %{URIPATHPARAM:path}"
        }
    }

    date {
        match => ["timestamp", "yyyy-MM-dd HH:mm:ss"]
        timezone => "Asia/Seoul"
    }
}

# 데이터를 어디로 저장할지
output {
    elasticsearch {
        hosts => ["https://localhost:9200"]
        user => "elastic"
        password => "elastic1234"
        ssl_enabled => true
        ssl_certificate_authorities => "/home/ubuntu/elasticsearch-8.18.0/config/certs/http_ca.crt"
        index => "django-log-data"
    }

    stdout {
        codec => rubydebug
    }
}
```

- logstash 실행
```bash
bin/logstash -f ~/damf2/logstash/django_log.conf 
```

- 확인
    1. 터미널 창에 노란색으로 로그가 쌓이면 잘 된 것
    2. elasticsearch dev-tools ⇒ 데이터가 출력되면 잘 된 것
```bash
GET /django-log-data/_search
```

### Discover
- elasticsearch → Discover → Create A data view
![alt text](/elasticsearch//assets/create_view_data.png)
-> save data view to Kibana

### Dashboard
- elasticsearch → Dashboard → create dashboard → create visualization → Horizontal axis : Date histogram, Vertical axis : Count / path.keyword
![alt text](/elasticsearch//assets/dashboard.png)

# kafka-elasticsearch
- kafka 실행
```bash
# 가상환경 비활성화
deactivate

# 위치이동
cd ~/kafka-3.9.0/

# zookeeper 실행
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties

# kafka 실행
bin/kafka-server-start.sh -daemon config/server.properties
```

- 새로운 TOPIC(bitcoin) 생성
```bash
bin/kafka-topics.sh --create --topic bitcoin --bootstrap-server localhost:9092
```

- `damf2/kafka/upbit-producer.py` → TOPIC이름 수정
```bash
...

while True:
    ...
    p.produce('bitcoin', result)
    p.flush()

    time.sleep(5) # 5초에 한번씩 요청을 보내서 데이터를 가져옴
```

- `upbit-producer.py` 실행
```bash
# 위치이동
cd ../damf2/kafka/

# 가상환경 활성화
source venv/bin/activate

# 실행
python upbit-producer.py
```

- `damf2/kafka/upbit-producer.py` 수정
    - 출력되는 형태를 우리가 선택한 데이터에서 전체 데이터로  바꿈
```bash
...

while True:
    res = requests.get(UPBIT_URL, params=params)
    bit_data = res.json()[0]
    json_data = json.dumps(bit_data)
    # result = f"{bit_data['market']}, {bit_data['trade_date']}, {bit_data['trade_time']}, {bit_data['trade_price']}"
    # print(result)
    print(json_data)

    p.poll(0)
    p.produce('bitcoin', json_data)
    p.flush()

    time.sleep(5) # 5초에 한번씩 요청을 보내서 데이터를 가져옴
```

- `damf2/logstash/bit_data.conf` 파일 생성
```bash
input {
    kafka {
        bootstrap_servers => "localhost:9092"
        topics => ["bitcoin"]
        auto_offset_reset => "earliest"
    }
}

filter {
    json {
        source => "message"
    }
}

output {
    elasticsearch {
        hosts => ["https://localhost:9200"]
        user => "elastic"
        password => "elastic1234"
        ssl_enabled => true
        ssl_certificate_authorities => "/home/ubuntu/elasticsearch-8.18.0/config/certs/http_ca.crt"
        index => "bitcoin-data"
    }

    stdout {
        codec => rubydebug
    }
}
```

- 실행
```bash
# logstash 실행 => ~/logstash-8.18.0
bin/logstash -f ~/damf2/logstash/django_log.conf 

# 다른 터미널 창
python upbit-producer.py
```

- 확인
    - elasticsearch → Dev Tools
```bash
GET /bitcoin-data/_search
```
⇒ value개수가 계속 늘어남

## 시각화
### Discover
- elasticsearch → Discover → Create A data view → Name : bitcoin, Index pattern : bitcoin-data → save data view to Kibana

### Dashboard
- elasticsearch → Dashboard → create dashboard → create visualization → Line → Horizontal axis : Date histogram, Vertical axis : Average / trad_price.keyword