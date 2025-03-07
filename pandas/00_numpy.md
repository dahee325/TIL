# 00_numpy
- [Numpy](https://numpy.org/doc/stable/reference/index.html) 불러오기
```python
import numpy as np
```
- 0차원 : 스칼라(scala), 1차원 : 벡터(vector), 2차원 : 행렬(matrix), 3차원 이상 : 텐서(tenser)
- `%timeit` : 코드를 실행하는데 시간이 얼마나 걸리는지 측정

 ## ndarray 생성
- `np.arange()` : `range()`함수와 동일,\
 `range()`함수는 정수단위만 지원하지만 `np.arange()`함수는 실수단위도 표현가능
 ```python
np.arange(10) # 0부터 9까지 순서가 있는 배열을 만들어줌
 ```
 - `np.array()` : 리스트나 튜플 등 iterable한 객체를 numpy 배열로 변환
```python
data = [[1, 2, 3], [4, 5, 6]]
arr = np.array(data)
type(arr) # numpy.ndarray유형
```
- `ndim` : 배열의 차원 수를 알려줌, arr은 2차원
- `shape` : 배열이 몇 행 몇 열인지 튜플 형식으로 반환
- `zeros(n)` : 데이터는 모르지만 배열의 규격을 정함, 0이 n개인 배열을 반환
```python
np.zeros((5, 5)) # np.zeros 안에 튜플을 넣으면 5행 5열인 배열 반환
```
- `empty()` : `np.zeros`와 같다고 생각

## ndarray 자료형
- `dtype` : 배열의 타입을 알려줌
- `array()`함수에 옵션`dtype=np.float64`을 넣으면 float64로 형변환
```python
np.array([1, 2, 3], dtype=np.float64)
```
- `astype()` : 형변환 함수, 소수를 정수로 형변환할 때 소수점은 버림처리
    - int32, int64, object, float64 등
```python
arr = np.array([1, 2, 3])
arr.astype(np.int64)
```

## 산술 연산
```python
arr = np.array([[1, 2, 3], [4, 5, 6]])

print(arr + arr)
print(arr -1)
print(arr * arr) # 같은 위치에 있는 값끼리 곱
print(1 / arr)
print(arr ** 3) # 모든 데이터들을 세제곱 
```
```python
arr1 = np.array([[1, 2, 3], [4, 5, 6]])
arr2 = np.array([[3, 2, 1], [1, 2, 3]])

print(arr1 > arr2) # 같은 위치에 있는 데이터끼리 비교
print(arr == 3) # 데이터가 3인 데이터를 찾음
```

## 색인(indexing)과 슬라이싱(slicing)
- indexing
```python
arr = np.arange(10)
print(arr[7]) # => 7
print(arr[2:5]) # 2번째 데이터부터 4번쨰 데이터까지 출력
arr[2:5] = 10 # 2번째부터 4번째까지 데이터의 값을 10으로 변경, 원본도 변경
```
- `py_list[2:3]`의 결과는 [3]이라는 새로운 리스트로 py_list와 별개
- 원본을 복제해서 사용했기 때문에 원본은 바뀌지 않음
```python
py_list = [1, 2, 3, 4, 5]
py_list[2:3].pop()
```
- 1번째 리스트의 요소 중 1번째 요소를 가져오기 위해서는 중첩 인덱싱으로 접근
```python
arr = np.array([[1, 2, 3], [4, 5, 6]])
print(arr[1][1]) # 중첩 인덱싱
print(arr[1, 1]) # 위와 같음
```
```python
arr3d = np.array([[[1, 2, 3], [4, 5, 6], [7, 8, 9]],
                  [[11, 12, 13], [14, 15, 16], [17, 18, 19]],
                  [[11, 22, 33], [44, 55, 66], [77, 88, 99]]])
print(arr3d[2][0][1])
print(arr3d[2, 0, 1])
```
- slicing : `[start:stop:step]`
    - `[:]` : 전체 추출
    - `[start:]` : start 인덱스부터 끝까지 추출
    - `[:stop]` : 처음부터 stop 인덱스 전까지 추출
    - `[start:stop:step]` : start 인덱스부터 stop 인덱스 전까지 인덱스 번호 step칸씩 건너뛰어 추출
```python
print(arr3d[:1, 1:, 2:])
print(arr3d[0, 1:, 2:]) # 인덱싱 접근을 하면 차원 안으로 들어가기 때문에 차원을 유지하지 않고 차원을 줄여줌
```

## 불리언 값으로 선택
- 불리언(boolean) : 참(True)과 거짓(False) 중 하나의 값을 가짐
- 전체 데이터 중 내가 취하고 싶은(특정 조건에 맞는) 데이터를 고를 때 사용
```python
names = np.array(['hong', 'kim', 'hong', 'kim'])
names = np.array(['hong', 'kim', 'hong', 'kim'])
data = np.array([['math', 60], ['math', 90], ['eng', 70], ['eng', 50]])
name == 'hong' 
data[names == 'hong']
```
- `name == 'hong'`의 결과는 `array([ True, False,  True, False])`로 names가 hong이면 True, 아니면 False를 반환
- `data[names == 'hong']`의 결과는 `array([['math', '60'], ['eng', '70']], dtype='<U21')`로 트루 위치의 값만 출력
```python
print(data[names == 'hong', 1]) # 인덱스 접근
print(data[names == 'hong', :1]) # 슬라이싱
print(names != 'hong') # != 다른가요?
print(~(names == 'hong')) # 계산결과를 역 취해줌
```

## 팬시 색인(fancy indexing)
- [](인덱싱)안에 [](리스트)를 넣으면 원하는 행을 직접적으로 선택하여 출력, 순서도 지정한대로 출력
```python
arr = np.zeros((8, 4)) # 8*4
for i in range(8):
    arr[i] = i
arr[[4, 3, 0, 5]] # 4, 3, 0, 5번째 행만 출력
```
- `reshape()` : 행렬의 행과 열을 변경할 때 사용
```python 
np.arange(32).reshape(8, 4)
```

## 배열 전치
- `T` : 대각선을 기준으로 대칭되어 행과 열이 바뀜
- `@`, `np.dat` : 행렬의 내적, 행렬의 곱
```python
arr = np.arange(15).reshape(3, 5)
print(arr.T)
print(arr @ arr.T)
print(np.dot(arr, arr.T))
```

## numpy 함수
- `np.random.standard_normal()` : 랜덤하게 배열을 만들 때 사용
    - 옵션 `size=()`로 행렬의 사이즈 설정 가능
- `np.random.randn()` : 랜덤하게 배열 만들 때 사용
```python
samples = np.random.standard_normal(size=(3, 3))
samples = np.random.randn(3, 3) # 위와 같은 코드
print(samples)
```
- `np.sqrt()` : 루트, 1/2 제곱
- `np.abs()` : 절대값
- `nan`(not a number) : 숫자가 아님, none과 비슷
- `np.isnan()` : 배열 안에 nan인 값이 있나요? => 있으면 True, 없으면 False 반환
```python
arr = np.sqrt(samples)
arr2 = np.abs(samples)
np.isnan(arr)
```