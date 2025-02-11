# Git 기본 개념

## 분산버전 관리 시스템
- 클라이언트(나)와 서버 모두(github)가 똑같은 데이터를 유지하여 버전을 관리하는 시스템

## 파일의 세 가지 상태

![areas](../assets/areas.png)

- 영역
    - working directory : 작업 영역, 작성하고 있는 코드, 파일(이미지)가 저장되는 공간
    - staging area : add 명령어로 파일들이 올라가는 공간('git'이 관리)
    - .git directory(respository) : commit 명령어로 찍힌 스냅샷들을 저장하는 공간('git'이 관리)
    - 꼭 add를 하고 commit을 해야함

## 파일의 라이프사이클
- ![lifecycle](../assets/lifecycle.png)

- Tracked(관리대상임), Untracked(관리대상이 아님)
    - Tracked 파일 : 은 이미 스냅샷에 포함돼 있던 파일
    - Unmodified(수정하지 않음), Modified(수정함), Staged(커밋으로 저장소에 기록할) Git이 알고 있는 파일- Untracked 파일 : 워킹 디렉토리에 있는 파일 중 스냅샷에도 Staging Area에도 포함되지 않은 파일