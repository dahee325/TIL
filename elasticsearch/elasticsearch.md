# ElasticSearch
✔️ 검색 서비스 > 검색 시스템 > 검색엔진

✔️ **검색엔진** : 광활한 웹에서 정보를 수집해 검색 결과를 제공하는 프로그램

✔️ **검색 시스템** : 대용량 데이터를 기반으로 신뢰성 있는 검색 결과를 제공하기위해 검색엔진을 기반으로 구축된 시스템

✔️ **검색 서비스** : 검색엔진을 기반으로 구축한 검색 시스템을 활용해 검색 결과를 서비스로 제공

✔️ 검색 시스템 구성 : 수집기 → 색인기 → 스토리지 → 검색기\
    => 수집기 : 웹에서 필요한 정보를 수집하는 프로그램\
    => 색인기 : 수집된 데이터를 검색 가능한 구조로 가공하고 저장\
    => 스토리지 : 데이터베이스에서 데이터를 저장하는 물리적인 장소\
    => 검색기 : 사용자의 질의를 입력받아 색인기에서 저장한 역색인 구조에서 일치하는 문서를 찾아 결과로 반환


## install
### elasticsearch
- download
```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.18.0-linux-x86_64.tar.gz
```

- unzip
```bash
tar -zxvf elasticsearch-8.18.0-linux-x86_64.tar.gz
```

### kibana
- download
```bash
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.18.0-linux-x86_64.tar.gz
```

- unzip
```bash
tar -zxvf kibana-8.18.0-linux-x86_64.tar.gz
```

## 실행
- elasticsearch => `localhost:5601`
```bash
cd elasticsearch-8.18.0/

bin/elasticsearch
```

- kibana
```bash
cd kibana-8.18.0/

bin/kibana
```

- elasticsearch가 잘 실행되는지 확인
```bash
curl http://www.naver.com
# 네이버에 들어갔을 때 뜨는 화면의 html코드를 보여줌

curl -k -u elastic:elastic1234 https://localhost:9200
```

## 2. 엘라스틱서치 구성
- 구성요소
    - 인덱스 : 테이블
    - 샤드
    - 타입
    - 문서
    - 필드
    
- 노드 종류 ⇒ 분산처리를 위해 나뉘어짐
    - 마스터 노드
    - 데이터 노드

- API종류
    - 인덱스관리 API : 인덱스 관리 : 스키마
    - 문서관리 API : 문서의 추가/수정/삭제, CRUD
    - 검색 API : 문서조회, 필요한 데이터 선택
    - 집계 API : 문서 통계

## 연습
```bash
# my-index 생성
PUT /my-index

# my-index에 데이터 추가
# 여러번 실행하면 계속해서 데이터 추가
POST /my-index/_doc
{
    "id": "park_rocky-mountain",
    "title": "hello Nice to meet you~",
    "description": "Hello world!"
}

# my-index에 rocky mountain 검색 -> 대소문자 상관없는 듯
# _score : 검색어와 결과가 일치하는 정도를 점수화, 상대적인 점수(1이 넘을수도 있음, 음수도 나올 수 있음)
GET /my-index/_search?q="hello"
```


## 2-1. 인덱스 관리 API
- 인덱스 생성 및 확인
```bash
# 인덱스 생성
PUT /movie

# 인덱스안에 데이터 생성
PUT /movie/_doc/1
{
    "movieNm": "살아남은 아이",
    "prdtYear" : 2017
}

PUT /movie/_doc/2
{
    "movieNm": "아이언맨",
    "prdtYear": 2010
}

# 확인(전체 검색)
GET /movie/_search
```

- 인덱스 구성정보
```bash
# 인덱스 구성정보
GET /movie
```

- 인덱스 삭제
```bash
# 인덱스 지우기
DELETE /movie
```

## 2-2. 문서관리 API
- 스키마 구조를 잡아서 인덱스 생성
```bash
PUT /movie
{
    "mappings": {
        "properties": {
            "movieNm": {
                "type": "text"
            },
            "prdtYear": {
                "type": "integer"
            }
        }
    }
}
```

- 데이터 생성
```bash
# 인덱스에 데이터 생성
PUT /movie/_doc/1
{
    "movieNm": "살아남은 아이",
    "prdtYear": 2017
}

PUT /movie/_doc/2
{
    "movieNm": "아이언맨",
    "prdtYear": 2010
}
```

- 인덱스 구조 확인(정보 조회)
```bash
# 스키마 구조 확인
GET /movie
```

- 데이터 확인
```bash
# 데이터 확인
GET /movie/_search
```

- 2번 문서 삭제(데이터 삭제)
```bash
# 2번 문서 삭제
DELETE /movie/_doc/2
```

