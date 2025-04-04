# ModelForm
-  데이터와 데이터를 입력받을 수 있는 폼을 합쳐놓은 것

## 00. settings
- `python -m venv venv`
- `source venv/Scripts/activate`
- `pip install django`
- `.gitignore` : python, windows, macOS, django
- `django-admin startproject modelForm .`
- `django-admin startapp articles`
- `modelForm/settings.py` : `articles`앱 등록

## 01. Django
### Modeling
- `articles/models.py`
```python
from django.db import models

# Create your models here.
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True) # 처음 게시물이 작성된 시간
    updated_at = models.DateTimeField(auto_now=True) # 수정된 시간, auto_now=True : 데이터가 저장되는 순간들을 계속해서 저장
```

### Migration
- `python manage.py makemigrations`
- `python manage.py migrate`

### admin에 Article추가
- `articles/admin.py`
```python
from django.contrib import admin
from .models import Article

# Register your models here.
admin.site.register(Article)
```

### superuser 등록
- `python manage.py createsuperuser` : admin, , 1234, 1234, y

### 공통 base.html 설정
- 폴더의 최상단(`MODELFORM/`)에 `templates`파일 생성 => `modelForm/settings.py`에 등록
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR/'templates'],
        ...
    },
]
```
- `MODELFIRM/templastes`폴더에 `base.html`파일 생성
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>여기는 base</h1>
    {% block body %}

    {% endblock %}
</body>
</html>
```

### 기본 url설정
- `modelform/urls.py`
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', include('articles.urls')),
]
```

## 02. Read(ALL)
- `articles/urls.py`생성
```python
from django.urls import path
from . import views

app_name = 'articles'

urlpatterns = [
    # Read
    path('', views.index, name='index'),
]
```

- `articles/vews.py` : 전체 게시물 불러오기
```python
from django.shortcuts import render
from .models import Article

# Create your views here.

def index(request):
    articles = Article.objects.all()
    context = {
        'articles': articles
    }
    return render(request, 'index.html', context)
```

- `articles/`dp `templates`폴더 생성 => `index.html`파일 생성
```html
{% extends 'base.html' %}

{% block body %}
    {% for article in articles %}
        <h3>{{article.title}}</h3>
        <p>{{article.content}}</p>
        <hr>
    {% endfor %}
{% endblock %}
```

## 03-1. CREATE(Ver.1) : 모델폼 만들기
- `articles/forms.py`파일 생성
```python
from django.forms import ModelForm
from .models import Article

class ArticleForm(ModelForm):
    class Meta():
        model = Article
        fields = '__all__'
```

- `articles/urls.py`
    - 지금까지 경로를 `new/`와 `create/`로 나눠서 처리했다면 오늘은 **`create/`에서 모두 처리**\
    => if문 사용
```python
urlpatterns = [
    ...
    # Create
    path('create/', views.create, name='create'), 
]
```

- `articles/views.py`
```python
from django.shortcuts import render
from .models import Article
from .forms import ArticleForm
...

def create(request):
    # new/ => 빈 종이를 보여주는 기능
    # create/ => 사용자가 입력한 데이터 저장
    # -------------------------------------
    # GET create/ => 빈 종이를 보여주는 기능
    # POST create/ => 사용자가 입력한 데이터 저장
    # => create/로 합쳐짐

    if request.method == 'POST':
        pass
    else: # 먼저 실행됨
        form = ArticleForm()
        context = {
            'form': form,
        }
        return render(request, 'create.html', context)
```

- `articles/templatees`폴더에 `create.html`파일 생성
```html
{% extends 'base.html' %}

{% block body %}
    <form action="" method="POST">
    <!--action=""을 비워두면 현재 위치로 다시 보냄(대신 GET에서 POST로 바뀜)
    => create함수의 if문으로 이동-->
        {% csrf_token %}
        {{form}}
        <input type="submit">
    </form>
{% endblock %}
```

- `articles/views.py` : if문 완성
```python
from django.shortcuts import render, redirect
...

def create(request):
    # new/ => 빈 종이를 보여주는 기능
    # create/ => 사용자가 입력한 데이터 저장
    # -------------------------------------
    # GET create/ => 빈 종이를 보여주는 기능
    # POST create/ => 사용자가 입력한 데이터 저장
    # => create/로 합쳐짐

    if request.method == 'POST':
        form = ArticleForm(request.POST) # request.POST의 딕셔너리 값을 html으로 바꿔줌
        if form.is_valid(): # validation : form에 있는 데이터가 유효한가요?
            form.save() # 있다면 저장
            return redirect('articles:index')
        else:
            context = {
                'form': form, # 이미 데이터가 들어가있는 form을 사용자에게 보여줌
            }
            return render(request, 'create.html', context)

    else: # 먼저 실행됨
        form = ArticleForm()
        context = {
            'form': form,
        }
        return render(request, 'create.html', context)
```

## 03-2. CREATE(Ver.2)
- `articles/views.py`
```python
def create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST) # request.POST의 딕셔너리 값을 html으로 바꿔줌
        if form.is_valid(): # validation : form에 있는 데이터가 유효한가요?
            form.save() # 있다면 저장
            return redirect('articles:index')

    else: # 먼저 실행됨
        form = ArticleForm()

    # else일 경우 항상 실행
    context = {
        'form': form,
    }
    return render(request, 'create.html', context)
```

**모든 경우의 수**
- GET : form을 만들어서 html 문서를 사용자에게 리턴
- POST : invalid data (데이터 검증 실패)
- POST : valid data (데이터 검증 성공)
```python
def create(request):

    # 5. POST 요청(invalid data)
    # 10. POST 요청(valid data)
    if request.method == 'POST':
        # 6. 사용자가 입력한 데이터(request.POST)를 담은 form 생성(invalid, 유효하지않음)
        # 11. 사용자가 입력한 데이터(request.POSt)를 담은 form 생성(valid, 유효함)
        form = ArticleForm(request.POST)

        # 7. form 검증(실패)
        # 12. form 검증(성공)
        if form.is_valid():
            # 13. form 저장
            form.save()
            # 14. index로 redirect
            return redirect('articles:index')

    # 1. GET 요청
    else:
        # 2. 비어있는 form을 만든다.
        form = ArticleForm()

    # 3. context dict에 비어있는 form을 담는다.
    # 8. context dict에 검증에 실패한 form을 담는다.
    context = {
        'form': form,
    }

    # 4. create.html 랜더링
    # 9. create.html 랜더링 - 잘못 작성한 데이터만 잘못 적으라고 알려줌
    return render(request, 'create.html', context)
```