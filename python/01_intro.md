# 1. 변수
`var_name = value`
## number
- `type()` : 유형확인\
=> int : 정수, float : 소수, complex : 복합
## boolean
- 참과 거짓
## string
- 대소문자 구분
### string interpolation
- `%-formatting`
```python
age = 10
print('홍길동은 %s살입니다.' % age)
```
- `str.format()`
```python
age = 10
print('홍길동은 {}살입니다.'.format(age))
```
- `f-string`
```python
age =10
print(f'홍길동은 {age}살입니다.')
```

# 2. 연산자
## 산술연산자
```python
print(a + b)
print(a - b)
print(a * b)
print(a / b)
print(a ** b) # ** : 제곱수
print(a // b) # // : 나눗셈을 한 후 몫 표시
print(a % b) # % : 나눗셈을 한 후 나머지 표시

divmod(a, b) # divmod : a를 b로 나누는 도구로 몫, 나머지 모두 표시 / 산술연산자 아님
```
## 비교연산자
```python
print(a > b) # >는 물음표의 역할, a가 b보다 큰가요? 크면 True/작으면 False
print(a < b)
print(a >= b)
print(a <= b)
print(a == b) # == : a와 b가 같나요?
print(a != b) # != : a와 b가 같지않나요?
```
## 논리연산자
- and : 양쪽 모두 True일 때, True반환
- or : 양쪽 모두 False일 때, False 반환 / 둘 중 하나라도 True면 True 반환
- not : 현재 상태 부정
- `a and b` : a가 True이면 b 반환, a가 False이면 a반환
- `a or b` : a가 True이면 a 반환, a가 False이면 b반환
## 복합연산자
```python
a += b
# a = a + b
a -= b
# a = a - b
a *= b
# a = a * b
a /= b
# a = a / b
a //= b
a %= b
a **= b
```
## 기타연산자
### concatenation : 연결
- a가 글자면 +를 연결로 인식, 숫자면 +를 더하기로 인식
- a와 b가 같은 유형이여야함
```python
a = [1, 2, 3]
b = [2, 3, 4]
print(a + b) # => [1, 2, 3, 2, 3, 4]

print(1 in a) # 1이 a에 포함되어 있나요? => True
```
## 연산자 우선순위
0. ()를 통해서 그룹
1. **
2. 산술연산자(*, /)
3. 산술연산자(+, -)
4. 비교연산자(>, <, >=, <=), in, is
5. not
6. and
7. or

# 3. 형변환
## 암시적 형변환
## 명시적 형변환

# 4. 시퀀스 자료형
- 데이터에 순서대로 나열된 자료구조(정렬과 다름)
