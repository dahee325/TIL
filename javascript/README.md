# JS(Java Script)
- [mdn](https://developer.mozilla.org/ko/docs/Learn_web_development/Getting_started/Your_first_website/Adding_interactivity)
- 객체 기반 언어, 동적 프로그래밍 언어


#  파일 연결
- `scripts/main.js` 최상단에 폴더와 파일 만들기
- `index.html` : `main.js`파일 연결
```html
    <script src="scripts/main.js"></script>
</body>
```
- `scripts/main.js`
```js
let myHeading = document.querySelector('h1')
myHeading.textContent = 'Hello World!'
```
=> html파일을 순서대로 코드진행\
=> `index.html`에 `main.js`를 코드 마지막에 연결\
=> `index.html`에 제목을 `Mozilla is cool`이라고 되어있지만 `main.js`의 `<h1>`를 만나 `myHeading`안에 `<h1>Mozilla is cool</h1>`가 들어있고 그 `content`인 `Mozilla is cool`를 수정


# 출력
- `scripts/main.js`
```js
var myVariable = 10

console.log(myVariable) //관리자창의 Consle에서 출력 => print문이라고 생각
```

- [jsonplaceholder](https://jsonplaceholder.typicode.com/) : fake placeholder


# JAVA SCRIPT
## 1. 변수 선언 + 값 할당
- 변수 선언 : `var myVariable`
- 값 할당 : `myVariable = 10`\
=> 변수 선언하고 값 할당을 따로 할 수 있음
- 변수선언 + 값할당 : `var myVariable = 10`

### ES6 전 문법
- var : 유연하게 동적으로 동작
```javascript
var myV1 = 1
```
- 재할당 O , 재선언 O
```javascript
myV1 = 10
var myV1 = 100
```

### ES6 이후 문법
- `let` : 재할당 O, 재선언 X
```js
let myV2 = 2

myV2 = 20
// let myV2 = 20
```
- `const` : 재할당 X, 재선언 X
```js
const myV3 = 3

// myV3 = 30
// const myV3 = 300
```

# 2. 변수 유형
```js
let a = 'bob' //string
let b = 10 //number
let c = true //bolean : 소문자 사용
let d = [1, 2, 3] //array
let d = {       // object, python의 class와 비슷
    'apple':, '사과',
}
```

# 3. + 계산
- `console.log(1+2)` : 더하기
- `console.log('Hello' + 'World!')` : 연결 -> concatnation

# 4. 등호
```js
let varA = '10' // string
let varB = 10 // number
```
- `console.log(varA == varB)`=> true : 값이 같은지
- `console.log(varA === varB)`=> false : 값과 타입까지 완전히 같은지
- `console.log(varA !== varB)`=> true : 값과 타입까지 완전히 다른지

# 5. Array
- `Array` : 여러개의 데이터 쌓기, 추가, 제거
```js
let myArray = []
myArray.push('hello') // 데이터 추가, python의 append와 같다고 생각
myArray.push('world!')
console.log(myArray)

myArray.pop() // 마지막 데이터 제거, stack구조
console.log(myArray)

console.log(myArray[0]) // 인덱스 접근 가능
```

# 6. Object
- python 의 class + dictionary라 생각
- json(JavaScript Object Nation) 구조
```js
let myObject = {
    'apple': '사과',
}
console.log(myObject.apple) // python 에서 class변수에 접근하는 방식과 같음

myObject.grape = '포도' // `'grape': '포도'`인 데이터 추가
```

# 7. 조건문 : if
```js
if (조건문1) {
    조건문1이 참일 경우 실행할 코드
} else if (조건문2) {
    조건문2가 참일 경우 실행할 코드
} else {
    조건문1, 2가 거짓일 경우 실행할 코드
}
```
```js
let icecream = 'vanila'
if (icecream == 'chocolate') {
    console.log('I love chocolate')
} else if (icecream == 'vanila') {
    console.log('I love vanila')
} else {
    console.log('I love strawberry')
}
```

# 8. 반복문
## 8-1. while
```js
console.log('--while--') // while문 실행하라는 코드

let i = 0
while (i < 5) {
    console.log(i)
    i++ // i = i + 1 (python)
}
```

## 8-2. for
```js
for (변수 초기화; 조건식; 조건식이 참일 경우 실행할 코드) {console.log()}
```
- Ver1. while문 축약
```js
console/log('---for1---')
for (let i = 0; i < 5; i++) {
    console.log(i)
}
```
- Ver2. 데이터를 꺼내오는 방법
```js
let arrayA = ['a', 'b', 'c']
console.log('---for2---')
for (let i = 0; i < arrayA.length; i++) { // arrayA.length : python의 len(arrayA)와 같음
    console.log(arrayA[i])
}
```
- `for in` : 값을 하나씩 출력 -> item으로 하면 인덱스 번호만 출력
```js
console.log('---for in---')
for (le index in arrayA) {
    console.log(index, arrayA[index])
}
```
- `for of` : python과 동일하게 값만 출력
```js
console.log('---for of---')
for (let item of arrayA) {
    console.log(item)
}
```
- `forEach` : python의 `map`과 비슷
```js
console.log('---forEach---')
arrayA.forEach(function(item, index, array) {
    console.log(item, index, array)
})
```

# 9. 함수
## 9-1. 함수 만들기
```js
function 함수이름 (변수) {코드}
```
```js
function multiply(num1, num2) {
    let result = num1 * num2
    return result
}
console.log(multiply(2, 3))
```

## 9-2. 함수 표현식
- 함수를 만들고 변수에 선언 -> 변수가 함수 이름이 됨
```js
let multiply2 = function(num1, num2) {
    return num1 * num2
}
console.log(multiply2(3, 4))
```

## 9-3. 화살표 함수
- `function`이 `=>`로 바뀜
```js
let multiply3 = (num1, num2) => {
    return num1 * num2
}
console.log(multiply(2, 5))
```

### 화살표 함수 생략1
- `{}`안에 코드가 `return`하는 문장 하나만 있다면 `{}`과 `return`생략 가능
```js
let multiply4 = (num1, num2) => num1 * num2
console.log(multiply4(4, 6))
```

### 화살표함수 생략2
- `()`안에 매개변수가 하나만 있다면 `()`생략 가능
```js
let cube = num => num ** 3
console.log(cube(10))
```

# 10. 이벤트
