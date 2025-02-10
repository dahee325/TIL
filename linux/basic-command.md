# 리눅스 기본 명령어

## 0. 명령어의 기본형식
```
command [options] [arguments]
```
- []로 표시하는 이유는 들어갈 수 있고 안들어갈 수도 있음
- command : 실행할 명령어, 프로그램 이름 
- options : 명령어의 옵션(설정값)
- arguments : 명령어에 전달할 인자(사용할 데이터)
- 띄어쓰기 중요

## 1. 파일 및 디렉토리 관리

### ls(list)
- 디렉토리 내용 목록을 보여줍니다.
- options:
     - `-l` : 파일의 상세 정보 표시
     - `-a` : 숨김 파일 표시
     - .은 현재폴더, ..은 상위폴더를 의미

### cd(change directory)
- 현재 작업 디렉토리 변경
- `cd {target-directory}`
    - target-directory는 자동완성 기능을 활용
        - 폴더명의 앞글자만 입력하고 탭을 누르면 파일의 이름을 자동완성시켜줌
        - 앞글자로 시작하는 파일명이 여러개일 경우 이름의 공통된 부분까지 자동완성시켜줌
- cd .. : 상위 폴더로 이동
- cd TIL : TIL폴더로 이동

- 파일 경로의 ~는 내PC/로컬디스크C/사용자/1-13 까지의 위치를 의미

### pwd(print working directory)
- 현재 작업 중인 디렉토리의 전체 경로를 출력

### mkdir(make directory)
- 새로운 디렉토리 생성
- `mkdir {directory-name}`

### touch
- 새로운 파일 생성
- `touch {file-name}`

### rm(remove)
- 파일 삭제
- 폴더는 폴더 안에 파일 또는 폴더가 있을 수 있으므로 지울 수 없음
- options
    - `-r` : 디렉토리와 그 내용을 재귀적으로 삭제

### cat(concatenate)
- 파일의 내용 출력
- 파일이 저장되지 않으면 빈 파일을 출력하므로 파일을 저장하고 출력해야함