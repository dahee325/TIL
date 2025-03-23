# [board](https://github.com/dahee325/board)

## 00. setting
- `python -m venv venv`
- `source venv/Scripts/activate`
- `pip install django`
- `.gitignore` 설정 : python, windows, macOS, django
- `django-admin startproject board .`
- `django-admin startapp articles`
- `/board/settings.py`에 앱 등록


## 중복된 데이터 없애기
1. 고정적으로 사용하는 url 중복된 리소스 없애기(ex. `crud` 프로젝트 경로의 `posts/`)\
    1-1. `include` 불러오기(`urls.py`)
    ```python
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('articles/', include('articles.urls')) # articles폴더 안의 urls파일로 가주세요 => 파일 만들어야함
    ]
    ```

2. `articles/urls.py`만들기\
    2-1. `board/urls.py`에서 연결되는 파일
    ```python
    from django.urls import path
    from . import views # 현재 폴더에 views.py가 같이 있기때문에 .에서 불러옴

    # `board/urls.py`에서 오는 파일이기 때문에 articles/를 입력하지 않아도됨
    urlpatterns = [
        path('', views.index)
    ]
    ```
    2-2. `views.py`
    ```python
    def index(request):
        return render(request, 'index.html')
    ```
3. `articles/templates`폴더 만들기\
    3-1. `articles/templates`폴더 안에 `index.html`파일 만들기

4. 폴더의 최상단에 `templates`폴더 만들기 => `templates`폴더가 총 2개\
    4-1. `templates/base.html`파일 만들기 => `base.html` : 공통 파일, 기준이 됨
    ```python
    <body>
    <h1>base입니다.</h1>

    {% block body %}<!--body블락(빈 종이에 구멍뚫기)-->

    {% endblock}
    </body>
    ```

5. `articles/index.html`
    - `templates/base.html`파일을 가져와서 block안에 index 넣기
    ```html
    {% extends 'base.html' %}

    <!--block이름은 `base.html`의 block이름과 같아야함-->
    {% block body%} 
    <h1>index</h1>
    {% endblock%}
    ```

6. articles파일이 아닌 외부의 templates파일도 연결해달라고 말하기(`board/settings.py`)\
    6-1. `BASE_DIR = Path(__file__).resolve().parent.parent` : 현재 파일(`settings.py`)의 부모(`board/`)파일의 부모(`board/`)파일로 이동
    - `BASE_DIR` : 프로젝트 폴더 자체를 의미, `BASE_DIR / '폴더이름'`으로 사용
    ```python
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [BASE_DIR / 'templates'],
            ...
        }
    ]
    ```

## 모델링
- 클래스 만들기(`articles/models.py`)
```python
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True) # 하나의 Article이 작성된 시간(UTC시간 기준)
    # `auto_now_add=True`` : 하나의 Article이 들어올 때 자동으로 들어오는 정보
```

## 마이그레이션
1. Python을 SQL에 적용시키기
    - `python manage.py makemigrations`
    - `python manage.py migrate` :

2. 관리자 페이지에 들어가서 Article 모델 관리 등록(`articles/admin.py`)

3. 관리자 페이지 들어가기
    - `python manage.py createsuperuser` 


## CRUD 기능 구현
- bootstrap 적용(`templates/base.html`)
    - `base.html`파일에서 수정하면 모두 파일 전부 수정
    ```html
    <head>
        ...
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    </head>
    <body>
        ...
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
    </body>
    ```

## 게시판 만들기
- `<table>`태그(표 만들기) 사용
    - `<thead>` : 테이블의 컬럼
        - `<th>` : 하나의 컬럼
    - `<tbody>` : 테이블의 데이터를 담는 부분
        - `<tr>` : 테이블의 행
        - `<td>` : 테이블의 데이터
- `articles/templates/index.html`
```html
{% extends 'base.html' %}

