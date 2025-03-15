# HTML
- html :  웹 페이지의 콘텐츠와 구조를 정의하는 데 사용되는 마크업 언어, `<tag>`를 사용하여 구조화
- [W3 school html](https://www.w3schools.com/tags/default.asp)
- [mdn web docs html](https://developer.mozilla.org/en-US/docs/Web/HTML)
- 주석달기(ctrl + /) `<!--주석-->`
```html 
<태그명 속성명1="속성값1" 속성명2="속성값2"> 내용 </태그명>
```
- html의 기본구조(! + tab)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    
</body>
</html>
```
- `<head> </head>` : 설정하는 공간
    - `<title> </title>` : 웹페이지의 제목
- `<body> </body>` : 실제로 보여지는 내용

## text 태그
- 제목태그 : `<h1> text </h1>` ~ `<h6> text </h6>` => 숫자가 높아질수록 글자 크기가 작아짐
- `<p> text </p>` : 단락 정의
- `<hr>` : 수평줄 만들기, 주제를 변경할 때 사용
- `<strong> text </strong>` : 굵게, 시멘틱 태그 -> 강조하기 위해 사용, 포함된 콘텐츠의 특정 의미를 정의하고 목적을 갖는 태그
- `<b> text </b>` : <strong>태그와 같게 단어를 굵게
- `<a href="URL" target="_blank"> text </a>` : 하이퍼링크 정의
    - `href="URL"` : 어떤 주소와 연결할지 저장
    - `target="_balnk` : 새로운 창으로 주소창 열기
- `<img scr="이미지 경로" alt="text">`  : 이미지 태그, 닫는 태그 없음
    - `scr="이미지 경로"` : 이미지 경로 저장
    - `alt="text"` : 이미지가 문제있을 때 이미지 대신 보여주는 문구

## 리스트 태그
- `<ul> </ul>` : 순서가 없는 리스트를 만들 때 사용
- `<ol> </ol>` : 순서가 있는 리스트를 만들 때 사용(번호를 매겨줌)
    - `<li> </li>` : 리스트로 작성