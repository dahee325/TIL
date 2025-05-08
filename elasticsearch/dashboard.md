# Dashboard
- elasticsearch -> dashboard -> create dashboard

# eCommerce
## 총 매출(Sum of taxful_total_price)
1. create visualization
2. Metric : 계산
3. Primary metric : Sum, taxful_total_price
4. save and return
![alt text](/elasticsearch/assets/ttp_sum.png)

## median spending
1. create visualization
2. Metric
3. Primary metric : Median, taxful_total_price
4. save and return
![alt text](/elasticsearch/assets/median_spending.png)

## 카테고리별 records 수
1. create visualization
2. Bar
3. Horizontal axis : Date histogram, order_date, Day
4. Vertical axis : Count, Records
5. Breakdown : Top values, category.keyword -> 같은 카테고리끼리 묶어서 보기
6. Number of values : 8
7. Stacked : 단순 데이터 쌓기, Percentage : 비율로 바꾸기
8. save and return
![alt text](/elasticsearch/assets/cate_count.png)

## 총 판매수량
1. create visualization
2. Line
3. Horizontal axis : Date histogram, order_date, Day
4. Vertical axis : Sum, products.quantity

### 지난주와 이번주 데이터 같이 그리기
5. Vertical axis : Sum, products.quantity
6. Advanced -> Time Shift : 1 week ago
7. save and return
![alt text](/elasticsearch/assets/bar_ttp_sum.png)

## 총 매출 차이 테이블
1. create visualization
2. Table
3. Rows : Date histogram, order_date, Day
4. Metrics : Sum, taxful_total_price
### 지난주 데이터
5. Metrics : Sum, taxful_total_price
6. Advanced -> Time Shift : 1 week ago
### 지난주와 이번주 가격의 차이
7. Metric
8. Primary metric -> Fofmula : `sum(taxful_total_price) - sum(taxful_total_price, shift='1w')`
9. Appearance -> Color by value : Text
10. ![alt text](/elasticsearch/assets/ttp_text_color.png)
11. save and return
![alt text](/elasticsearch/assets/table_ttp.png)

## 나라별 매출 map
1. elasticsearch -> Maps -> Create map
2. Layers -> add layer -> Clusters
3. Data view : Kibana Sample Data eCommerce
4. Cluster field : geoip.location
5. Metric -> add metric -> Sum, taxful_total_price
6. Layer style -> Label : sum taxful_total_price
7. Fill color : sum taxful_total_price, 색 지정
8. save -> 아까 만들었던 dashboard(myeCommerce)에 추가
![alt text](/elasticsearch/assets/map_ttp.png)

# log-dashboard
- Data view : Kibana sample data log
## Count of records
1. create visualization
2. Metric
3. Primary metric : Count, Records
4. save and return

## Unique count of clientip
1. create visualization
2. Metric
3. Primary metric : Count, clientip
4. save and return

## 500에러 수 비율
1. create visualization
2. Metric
3. Primary metric -> Formula : `count(kql='response.keyword > 500') / count()`
    - kql : kibana query language
4. Value format : Percent
5. save and return

## url별 500에러 수
1. create visualization
2. Table
3. Rows : Top values, url.keyword, 20
4. Metrics : Count, Records
5. Primary metric -> Formula : `count(kql='response.keyword > 500') / count()`
6. Value format : Percent
7. Sort dscending
8. save and return

## 지역별 byte 합계 map
1. elasticsearch -> Maps -> Create map
2. Layers -> add layer -> Clusters
3. Data view : Kibana Sample Data eCommerce
4. Cluster field : geoip.location
5. metric : sum, byte
6. save -> 아까 만들었던 dashboard(log-dashboard)에 추가

## 
1. create visualization
2. Bar
3. Horizontal axis : Top values, machine.os.keyword
4. Vertical axis : Unique count, geo.dest