- id를 지정하지 않고 데이터 생성 → id가 난수(랜덤)로 생성
```bash
# id를 지정하지 않고 데이터 생성
POST /movie/_doc
{
    "movieNm": "겨울왕국",
    "prdtYear": 2014
}
GET /movie/_search
```

# 3-1. 매핑 API
- 매핑 정보 확인
```bash
# 매핑 정보 확인
GET /movie/_mapping
```

- 매핑 파라미터
    - analyzer : 해당 필드의 데이터를 형태소 분석
    - normalizer : 대소문자 등을 구분 → cafe, Cafe를 서로 다른 문서로 인식
    - format : 날짜/시간을 문자열로 변경할 때

# 3-2. 메타필드
- _index
- _type
- _id

# 3-3. 필드 데이터 타입
- mapping 확인
```bash
# mapping 확인
GET /movie_mapping/_mapping
```

- keyword
```bash
# 필드 데이터 타입
PUT /movie_mapping

# keyword
PUT movie_mapping/_mapping/
{
    "properties": {
        "multiMovieYn": {
            "type": "keyword"
        }
    }
}
```

- text
```bash
# text
PUT movie_mapping/_mapping
{
    "properties": {
        "movieComment": {
            "type": "text"
        }
    }
}
```

- numeric
    - long
    - integer
    - short
    - byte
    - double
    - float / half_float
```bash
# integer
PUT movie_mapping/_mapping
{
    "properties": {
        "Year": {
            "type": "integer"
        }
    }
}
```

- date
```bash
# date -> 데이터의 포맷도 같이 적어줘야함
PUT movie_mapping/_mapping
{
    "properties": {
        "date": {
            "type": "date",
            "format": "yyyy-MM-dd"
        }
    }
}
```

- range
    - integer_range
    - float_range
    - long_range
    - double_range
    - date_range
    - ip_range
```bash
# date_range -> 시작과 끝을 지정해줘야함
PUT movie_mapping/_mapping
{
    "properties": {
        "showRange": {
            "type": "date_range"
        }
    }
}
# 상영기간(범위) 지정
POST movie_mapping/_doc
{
    "showRange": {
        "gte": "2025-01-01",
        "lte": "2025-12-31"
    }
}
```

- geo-point
```bash
# geo_point -> 위도와 경도값을 같이 넣어줘야함
PUT movie_mapping/_mapping
{
    "properties": {
        "filmLocation": {
            "type": "geo_point"
        }
    }
}
POST movie_mapping/_doc
{
    "filmLocation": {
        "lat" : 55,
        "lon": -1
    }
}
```

# 3.4 엘라스틱서치 분석기
- Analyze API → 기본적으로 띄어쓰기를 기준으로 구분
```bash
# 분석기
POST _analyze
{
    "analyzer": "standard",
    "text": "우리나라가 좋은나라, 대한민국 화이팅"
}
```

- 역색인 : 어떤 단어가 어떤 문서에 나오는지를 빠르게 찾을 수 있도록 정리한 자료구조
- 분석기 동작 순서
    1. 문장을 특정한 규칙에 의해 수정
    2. 수정한 문장을 개별 토큰으로 분리
    3. 개별 토큰을 특정한 규칙에 의해 변경

# 3.5 document API
## 3-5-1. CRUD
- create
```bash
# CRUD
# create
PUT movie_mapping/_doc/1
{
    "movieNm": "아이언맨"
}
GET movie_mapping/_doc/1
```

- update
```bash
# update -> 덮어씌우기
PUT movie_mapping/_doc/1
{
    "movieNm": "아이언맨2"
}
# read
GET movie_mapping/_doc/1
```

- delete
```bash
DELETE movie_mapping/_doc/1
```

## 3-5-2. Bulk API
- `/damf2/ES/bulk.py` 폴더와 파일 생성
    - `pip install "elasticsearch>=8, <9”` → 8이상 9이하인 버전 설치
    - `pip install faker`

```python
# pip install "elasticsearch>=8, <9"
from elasticsearch import Elasticsearch, helpers
from faker import Faker
import random

es = Elasticsearch(
    ['https://localhost:9200'],
    basic_auth=('elastic', 'elastic1234'),
    verify_certs=False
)

fake = Faker()

movies = []

for i in range(1000):
    movies.append({
        '_index': 'movie',
        '_source': {
            'movieNm': fake.sentence(nb_words=3),
            'prdtYear': random.randint(1990, 2025),
        }
    })

helpers.bulk(es, movies)
```

- elasticsearch에서 확인 ⇒ value가 1000개 이상이면 된 것
```bash
GET movie/_search
```

