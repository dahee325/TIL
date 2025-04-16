- `hadoop-3.3.6/sbin/start-all.sh` : hadoop 실행
- `hbase-2.5.11/bin/start-hbase.sh` : hbase 실행
- `damf2/hbase/main.py`파일 생성

### python 3.11.12 install
- pyenv -> 파이썬을 여러버전 설치할 수 있게 도와줌
- `pyenv install 3.11.12` : python 11의 최근 버전 설치
- `pyenv versions` : 설치된 파이썬 버전 확인
- `python -V` : 파이썬 버전 확인 => 3.13.2로 되어있음
- `pyenv global 3.11.12` : 3.11.12버전으로 바꾸기
- `python -V` : 3.11.12로 바뀌어있음
- `pyenv global 3.13.2` : 다시 바꿈
- `pyenv local 3.11.12` : `damf2/hbase`안에서는 3.11.12를 사용하고 나머지 공간에서는 3.13.2사용 => hbase폴더 안에 `.python-version`파일 생성됨

### setting
- `python -m venv venv` : `damf2/hbase`위치에서 python 3.11.12에서 가상환경 설정
- `source venv/bin/activate` : 활성화

## [FastAPI](https://fastapi.tiangolo.com/ko/)
- hbase와 연결할 것
- django와 db를 연결한 것처럼 fastapi를 통해 요청을 받고 hbase에 저장
- fastapi와 hbase를 연결해주는 프로그램 => [HappyBase](https://happybase.readthedocs.io/en/latest/)

### install
- `pip install fastapi uvicorn`
- `damf2/hbase/main.py`
```python
from fastapi import FastAPI

app = FastAPI()

@app.get('/')   # => urls.py
def index():    # => views.py, 위의 링크로 요청이 들어왔을 때 함수 실행
    return {'hello': 'world'}
```

### fastAPI 서버 실행
- `uvicorn main:app --reload` => 8000으로 열려야되는데 안되면 밑의 코드 실행
- `uvicorn main:app --reload --port 9999` => fastapi는 원래 8000번 포트인데 안되니까 임의로 9999로 실행
- `http://127.0.0.1:9999/docs`로 들어가보면 docs파일을 자동으로 만들어줌
- 새로운 WSL 터미널 창 `cd hbase-3.5.11`로 위치 이동

### thrift(Hbase) 서버 실행
- `$HBASE_HOME/bin/hbase-daemon.sh start thrift`

## [HappyBase](https://happybase.readthedocs.io/en/latest/)
- python 코드를 쓰면 hbase코드로 바꿔줌
- `pip install happybase` : 설치
- `damf2/hbase/main.py`
```python
from fastapi import FastAPI
import happybase

# thrift(Hbase)
connection = happybase.Connection('localhost', port=9090)
connection.open()

app = FastAPI()

@app.get('/')   # => urls.py
def index():    # => views.py, 위의 링크로 요청이 들어왔을 때 함수 실행
    return {'hello': 'world'}
```
- `uvicorn main:app --reload --port 9999` : 에러가 안뜨면 성공

## hbase shell
- `hbase shell` : shell 켜기
- `list`를 입력했을 때 전에 만든 chatrooms와 messages가 나와야함\
=> 안나오면 `bin/stop-hbase.sh`로 멈춘 후 `bin/start-hbase.sh`다시 실행\
=> 그래도 안되면 start가 두번 켜져있으면 `kill -9 24418`(포트 번호는 2번 켜졌을 때 밑에 나와있음)로 hbase 강제 종료\
=> `bin/start-hbase.sh` 다시 실행\
=> 그래도 안되면 `sudo vi /etc/hosts`에 들어가서 `127.0.0.1    1-13`이 사라졌는지 확인\
=> 사라졌으면 다시 hbase실행하고 `hbase shell`실행
```shell
# chatroom, messages 테이블 삭제
disable 'chatroom'
drop 'chatroom'
disable 'messages'
drop 'messages'
# 테이블 생성
create 'chatrooms', 'info'
create 'messages', 'info'
```
- `damf2/hbase/main.py`
```python
# pip install fastapi uvicorn happybase
from fastapi import FastAPI
import happybase
from pydantic import BaseModel 

# 사용자한테 입력받은 데이터가 room_name을 포함하고 있는지
class Chatroom(BaseModel):
    room_name: str

# thrift(Hbase)
connection = happybase.Connection('localhost', port=9090)
connection.open()

app = FastAPI()

# 채팅방 생성
@app.post('/chatrooms')   # => post요청으로 들어옴
def create_chatroom(chatroom: Chatroom):
    table = connection.table('chatrooms')
    table.put()

# 채팅방 조회
@app.get('/chatrooms')
def get_chatrooms():
    pass

# 메세지 작성
@app.post('/messages')
def create_message():
    pass

# 채팅방 안의 메세지 조회
@app.get('/chatrooms/{room_id}/messages')
def get_messages():
    pass
```
- `pip install pydantic` : 설치 -> fastAPI설치할 때 이미 설치됨
- `uvicorn main:app --reload --port 9999` => `localhost:9999/docs`에 들어가서 테이블 생성됐는지 확인
![alt text](/hbase/assets/create_table.png)
- room_name을 설정해서 execute로 보내면 입력한 정보를 create_chatroom함수의 chatroom변수로 받음
![alt text](/hbase/assets/room_name.png)
- uuid(Universally Unique Identifier) : 세계적으로 고유한 식별자
```python
from fastapi import FastAPI
import happybase
from pydantic import BaseModel
import uuid

# 사용자한테 입력받은 데이터가 room_name을 포함하고 있는지
class Chatroom(BaseModel):
    room_name: str

# thrift(Hbase)
connection = happybase.Connection('localhost', port=9090)
connection.open()

app = FastAPI()

# 채팅방 생성
@app.post('/chatrooms')   # => post요청으로 들어옴
def create_chatroom(chatroom: Chatroom):
    table = connection.table('chatrooms')
    chatroom_id = str(uuid.uuid4()) # uuid는 바이트로 생성되므로 str로 바꿈

    table.put(chatroom_id, {'info:room_name': chatroom.room_name})

    return {
        'chatroom_id': chatroom_id, 
        'room_name': chatroom.room_name
    }
```
![alt text](/hbase/assets/chatroom_POST.png) => 에러가 안나면 성공
```python
@app.get('/chatrooms')
def get_chatrooms():
    table = connection.table('chatrooms')
    rows = table.scan()

    result = []

    for k, v in rows:
        result.append(
            {
                'chatroom_id': k,
                'room_name': v[b'info:room_name'],
            }
        )
    return result
```
![alt text](/hbase/assets/chatroom_GET.png)
```python
from datetime import datetime

class Message(BaseModel):
    room_id: str # 없어도 상관없긴함
    content: str

...
# 메세지 작성
@app.post('/messages')
def create_message(message: Message):
    table = connection.table('messages')
    room_id = message.room_id
    timestamp = int(datetime.now().timestamp()*1000)
    message_id = f'{room_id}-{timestamp}'

    table.put(message_id, {'info:content': message.content, 'info:room_id': room_id})

    return {
        'message_id': message_id,
        'room_id': room_id,
        'content': message.content,
    }
```
![alt text](/hbase/assets/message_POST.png) => chatroom GET에서 chatroom_id복사해서 message POST에 입력하면 위처럼 결과가 나와야함
```python
# 채팅방 안의 메세지 조회
@app.get('/chatrooms/{room_id}/messages')
def get_messages(room_id: str):
    table = connection.table('messages')
    prefix = room_id.encode('utf-8')

    rows = table.scan(row_prefix=prefix)

    result = []
    for k, v in rows:
        result.append(
            {
                'message_id': k,
                'room_id': v[b'info:room_id'],
                'content': v[b'info:content'],
            }
        )
    return result
```
![alt text](/hbase/assets/message_GET.png) => room_id를 입력했을 때 위처럼 뜨면 성공, 먼저 쓴 댓글이 맨 위에 뜸
