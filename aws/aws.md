## AWS
- 로그인(회원가입)
- 빌리는 컴퓨터의 위치 -> 서울
- `ec2` 검색 => 대시보드

### DNS
- 내가 원하는 도메인네임을 쓸려면 dns를 사용해야함\
=> 사야함, godday로 가격확인

- [강사님 notion](https://echo-edu.notion.site/deploy-1cb82d1dd35f80d98a3ddb9368bb193c)


# setting
## 도메인 허용
- 어떤 서비스가 장고에 접근할 수 있는지 설정 : `'*'`도 가능
- `insta/settings.py`
```python
ALLOWED_HOSTS = [
    '.compute.amazonaws.com',
]
```
- `insta.urls.py`
```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('posts/', include('posts.urls')),
    path('accounts/', include('accounts.urls')),

    path('', views.index) # 추가 => http://127.0.0.1:8000/ 에 들어가도 바로 index페이지가 뜸
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

## 의존성 저장 -> git push
- `pip freeze > requirements.txt`
- 커밋 남기고 github에 push 해야함


# EC2
- `ec2` 검색 => 대시보드 => 인스턴스 시작
- **이름 및 태그** => yangsta
- **애플리케이션 및 OS 이미지** : 컴퓨터 빌릴 때 어떤 운영체제를 사용할지 => `Ubuntu`
- **인스턴스 유형** => t2micro(더 줄이고 싶으면 t2nano로 바꾸삼, 근데 그냥 쓰셈)
- **키페어(로그인)** -> 키 페어 생성 : 접근 열쇠 => ydh로 사용 / 처음에만 생성
- **인스턴스 시작** => 모든 인스턴스 보기 누르면 생성된 거 알 수 있음


## 인스턴스 설정
- 이름 옆 인스턴스 id 클릭
1. 보안
2. 보안그룹(컴퓨터 접근 설정)
3. 인바운드 : 컴퓨터로 들어오는 요청 허가
4. 인바운드 규칙 편집
5. 인바운드 규칙 추가
6. 포트 `80` 소스 `0.0.0.0/0` 추가
7. 포트 `80` 소스 `::/0` 추가


## SSH : 접근
- 확장프로그램 설치 : vscode -> extensions -> `ssh`검색 -> `Remote_SSH` : 접속할 수 있게 도와주는 프로그램
- 바탕화면에 `key`폴더 생성 -> `.pem`파일(키파일) `key`폴더로 이동
- 파일 속성 -> 보안 -> 편집 -> 모든 시스템 수정 거부 체크 -> 허용 => 키를 수정하지 못하게 바꿔주는 설정


## Remote Explorer
- vscode의 맨 왠쪽에 `Remote Explorer`생김
1. `Remote Explore`
2. `SSH`폴더 옆의 플러스 클릭
3. `ssh -i ~/Desktop/key/ydh.pem ubuntu@13.209.70.208` 입력 -> 퍼블릭 IP사용
4. linuxs -> countinue
5. open folder -> ok => 빈 컴퓨터 환경


## python 설치
- `python -V`해보면 python이 설치되어있지 않음
- 강사님 링크 확인 -> 복붙
- `.pyenv`설치 => python을 설치할 수 있게 도와줌
- 링크 따라하다가 github 클론해오면
- `python -m venv venv`
- `source venv/bin/activate` : 리눅스 환경이여서 `Scripts`가 `bin`으로 바뀜
- `pip install -r requirements.txt`
- `python manage.py migrate` : 이미 migration파일이 만들어져있으므로 makemigrations안해도됨


# nginx 설치
- `pip install uwsgi` : nginx와 django연결