- damf2/ES/bulk.py
```python
# pip install "elasticsearch>=8, <9"
from elasticsearch import Elasticsearch, helpers
from faker import Faker
import random

es = Elasticsearch(
    ['https://localhost:9200'],
    basic_auth=('elastic', 'elastic1234'),
    # verify_certs=False,
    ca_certs='/home/ubuntu/elasticsearch-8.18.0/config/certs/http_ca.crt'
)

fake = Faker()

movies = []

for i in range(1000):
    movies.append({
        '_index': 'movie',
        '_source': {
            'movieNm': fake.sentence(nb_words=3),
            'prdtYear': random.randint(1990, 2025),
        }
    })

helpers.bulk(es, movies)
```

- 확인 ⇒ value가 1000개 더 늘어나 2002개가 됨
```bash
GET movie/_search
```

# 4-1. 검색 API
- 2018년에 개봉한 영화 출력 ⇒ 62개
1. URL 검색
```bash
# 검색
GET movie/_search?q=prdtYear:2018
```

2. Request Body 검색

```bash
# Request Body 검색
GET /movie/_search
{
    "query": {
        "term": {"prdtYear": 2018}
        }
    }
}
```

- 영화이름에 star가 들어가는 영화 ⇒ 7개
```bash
# URL 검색
GET /movie/_search?q=movieNm:star
```

- URL검색에서 자주 사용하는 파라미터
    - q : 검색을 수행할 쿼리 문자열 조건 지정
    - analyzer : 사용할 형태소 분석기 지정
    - sort : 검색 결과의 정렬 기준 필드 지정
    - from : 검색을 시작할 문서의 위치 설정
    - size : 반환할 검색 결과 개수 설정

# 4-2. Query DSL
- 지금까지 사용한 방식은 쿼리 방식
- 필터 방식 ⇒ 결과는 62개로 같음
```bash
# filter방식
GET /movie/_search
{
    "query": {
        "bool": {
            "filter": {
                "term": {
                    "prdtYear": 2018
                }
            }
        }
    }
}
```

- 쿼리 컨텍스트
    - 문서가 쿼리와 얼마나 유사한지 스코어로 계산
    - 일반적으로 전문 검색에 많이 사용
    - 상대적으로 느림 → 캐싱되지않고 디스크 연산을 수행하기 때문

- 필터 컨텍스트
    - 쿼리의 조건과 문서가 일치하는지(Yes/No)를 구분
    - 단순 매칭 여부 검사 → 스코어 계산 X
    - 상대적으로 빠름 → 기본적으로 메모리 연산을 수행하기 때문

- 쿼리 결과 페이징
    - from : 문서의 시작
    - size : 문서의 개수

```bash
# from, size
GET /movie/_search
{
    "query": {
        "term": {"prdtYear": 2018}
        },
        "from": 1,
        "size": 10
    }
}
```

- 쿼리 결과 정렬
    - sort : 필드의 이름, 가격, 날짜 등을 기준으로 재정렬

```bash
# sort
GET /movie/_search
{
    "query": {
        "term": {"movieNm": "star"}
    },
    "sort": {
        "prdtYear": {
            "order": "desc"
        }
    }
}
```

- _source 필드 필터링
    - _source : 검색 결과에 포함하고싶은 필드 지정 ⇒ `movieNm`만 출력

```bash
# _source
GET /movie/_search
{
    "query": {
        "term": {
            "movieNm": "star"
        }
    },
    "_source": ["movieNm"]
}
```

- 범위 검색
    - range ⇒ 2010년부터 2020년까지의 데이터 조회

```bash
# range
GET /movie/_search
{
    "query": {
        "range": {
            "prdtYear": {
                "gte": 2010,
                "lte": 2020
            }
        }
    }
}
```

- operator 설정
    - operator : 문장이 들어오는 경우 and나 or로 연산자를 명시적으로 지정
        
        ⇒ `and` : `star couple`이 포함된 영화제목
        
        ⇒ `or` : `star`나 `couple`중 하나만 포함되거나 둘 다 포함된 영화제목
        

```bash
# operator
GET /movie/_search
{
    "query": {
        "match": {
            "movieNm": {
                "query": "star couple",
                "operator": "and"
            }
        }
    }
}
```

- minimum_should_match : or 연산으로 계산할 때 최소 몇 개 이상일 때 검색 결과가 나오도록 설정

- fuzziness 설정
    - fuzziness : 지정한 문자와 1개 차이나는 문자 출력 ⇒ 1개가 빠지거나 다른 단어
    
    ```
    # fuzziness
    GET /movie/_search
    {
        "query": {
            "match": {
                "movieNm": {
                    "query": "stat",
                    "fuzziness": 1
                }
            }
        }
    }
    ```
    
- boost : 관련성이 높은 필드나 키워드에 가중치를 더 줄 수 있게 해줌