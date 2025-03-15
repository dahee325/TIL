# CSS
- [w3school html css](https://www.w3schools.com/html/html_css.asp)
- [mdn html style](https://developer.mozilla.org/ko/docs/Web/HTML/Element/style)
- css : HTML 요소의 스타일을 지정하는 데 사용되는 언어
- 선택자(selector)와 선언블록(declaration block)으로 구성

## 인라인 스타일
- html 요소 내에 style속성 사용
```html
<태그명 style="속성: 속성값;">text</태그명>
```
- 간단히 바로 적용 가능하지만 해당 태그에만 스타일 적용 \
=> 추후 스타일 수정시 해당 부분을 찾아서 따로 수정해야한다는 단점

## 내부 스타일
- `<head>`내에서 `<style>`태그 사용
```html
<head>
    <style>
        태그명 {
            속성: 속성값;
        }
    </style>
</head>
<body>
    <태그명>text</태그명>
</body>
```
- 해당 웹문서 전체에 대하여 스타일 시트 적용

## 외부 스타일
- 별도의 `.css`확장자를 사용하는 스타일 시트 파일 따로 작성
- 외부의 css파일을 `<link>`태그로 html문서에 연결
```html
<head>
    <link rel="stylesheet" href="파일명">
</head>
```

## css파일
- 주석달기 : /* text */
```css
선택자 {속성:속성값; 속성2: 속성값2;}

선택자 {
    속성: 속성값;
    속성2: 속성값2;
}
```

### 기본 선택자
- 태그 선택자 : html 문서 내 스타일을 적용하고자 하는 태그를 선택
```css
태그명 {...}
```
- 클래스 선택자
    - html문서 내 태그 내 `class`속성으로 `클래스명`을 지정하고 해당 클래스 명으로 스타일 적용
    -  특정 요소만 스타일 적용
    - html 문서 내 여러 태그에 동일한 클래스명을 지정하면 동일한 스타일 적용 가능능
```css
.클래스명 {...}
```
- 아이디 선택자
    - html문서 내 `id`속성으로 `아이디명`을 지정하고 해당 아이디명으로 스타일 적용
    - 특정 요소만 스타일 적용
    - 중복되지 않는 고유 요소/영역에 대해 스타일을 적용해줄 때 사용
    - 동일한 웹 문서 내 한번만 적용 가능
```css
#아이디명 {...}
```
- 그룹 선택자 : 여러 선택자에 같은 스타일을 적용할 때 사용, 쉼표로 선택자들 구분
```css
선택자1, 선택자2 {...}
```
- 자식선택자 : `>`기호를 사용하여 특정 요소의 직접적인 자식요소만을 대상으로 스타일 지정
- 예시
```css
div > p {...} /* div의 직접적인 자식요소인 p태그에만 스타일 적용 */
div > #unique {...} /* #unique와 같음 */
div > .a {...} /* div태그 안의 클래스가 a인 것들 */
```
- 하위선택자 : 공백을 사용하여 특정 요소의 모든 하위요소를 대상으로 스타일 지정
-예시 : div내부의 모든 p태그(직접적인 자식요소 뿐만 아니라 그 아래 단계까지 포함)에 스타일 적용
```css
div p {...}
```

### 가상 클래스 선택자
- `.link` : 방문한 적이 없는 링크
- `.visited` : 방문한 적이 있는 링크
- `.first` : 처번째 요소
- `.last` : 마지막 요소
- `.hover` : 마우스를 롤오버 했을 때
- `.active` : 마우스를 클릭 했을 때

## 속성
- `color: 속성값;` : 텍스트 색
- `background-color: 속성값;` : 배경색
- `border: 속성값;` : 테두리 색
    - `border-style: solid` : 실선 테두리
    - `border-style: double` : 이중선 테두리
    - `border-style: dotted` : 점선 테두리

### 텍스트
- `color: 속성값;` : 텍스트 색
- `text-align: 속성값;` : 텍스트의 수평 정렬 설정
    - `text-align: center;` : 가운데 정렬
    - `text-align: left;` : 왼쪽 정렬
    - `text-align: right;` : 오른쪽 정렬렬

### 여백과 패딩
- **여백** : 정의된 테두리 외부의 요소 주위에 공간을 만드는데 사용
- `margin-top: 속성값;` : 위위 여백
- `margin-right: 속성값;` : 오른쪽 여백
- `margin-bottom: 속성값;` : 아래 여백
- `margin-left: 속성값;` : 왼쪽 여백
- 여백의 속성값
    - auto: 브라우저가 여백 계산
    - 길이 : 여백을 px, pt, cm 등으로 지정
    - % : 포함하는 요소 너비의 %로 여백 지정
    - 상속 : 여백이 부모 요소에서 상속되어야함을 지정
- **패딩** : 정의된 테두리 내부의 요소 콘텐츠 주위에 공간을 만드는데 사용
- `padding-top: 속성값;` : 위 패딩
- `padding-right: 속성값;` : 오른쪽 패딩
- `padding-bottom: 속성값;` : 아래 패딩
- `padding-left: 속성값;` : 왼쪽 패딩
- 패딩의 속성값
    - 길이 : px, pt, cm 등으로 패딩 지정
    - % : 포함하는 요소 너비의 %로 패딩 지정
    - 상속 : 여백이 부모 요소에서 상속되어야함을 지정

## flexbox froggy
### justify-content
-  flex요소들을 메인축에서 정렬
- `justify-content: flex-start;` : 요소들을 컨테이너의 왼쪽으로 정렬
- `justify-content: flex-end;` : 요소들을 컨테이너의 오른쪽으로 정렬
- `justify-content: center;` : 요소들을 컨테이너의 가운데로 정렬
- `justify-content: space-between;` : 요소들 사이에 동일한 간격을 둠
- `justify-content: space-around;` : 요소들 주위에 동일한 간격을 둠

### align-items
- flex요소들을 보조축에서 정렬
- `align-items: flex-start;` : 요소들을 컨테이너의 꼭대기로 정렬
- `align-items: flex-end;` : 요소들을 컨테이너의 바닥으로 정렬
- `align-items: center;` : 요소들을 컨테이너의 보조축의 가운데 정렬
- `align-items: baseline;` : 요소들을 컨테이너의 시작 위치에 정렬
- `align-items: stretch;` : 요소들을 컨테이너에 맞게 늘림

### flex-direction
- 정렬할 방향 지정
- `flex-direction: row;` : 요소들을 왼쪽에서 오른쪽으로 정렬
- `flex-direction: row-reverse;` : 요소들을 오른쪽에서 왼쪽으로 정렬
- `flex-direction: column;` : 요소들을 위에서 아래로 정렬
- `flex-direction: column-reverse;` : 요소들을 아래에서 위로 정렬

### order
- flex요소의 순서 지정
- `order: 양의 정수;`
- `order: 0;`
- `order: 음의 정수;`

### align-self
- 개별 요소에 적용할 수 있는 속성
- `align-items`가 사용하는 값들을 인자로 받음

### flex-wrap
- 요소들을 한 줄 또는 여러줄에 걸쳐 정렬
- `flex-wrap: nowrap;` : 모든 요소들을 한 줄에 정렬
- `flex-wrap: wrap;` : 모든 요소들을 여러 줄에 걸쳐 정렬
- `flex-wrap: wrap-reverse;` : 모든 요소들을 여러 줄에 걸쳐 반대로 정렬

### flex-flow
- `flex-direction`과 `flex-wrap`을 간략히한 속성
- 예시 : `flex-flow: row wrap;`

### align-content
- 보조축에 여분의 공간이 있는 경우 flex 컨테이너 사이의 간격 조절
- 한 줄만 있는 경우 효과를 보이지 않음
- `align-content: flex-start;` : 여러 줄들을 컨테이너의 꼭대기에 정렬
- `align-content: flex-end;` : 여러 줄들을 컨테이너의 바닥에에 정렬
- `align-content: center;` : 여러 줄들을 보조축의 가운데에에 정렬
- `align-content: space-between;` : 여러 줄들을 사이에 동일한 간격을 둠
- `align-content: space-around;` : 여러 줄들을 주위에 동일한 간격을 둠
- `align-content: stretch;` : 여러 줄들을 컨테이너에 맞도록 늘림
