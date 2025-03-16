# bootstrap
- [부트스트랩](https://getbootstrap.com/docs/5.3/getting-started/introduction/) : 반응형 웹 디자인, 오픈소스 CSS 라이브러리
-  휴대폰에서 대형 데스크톱에 이르기까지 모든 기기에서 보기 좋게 보이도록 자동으로 조정하는 웹사이트를 만드는 것
- 일괄된 디자인 적용을 위해 기본 html태그들의 기본 css속성들을 재정의하거나 수정된 초기값을 사용
1) html 파일 만들고 기본구조 만들기
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>bootstrap</title>
    </head>
    <body>
        <h1>welcome bootstrap</h1>
    </body>
</html>
```
2) bootstrap의 css와 js 포함 - Include via CDN <link>는 <head>에 <script>는 <body>에 넣기
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>bootstrap</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    </head>
    <body>
        <h1>welcome bootstrap</h1>
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
    </body>
</html>
```
- 사용방법
```html
<태그 class="bs-class1 bs-class2 ...">
```

## 컨테이너(Container)
- 화면 구성요소들을 묶기 위한 틀
- `<div>`태그들을 중첩해 사용하는 형식
- `container` : 반응형으로 동작하며 고정폭을 가지고 있음
- `container-fluid` : 전체 폭을 모두 차지

## 그리드(Grid)
- 테이블과 유사하게 화면을 분할해 원하는 레이아웃을 잡기위해 정의된 것
- 12개의 컬럼 구조 사용
- `row`와 `col-*-*`클래스를 사용해 화면크기에 따른 컬럼과 로우 지정하고 부가적인 유틸리티 클래스로 조정
```html
<!--동일한 width를 가지는 컬럼을 정의-->
<div class="row">
    <div class="col-">extra small devices</div>
    <div class="col-sm-1">small devices</div> <!--sm사이즈의 1컬럼 크기-->
    <div class="col-md-2">medium devices</div> <!--md사이즈의 2컬럼 크기-->
    <div class="col-lg-3">large devices</div> <!--lg사이즈의 3컬럼 크기-->
    <div class="col-xl-4">xlarge devices</div>
    <!--xl사이즈의 4컬럼 크기-->
</div>
```
- 하나의 row에 모든 컬럼을 배치하려면 구성 컬럼 크기의 합이 반드시 12컬럼 이하여야 하며 초과될 경우 다음 row로 넘어감

## Utilities
### 문자색(Colors)
```html
<p class="text-primary">.text-primary</p> <!--파랑색-->
<p class="text-secondary">.text-secondary</p> <!--회색-->
<p class="text-success">.text-success</p> <!--초록색-->
<p class="text-danger">.text-danger</p> <!--빨강색-->
<p class="text-warning">.text-warning</p> <!--노랑색-->
<p class="text-info">.text-info</p> <!--하늘색-->
<p class="text-light bg-dark">.text-light</p>
<p class="text-dark">.text-dark</p> <!--검정색-->
<p class="text-body">.text-body</p> <!--기본 본문 색상, 대개 검정색-->
<p class="text-white bg-dark">.text-white</p> <!--흰색 글자에 검정색 배경-->
<p class="text-black-50">.text-black-50</p> <!--검정색 50% 불투명도 추가-->
<p class="text-white-50 bg-dark">.text-white-50</p> <!--흰색 글자에 50% 불투명도 추가, 검정색 배경-->
```

### 배경색(Background)
```html
<div class="p-3 mb-2 bg-primary text-white">.bg-primary</div> <!--파랑색 배경에 흰색 글자-->
<div class="p-3 mb-2 bg-secondary text-white">.bg-secondary</div> <!--회색 배경에 흰색 글자-->
<div class="p-3 mb-2 bg-success text-white">.bg-success</div> <!--초록색 배경에 흰색 글자-->
<div class="p-3 mb-2 bg-danger text-white">.bg-danger</div> <!--빨강색 배경에 흰색 글자-->
<div class="p-3 mb-2 bg-warning text-dark">.bg-warning</div> <!--노랑색 배경에 검정색 글자-->
<div class="p-3 mb-2 bg-info text-white">.bg-info</div> <!--하늘색 배경에 흰색 글자-->
<div class="p-3 mb-2 bg-light text-dark">.bg-light</div>
<div class="p-3 mb-2 bg-dark text-white">.bg-dark</div> <!--검정색 배경에 흰색 글자-->
<div class="p-3 mb-2 bg-white text-dark">.bg-white</div> <!--흰색 배경에 검정색 글자-->
<div class="p-3 mb-2 bg-transparent text-dark">.bg-transparent</div> <!--투명배경에 검정색 글자-->
```

## 컴포넌트(Components)
- [컴포넌트](https://getbootstrap.com/docs/4.3/components/alerts/) : 화면 구성에 필요한 요소들을 정의해둔 클래스 집합

### [Button](https://getbootstrap.com/docs/4.3/components/buttons/)
```html
<button type="button" class="btn btn-primary">Primary</button> <!--파랑색-->
<button type="button" class="btn btn-secondary">Secondary</button> <!--회색-->
<button type="button" class="btn btn-success">Success</button><!--초록색-->
<button type="button" class="btn btn-danger">Danger</button> <!--빨강색-->
<button type="button" class="btn btn-warning">Warning</button> <!--노랑색-->
<button type="button" class="btn btn-info">Info</button> <!--하늘색-->
<button type="button" class="btn btn-light">Light</button> <!--흰색-->
<button type="button" class="btn btn-dark">Dark</button> <!--검은색-->

<button type="button" class="btn btn-link">Link</button> <!--링크연결-->
```

### [Card](https://getbootstrap.com/docs/4.3/components/card/)
- 이미지, 텍스트, 목록 그룹, 링크 등 다양한 콘텐츠 지원
- `card-body` : 카드의 구성요소는 `card-body`카드 안에 패딩 섹션이 필요할 때마다 사용
- `card-title` : 카드 제목
- `card-text` : 카드에 텍스트 추가
- `card-img-top` : 카드 상단에 이미지 배치
```html
    <div class="card m-3" style="width: 18rem;">
        <img src="hello.jpg" class="card-img-top" alt="...">
        <div class="card-body">
          <h5 class="card-title">Card title</h5>
          <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
          <a href="#" class="btn btn-danger">Go somewhere</a>
        </div>
    </div>
```

### Navbar
- 여러 컴포넌트를 묶어 구성
- `navbar-brand` : 회사, 제품 또는 프로젝트 이름 입력
- `navbar-nav` : 전체 높이와 가벼운 탐색 기능
- `navbar-toggler` : 축소 플러그인 및 기타 탐색 전환 동작과 함께 사용

### Nav
- 메뉴 시스템(네비게이션) 구성을 위한 컴포넌트

### [Dropdown](https://getbootstrap.com/docs/4.3/components/dropdowns/)
- 링크 목록 등을 표시하기 위한 토글 가능한 컨텍스트 오버레이