{% block body%}
    <table class="table">
        <thead>
            <tr>
                <th>제목</th>
                <th>내용</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>임시제목</td>
                <td>임시내용</td>
            </tr>
        </tbody>
    </table>
{% endblock %}
```

### Read
1. 게시물 불러오기(`articles/views.py`)

2. 불러온 게시물 출력하기(`articles/templates/index.html`)

3. 게시물 디자인 바꾸기(`templates/base.html`)
    - Navbar만들고 게시물 컨테이너로 바꾸기
    ```html
    <body>
        <nav class="navbar navbar-expand-lg bg-body-tertiary">
            <div class="container-fluid">
                <a class="navbar-brand" href="#">Board</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
                    <div class="navbar-nav">
                        <a class="nav-link" href="#">Create</a>
                        <a class="nav-link" href="#">Features</a>
                        <a class="nav-link" href="#">Pricing</a>
                    </div>
                </div>
            </div>
        </nav>

        <div class="container">
            {% block body %}<!--body블락(빈 종이에 구멍뚫기)-->
            {% endblock %}
        </div>

        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
    </body>
    ```

4. 상세보기 페이지 만들기\
    4-1.`articles/urls.py`\
    4-2. `articles/views.py`\
    4-3. `articles/templates/detail.html` => `detail.html`파일 만들기

5. Navbar에 링크 달기
6. URL을 변수처럼 사용하기위해 변수이름 설정하기(`articles/urls.py`)
    ```python
    app_name = 'articles'

    # `board/urls.py`에서 오는 파일이기 때문에 articles/를 입력하지 않아도됨
    urlpatterns = [
        path('', views.index, name='index'), # index = '/articles/'
        path('<int:id>/', views.detail, name='detail'),
    ]
    ```
7. 전체 게시물 목록으로 가는 링크달기(`templates.base.html`)
    ```html
    <body>
        ...
            <a class="navbar-brand" href="{% url 'articles:index' %}">Board</a>
            <!--{% url 'app_name:name' %} => url 'articles:index'는 `/articles/`와 같음 -->
        ...
    </body>
    ```

8. 상세보기 페이지로 가는 링크달기(`articles/templates/index.html`)
    ```html
    <tbody>
        {% for article in articles %}
        <tr>
            <td>{{article.title}}</td>
            <td>{{article.content}}</td>
            <td><a href="{% url 'articles:detail' article.id %}" class="btn btn-primary">detail</a></td>
        </tr>
        {% endfor %}
    </tbody>
    ```

### Create
1. 경로설정(`articles/urls.py`)

2. 빈 종이 보여주기\
    2-1. `articles/views.py`\
    2-2. `articles/templates/new.html` => `new.html`파일 만들기\
    2-3. `articles/templates/new.html` : `<label>`과 `<input>`연결하고 `name=""`키값 설정

3. `create/`링크로 보내기(`articles/templates/new.html`)
    ```html
    <form action="{% url 'articles:create' %}">
        ...
    </form>
    ```
4. **REST** : URL에 데이터를 어떻게 처리할지 생략(`articles/templates/new.html`)
    - Read => GET : 데이터 가져오기
    - Create => POST : 데이터 처리하기
    - Update => UPDATE/FACH
    - Delete => Delete
    - URL을 설계하는 방식
    - 
    ```html
    <form action="{% url 'articles:create' %}" method="POST"> <!-- 데이터를 같이 보낼 것, 처리해주세요 -->
        {% csrf_token %} <!--csrf : 사이트간 요청 위조, 데이터를 보낼거니 도장을 위조-->
        ...
    </form>
    ```
5. 데이터 꺼내오기(`articles/views.py`)
    ```python
    from django.shortcuts import render, redirect
    ...
    def create(request):
        title = request.POST.get('title') # hidden token이 생성되었기 때문에 저장되는 위치가 GET에서 POST로 바뀜
        content = request.POST.get('content')

        article = Article()
        article.title = title
        article.content = content
        article.save()
        
        return redirect('articles:detail', id=article.id)
    ```
6. Navbar에 create를 누르면 `new/`로 이동(`templates/base.html`)
    ```html
    <body>
        <nav class="navbar navbar-expand-lg bg-body-tertiary">
            <div class="container-fluid">
                ...
                <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
                    <div class="navbar-nav">
                        <a class="nav-link" href="{% url 'articles:new' %}">Create</a>
                        ...
                    </div>
                </div>
            </div>
        </nav>
        ...
    </body>
    ```

### Delete
1. delete 버튼 만들기(`articles/templates/detail.html`)

2. delete 경로 설정하기(`articles/urls.py`)

3. delete버튼에 `/delete/`링크 연결하기(`articles/templates/detail.html`)

4. delete 기능 만들기(`articles/views.py`)

### Update
1. update 버튼 만들기(`articles/templates/detail.html`)

2. update 경로 만들기(`articles/urls.py`)

3. `edit`, `update` 함수 만들기(`articles/views.py`)

4. `new.html`파일 복사 붙여넣기 해서 `edit.html`으로 이름 바꾸기(`articles/templates/edit.html`)

5. update 버튼에 `/edit/`링크 연결하기(`articles/templates/detail.html`)

6. `/edit/`링크에서 제출을 누르면 `/update/`링크로 이동(`articles/templates/edit.html`)

7. update 기능 구현하기(`articles/views.py`)

### 작성된 시간과 관리자 페이지 한국어로 바꾸기
- `board/settings.py` 107번 줄 ~
```python
# LANGUAGE_CODE = 'en-us'
LANGUAGE_CODE = 'ko-kr' # 언어 바꾸기

# TIME_ZOZNE = 'UTC'
TIME_ZONE = 'Asia/seoul' # 시간 바꾸기

USE_I18N = True

USE_TZ = True
```