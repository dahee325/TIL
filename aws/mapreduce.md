# MapReduce
- 구글에서 개발한 대용량 데이터 처리 프레임워크
- 병렬 처리를 사용하여 데이터 처리 속도를 높임
- Hadoop에서 실행됨

1. Input
2. Splitting
3. Mapping : 필요한 데이터 골라내기
4. Shuffling : Sort라 생각
5. Reducinig
6. Final result

![MapReduce](/assets/mapreduce.png)


## 실습
- window 검색창에 터미널 검색 -> 실행
- `Ubuntu` 새 창으로 열기
- `code .` : vscode로 들어가기 -> 리눅스 컴퓨터로 들어간 것이라고 생각 -> 터미널창이 Ubuntu(WSL)인지 확인
- 하둡 프로그램 들어가기
```shell
cd hadoop-3.3.6/
```
- `dfs`와 `yarn`한번에 시작
```shell
sbin/start-all.sh 
```
- `local:9870` 링크에 들어가서 접속되는지 확인 -> 어제 올려놨던 ratings.csv파일이 하둡에 올라가 있음
- `damf2`폴더 안에 `hadoop`폴더 생성
- `data`폴더로 위치 이동
```shell
cd ~/damf2/data/
```

## Hadoop command
- `ls` : 현재 위치에 있는 모든 폴더와 파일을 보여주는 명령어
- 하둡한테 ls명령어 실행
```shell
hdfs dfs -ls /
hdfs dfs -ls <확인하고싶은 경로>
```
- `mkdir` : 하둡에 폴더 생성
```shell
hdfs dfs -mkdir /input
hdfs dfs -mkdir <생성하고싶은 폴더 이름>
```
- `put` : 파일 업로드
```shell
hdfs dfs -put ml-25m/movies.csv /input
# /input은 하둡이 갖고있는 파일이기 때문에 자동완성이 되지않음
hdfs dfs -put <업로드할 파일 경로> <업로드할 위치>
```
- `cat` : 파일 출력
```shell
hdfs dfs -cat /input/movies.csv
hdfs dfs -cat <출력하고싶은 파일 경로>
```
- `head`, `tail` : 
```shell
hdfs dfs -head /input/movies.csv
hdfs dfs -head <출력하고싶은 파일 경로>

hdfs dfs -tail /input/movies.csv
hdfs dfs -tail <출력하고싶은 파일 경로>
```
- `rm`
```shell
hdfs dfs -rm /ratings.csv
hdfs dfs -rm <지울 파일 경로>

# 폴더를 삭제할 경우 -r 옵션 추가
hdfs dfs -rm -r /input
```

## 설정 바꾸기
- `C:\Windows\System32\drivers\etc\hosts`파일 `vscode`으로 열기\
-> `127.0.0.1 1-13.` 입력 후 저장 `retry as admin`클릭 => 로컬호스트와 똑같이 바라볼 문자를 설정
- 하둡링크에서 `/input`에 `movies.csv`클릭 후 `Head the file (first 32K)`누르면 밑에 `file content`에 데이터가 나옴
![alt text](/assets/hadoop_host.png)


# MapReduce 실습
## 1. Input
### 1-1. 하둡에 input, output 폴더 생성
- `hdfs dfs -mkdir /input`
- `hdfs dfs -mkdir /output`

### 1-2. 데이터 만들기
- `damf2/hadoop`폴더 안에 `0.wordcount`폴더 생성
- `0.wordcount`안에 `mapper.py`, `reducer.py`, `text.txt`파일 생성
- `text.txt` : 아무 데이터 입력
```txt
apple hello world
hello hello apple
world
apple world
hello world
world hello
world
world
apple
```

### 1-3. 하둡에 txt 데이터 올리기
- `cd ../hadoop/0.wordcount/` : `0.wordcount`폴더로 이동
- `hdfs dfs -put text.txt /input` : `text.txt`파일을 하둡의 `input`폴더에 업로드하기 => `Head the file`로 잘 업로드 됐는지 확인

### 참고사항
- HDFS는 분산 저장 구조기 때문에 하둡에 올리면 데이터를 수정할 수 없음 => 추가는 가능
- 수정하면 전체르 삭제하고 다시 업로드해야함


## 2, 3. Splitting & Mapping
- `damf2/hadoop/0.wordcount/mapper.py`
```python
import sys # system

# splitting
for line in sys.stdin: # stdin(standardinput) : 파일 전체
    line = line.strip() # 좌우 공백 없애기
    words = line.split() # 띄어쓰기를 기준으로 단어 쪼개기
    # words에는 리스트의 형태로 ['apple', 'hello', 'world'] 저장되어있음
    
    # mapping
    for word in words:
        print(f'{word}\t1') # \t : 탭(간격두기)
```

- `cat text.txt | python3 mapper.py ` : `|`는 앞에서 출력된 결과를 뒤쪽 명령어의 입력(stdin) 으로 전달\
=> 리눅스에서 돌아가는 코드(하둡 아님)

## 4. Shuffling
- `cat text.txt | python3 mapper.py | sort`

## 5. Reducing
- `damf2/hadoop/0.wordcount/reducer.py`
```python
import sys

# apple 1 -> line1
# apple 1 -> line2
# hello 1
# hello 1
# hello 1
# ....

last_word = None # 글자가 바뀌는 시점
total_count = 0

for line in sys.stdin: 
    word, value = line.split('\t') # 탭을 기준으로 쪼개기
    value = int(value)

    if last_word == word:
        total_count += value
    else:
        if last_word is not None: 
        # None인 경우는 맨 처음, None이 아닌 경우는 단어에서 단어로 이어질 때
            print(f'{last_word}\t{total_count}')
        last_word = word # 글자가 바뀜
        total_count = value # 글자가 바꼈으니까 total_count 값 초기화

if last_word == word:
    print(f'{last_word}\t{total_count}')
```
- `cat text.txt | python3 mapper.py | sort | python3 reducer.py`

# Hadoop으로 실행
## jar 제출
- `hadoop jar ~/hadoop-3.3.6/share/hadoop/tools/lib/hadoop-streaming-3.3.6.jar` -> 추가로 4가지 인자를 입력해줘야함
    - input, output, mapper, reducer의 경로를 알려줘야함(풀로 알려줘야함)
- 최종코드 => 에러가 생기는게 맞음(몇 가지 설정을 해줘야함)
```shell
hadoop jar ~/hadoop-3.3.6/share/hadoop/tools/lib/hadoop-streaming-3.3.6.jar 
-input /input/text.txt 
-output /output/wordcount 
-mapper /home/ubuntu/damf2/hadoop/0.wordcount/mapper.py 
-reducer /home/ubuntu/damf2/hadoop/0.wordcount/reducer.py
```