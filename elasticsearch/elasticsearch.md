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