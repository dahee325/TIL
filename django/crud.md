# CRUD
- **Create** : 생성 ex.로그인
- **Read**(or retrieve) : 읽기(인출) ex.로그인
- **Update** : 갱신 ex.새로운 사진으로 프로필 바꾸기
- **Delete**(or destroy) : 삭제(파괴) ex.계정탈퇴

- **ORM**(Object-Relational Mapping)
    - O : python 세상
    - RM : SQL 세상

## setting
- `python -m venv venv`
- `source venv/Scripts/activate`
- `.gitignore` 설정 : python, windows, macOS, django

## Django
- `pip install django`
- `django-admin startproject crud .` : 현재 폴더(.)에 crud 프로젝트 생성
- `django-admin startapp posts` : 현재 폴더에 posts **앱 생성**
- `curd/settings.py`에 **앱 등록**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'posts',
]
```
1. `crud/urls.py`에 `index/`경로 추가 => views 선언해야함

2. `crud/urls.py`에 `from posts import views` 선언
```python
from posts import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('index/', views.index),
]
```

3. `posts/views.py`에 `index`함수 정의 => `index.html`파일 만들어야함
```python
def index(request):
    return render(request, 'index.html')
```

4. `post`폴더 안에 `templates`폴더 만들기

5. `templates`폴더 안에 `index.html`파일 생성 후 보여줄 내용 작성
```html
<body>
    <h1>index</h1>
</body>
```

## [CRUD](https://github.com/dahee325/CRUD)
### modeling
- `posts/models.py` : 클래스 정의 => [classField](https://docs.djangoproject.com/en/5.1/ref/forms/fields/)
```python
class Post(models.Model): # models안의 Model클래스 사용
    title = models.CharField(max_length=100) # 글자 저장 필드
    content = models.TextField()
```

### migration
- python세상에서 SQL세상(`db.sqlite3`)으로 이주
- `python manage.py makemigrations` : `posts/migrations/0001_initial.py` 생성 => SQL에 반영할 수 있는 python 문서
- `python manage.py migrate` : DB에 반영
- - Extensions -> `SQLite Viewer` 프로그램 설치\
=> `db.sqlite3`파일 모양이 바뀜\
=> 들어가보면 `posts_post`가 우리가 만든 파일
- 주의 : models.py에서 오타나거나 잘못입력했다면 수정하고 다시 번역본 만들고 read해야함

### 관리자 계정 생성
- `admin/` 계정 생성
```shell
python manage.py createsuperuser
# Username : admin
# Email address : 
# password : 1234
# password(again) : 1234
# y
```
=> - `python manage.py runserver` 실행 후 위에서 만든 아이디와 비밀번호로 로그인하면 관리자 페이지가 뜸(정보를 볼 수 있음)\
=> password는 암호화되어 저장되어있음

### 관리자 페이지에 모델 등록
- `admin.py`
```python
from django.contrib import admin
from .models import Post
# admin과 같은 위치에 있기 때문에 .models
# Register your models here.
admin.site.register(Post)
```

## Read
- `Post`클래스 `views.py`에 불러와서 사용
- 게시물 상세보기 페이지 만들기
1. `urls.py`에 경로 추가
2. `views.py`에 `detail`함수 추가
3. `detail.html`파일 만들기
4. `index/`링크에서 하나의 object링크로 이동(`index.html`)
5. object링크에서 `index/`링크로 돌아가기(`detail.html`)

## Create
- 게시물 생성하기
1. 사용자에게 빈 종이 제공(`urls.py`)
2. 빈 종이에 내용 입력(`new.html`)
3. 입력된 내용 /posts/create/로 전송(`new.html`) : `<form action="/posts/create/">` 추가
4. 전송된 데이터 중 필요한 정보 뽑아내기
(`views.py`)
5. DB에 저장(`views.py`) : `post.save()` 추가
6. 사용자에게 저장된 내용 보여주기(`views.py`)
    - `redirect()` : 사용자가 요청한 URL이 아닌 다른 URL로 이동

## Delete
1. 사용자가 삭제 버튼 클릭\
1-1. `detail.html`에 삭제 버튼 추가
1-2. `urls.py`에 경로 설정
2. 몇 번 게시물을 삭제할지 찾기(`views.py`)
3. 해당 게시물 삭제(`views.py`)

## Update
1. 사용자가 수정 버튼 클릭 => `detail.html`에 수정 버튼 추가
2. 몇 번 게시물을 수정할지 찾기(`urls.py`, `views.py`)
3. 해당 게시물에 전에 제출했던 내용이 들어있어야함(`edit.html`)
4. `/posts/{{post.id}}/update/`로 전송 => `edit.html`파일에 `<form action="/posts/{{post.id}}/update/">`추가
5. 해당 게시물 수정(`views.py`)