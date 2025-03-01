## 00_numpy
- [Numpy](https://numpy.org/doc/stable/reference/index.html) 불러오기
```
ipmort numpy as np
```
- 0차원 : 스칼라(scala), 1차원 : 벡터(vector), 2차원 : 행렬(matrix), 3차원 이상 : 텐서(tenser)
- `%timeit` : 코드를 실행하는데 시간이 얼마나 걸리는지 측정

 ### ndarray 생성
- `np.arange()` : `range()`함수와 동일,\
 `range()`함수는 정수단위만 지원하지만 `np.arange()`함수는 실수단위도 표현가능
 ```
np.arange(10) # 0부터 9까지 순서가 있는 배열을 만들어줌
 ```
 - `np.array()` : 리스트나 튜플 등 iterable한 객체를 numpy 배열로 변환
```
data = [[1, 2, 3], [4, 5, 6]]
arr = np.array(data)
type(arr) # numpy.ndarray유형
```
- `arr.ndim` : 배열의 차원 수를 알려줌, arr은 2차원
- `arr.shape` : 배열이 몇 행 몇 열인지 튜플 형식으로 반환
- `np.zeros(n)` : 데이터는 모르지만 배열의 규격을 정함, 0이 n개인 배열을 반환
```
np.zeros((5, 5)) # np.zeros 안에 튜플을 넣으면 5행 5열인 배열 반환
```
- `np.empty()` : `np.zeros`와 같다고 생각

### ndarray 자료형
- `arr.dtype` : 배열의 타입을 알려줌
- `np.array()`함수에 옵션`dtype=np.float64`을 넣으면 float64로 형변환
```
np.array([1, 2, 3], dtype=np.float64)
```
- `arr.astype()` : 형변환 함수, 소수를 정수로 형변환할 때 소수점은 버림처리리
```
arr = np.array([1, 2, 3])
arr.astype(np.int64)
```
int32, int64, object, float64 등

### 산술 연산
```
arr = np.array([[1, 2, 3], [4, 5, 6]])

print(arr + arr)
print(arr -1)
print(arr * arr) # 같은 위치에 있는 값끼리 곱
print(1 / arr)
print(arr ** 3) # 모든 데이터들을 세제곱 
```
```
arr1 = np.array([[1, 2, 3], [4, 5, 6]])
arr2 = np.array([[3, 2, 1], [1, 2, 3]])

print(arr1 > arr2) # 같은 위치에 있는 데이터끼리 비교
print(arr == 3) # 데이터가 3인 데이터를 찾음
```

### 색인(indexing)과 슬라이싱(slicing)
- indexing
```
arr = np.arange(10)
print(arr[7]) # => 7
print(arr[2:5]) # 2번째 데이터부터 4번쨰 데이터까지 출력
arr[2:5] = 10 # 2번째부터 4번째까지 데이터의 값을 10으로 변경, 원본도 변경
```
```
py_list = [1, 2, 3, 4, 5]
py_list[2:3].pop()
```
- py_list[2:3]의 결과는 [3]이라는 새로운 리스트로 py_list와 별개
- 원본을 복제해서 사용했기 때문에 원본은 바뀌지 않음
```
arr = np.array([[1, 2, 3], [4, 5, 6]])
print(arr[1][1])
print(arr[1, 1]) # 위와 같음
```
```
arr3d = np.array([[[1, 2, 3], [4, 5, 6], [7, 8, 9]],
                  [[11, 12, 13], [14, 15, 16], [17, 18, 19]],
                  [[11, 22, 33], [44, 55, 66], [77, 88, 99]]])
print(arr3d[2][0][1])
print(arr3d[2, 0, 1])
```