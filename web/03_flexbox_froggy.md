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
