# [Youtube API](https://developers.google.com/youtube/v3?hl=ko)
- 하둡 실행
```shell
 ~/hadoop-3.3.6/sbin/start-all.sh 
```

- airflow 실행
```shell
airflow standalone
```
## [install](https://github.com/googleapis/google-api-python-client)
- airflow실행중인 상태에서 터미널 창 새로 생성
```shell
 pip install google-api-python-client
```
### [사용 가능한 라이브러리](https://googleapis.github.io/google-api-python-client/docs/dyn/youtube_v3.html)

## 키 발급받기
- [GCP](https://cloud.google.com/free?utm_source=google&utm_medium=cpc&utm_campaign=japac-KR-all-ko-dr-BKWS-all-core-trial-EXA-dr-1710102&utm_content=text-ad-none-none-DEV_c-CRE_668690472449-ADGP_Hybrid+%7C+BKWS+-+EXA+%7C+Txt+-GCP-General-core+brand-main-KWID_43700077514871058-kwd-87853815&userloc_9195812-network_g&utm_term=KW_gcp&gad_source=1&gad_campaignid=20424845163&gclid=CjwKCAjwn6LABhBSEiwAsNJrjtwxTT0PT_PrnrpDMQr76s1OKYRo4eD23gEFf418XlIdtOFzI0HkmBoCOGsQAvD_BwE&gclsrc=aw.ds)
1. 새로운 my project 생성
2. youtube 검색
3. YouTube Data API v3 -> 사용
4. 사용자 인증정보 만들기 -> 공개 데이터 -> 다음
5. 키 복사 -> `.env`에 저장

- `airflow/dags/utils/yt_data.py` 폴더와 파일 생성
```python
# pip install google-api-python-client
from googleapiclient.discovery import build
from dotenv import load_dotenv
import os


load_dotenv('/home/ubuntu/airflow/.env')
YOUTUBE_API_KEY = os.getenv('YOUTUBE_API_KEY')

youtube = build('youtube', 'v3', developerKey=YOUTUBE_API_KEY)
print(youtube)
```
⇒ 키 발급 & 인증 완료

## 01. channel_id 가져오기
- handle을 기준으로 channelId를 리턴하는 함수
    - channel() 사용
    - `pip install google-api-python-client`
```python
from googleapiclient.discovery import build
from dotenv import load_dotenv
import os
from pprint import pprint # 더 보기좋게 출력해줌


load_dotenv('/home/ubuntu/airflow/.env')
YOUTUBE_API_KEY = os.getenv('YOUTUBE_API_KEY')

youtube = build('youtube', 'v3', developerKey=YOUTUBE_API_KEY)
# print(youtube)


# handle을 가져와서 channelId로 바꾼 후 채널 검색

# handle을 기준으로 channelId 리턴하는 함수 => channel()
def get_channel_id(youtube, handle):
    # list(part='') : 보고싶은 컬럼에 대한 정보, 필수 옵션
    response = youtube.channels().list(part='id', forHandle=handle).execute()
    # print(response)
    return response['items'][0]['id']

# 유사도로 가장 높은 handle 출력
target_handle = 'coldplay'
channel_id = get_channel_id(youtube, target_handle)
```

## 02. video_id 가져오기
- channel_id를 기준으로 최신영상의 videoId들을 리턴하는 함수
    - search() 사용
```python
def get_latest_video_ids(youtube, channel_id):
    # snippet : 
    response = youtube.search().list(
        part='snippet',
        channelId=channel_id,
        maxResults=5,
        order='date',
    ).execute()

    video_ids = []

    for item in response['items']:
        video_ids.append(item['id']['videoId'])
    
    return video_ids

latest_video_ids = get_latest_video_ids(youtube, channel_id)
# print(latest_video_ids)
```

## 03. comments 가져오는 함수 생성
- video_id를 기준으로 comment를 리턴하는 함수
    - commentThreads() 사용
```python
# 하나의 video_id를 기준으로 comment를 뽑는 함수를 먼저 만들고 반복문을 돌림
from pprint import pprint

def get_comments(youtube, video_id):
    # maxResults=5 : 댓글 5개만 출력
    # order='relevance', # 좋아요가 많은 개수의 댓글 출력
    response = youtube.commentThreads().list(
        part='snippet',
        videoId=video_id,
        textFormat='plainText',
        maxResults=100,
        order='relevance', # 좋아요가 많은 개수의 댓글 출력
    ).execute()
    # pprint(response)

    comments = []

    for item in response['items']:
        # author : 사용자 이름, text : 댓글내용, publishedAt : 작성 시간, likeCount : 좋아요 수, commentID : 댓글 id
        comment = {
            'author': item['snippet']['topLevelComment']['snippet']['authorDisplayName'],
            'text': item['snippet']['topLevelComment']['snippet']['textDisplay'],
            'publishedAt': item['snippet']['topLevelComment']['snippet']['publishedAt'],
            'likeCount': item['snippet']['topLevelComment']['snippet']['likeCount'],
            'commentId': item['snippet']['topLevelComment']['id'],
        }
        comments.append(comment)
    return comments
    
# for video_id in latest_video_ids:
#     result = get_comments(youtube, video_id)
#     print(result)
```

## 04. 최종 형태의 함수 생성
- 위에 구현한 함수를 실행하여 최종 형태 리턴
```python
def get_handle_to_comments(youtube, handle):
    channel_id = get_channel_id(youtube, handle)
    latest_video_ids = get_latest_video_ids(youtube, channel_id)

    all_comments = {}

    for video_id in latest_video_ids:
        comments = get_comments(youtube, video_id)
        all_comments[video_id] = comments

    return {
        'handle': handle,
        'all_comments': all_comments
    }

# get_handle_to_comments(youtube, target_handle)
```

## 05. 하둡에 업로드
- `pip install hdfs`
```python
from hdfs import InsecureClient
from datetime import datetime
import json

def save_to_hdfs(data, path):
    # InsecureClient('저장 경로', 사용자)
    client = InsecureClient('http://localhost:9870', user='ubuntu')

    # 현재 시간을 원하는 시간형태로 만들어줌 -> ex. 2504241142
    current_date = datetime.now().strftime('%y%m%d%H%M')
    file_name = f'{current_date}.json'

    # '/input/yt-date + 2504241144.json
    hdfs_path = f'{path}/{file_name}'

    # 딕셔너리를 json으로 바꾸는 함수
    # ensure_ascii=False : 이모티콘 제거
    json_data = json.dumps(data, ensure_ascii=False)

    with client.write(hdfs_path, encoding='utf-8') as writer:
        writer.write(json_data)

data = get_handle_to_comments(youtube, target_handle)

# save_to_hdfs(저장할 파일, 경로)
# 하둡에 파일 생성 : hdfs dfs -mkdir /input/yt-data
save_to_hdfs(data, '/input/yt-data')
```

## 06. 최종 코드
```python
# pip install google-api-python-client
from googleapiclient.discovery import build
from dotenv import load_dotenv
import os
from pprint import pprint # 더 보기좋게 출력해줌
from hdfs import InsecureClient
from datetime import datetime
import json

load_dotenv('/home/ubuntu/airflow/.env')
YOUTUBE_API_KEY = os.getenv('YOUTUBE_API_KEY')

youtube = build('youtube', 'v3', developerKey=YOUTUBE_API_KEY)
# print(youtube)


# handle을 가져와서 channelId로 바꾼 후 채널 검색

# 1. handle을 기준으로 channelId 리턴하는 함수 => channel()
def get_channel_id(youtube, handle):
    # list(part='') : 보고싶은 컬럼에 대한 정보, 필수 옵션
    response = youtube.channels().list(part='id', forHandle=handle).execute()
    # print(response)
    return response['items'][0]['id']

# 유사도로 가장 높은 handle 출력
target_handle = 'coldplay'
channel_id = get_channel_id(youtube, target_handle)
# print(channel_id)

# 2. channel_id를 기준으로 최신영상의 videoId들을 리턴하는 함수 => search()
def get_latest_video_ids(youtube, channel_id):
    # snippet : 
    response = youtube.search().list(
        part='snippet',
        channelId=channel_id,
        maxResults=5,
        order='date',
    ).execute()

    video_ids = []

    for item in response['items']:
        video_ids.append(item['id']['videoId'])
    
    return video_ids

latest_video_ids = get_latest_video_ids(youtube, channel_id)
# print(latest_video_ids)

# 3. video_id를 기준으로 comment를 리턴하는 함수 => commentThreads()
# 하나의 video_id를 기준으로 comment를 뽑는 함수를 먼저 만들고 반복문을 돌림
def get_comments(youtube, video_id):
    # maxResults=5 : 댓글 5개만 출력
    # order='relevance', # 좋아요가 많은 개수의 댓글 출력
    response = youtube.commentThreads().list(
        part='snippet',
        videoId=video_id,
        textFormat='plainText',
        maxResults=100,
        order='relevance', # 좋아요가 많은 개수의 댓글 출력
    ).execute()
    # pprint(response)

    comments = []

    for item in response['items']:
        # author : 사용자 이름, text : 댓글내용, publishedAt : 작성 시간, likeCount : 좋아요 수, commentID : 댓글 id
        comment = {
            'author': item['snippet']['topLevelComment']['snippet']['authorDisplayName'],
            'text': item['snippet']['topLevelComment']['snippet']['textDisplay'],
            'publishedAt': item['snippet']['topLevelComment']['snippet']['publishedAt'],
            'likeCount': item['snippet']['topLevelComment']['snippet']['likeCount'],
            'commentId': item['snippet']['topLevelComment']['id'],
        }
        comments.append(comment)
    return comments


# for video_id in latest_video_ids:
#     result = get_comments(youtube, video_id)
#     print(result)

# 4. 위에 구현한 함수를 실행하여 최종 형태 리턴
def get_handle_to_comments(youtube, handle):
    channel_id = get_channel_id(youtube, handle)
    latest_video_ids = get_latest_video_ids(youtube, channel_id)

    all_comments = {}

    for video_id in latest_video_ids:
        comments = get_comments(youtube, video_id)
        all_comments[video_id] = comments

    return {
        'handle': handle,
        'all_comments': all_comments
    }

# get_handle_to_comments(youtube, target_handle)


# hdfs에 업로드
# pip install hdfs
def save_to_hdfs(data, path):
    # from hdfs import InsecureClient

    # InsecureClient('저장 경로', 사용자)
    client = InsecureClient('http://localhost:9870', user='ubuntu')

    # from datetime import datetime
    # 현재 시간을 원하는 시간형태로 만들어줌 -> ex. 2504241142
    current_date = datetime.now().strftime('%y%m%d%H%M')
    file_name = f'{current_date}.json'

    # '/input/yt-date + 2504241144.json
    hdfs_path = f'{path}/{file_name}'

    # import json
    # 딕셔너리를 json으로 바꾸는 함수
    # ensure_ascii=False : 이모티콘 제거
    json_data = json.dumps(data, ensure_ascii=False)

    with client.write(hdfs_path, encoding='utf-8') as writer:
        writer.write(json_data)

data = get_handle_to_comments(youtube, target_handle)

# save_to_hdfs(저장할 파일, 경로)
# 하둡에 파일 생성 : hdfs dfs -mkdir /input/yt-data
save_to_hdfs(data, '/input/yt-data')
```

=> airflow로 자동화시키기 \
-> 07_collect_yt_comments.py : 댓글 모으기\
-> 08_yt_data_ETL.py : json파일 csv파일로 바꿔서 저장




![alt text](/airflow/assets/youtube.png)