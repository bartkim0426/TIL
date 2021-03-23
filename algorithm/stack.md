# Stack and que

## DFS/BFS: 그래프 탐색 알고리즘

- 탐색: 많은 양의 데이터 중에 원하는 데이터를 찾는 과정
- 대표적인 그래프 탐색 알고리즘: DFS, BFS
- 매우 자주 등장하는 유형이기 때문에 반드시 알아야함!

## 스택 자료구조

- 먼저 들어온 데이터가 나중에 나가는 방식: FILO (First In Last Out)
- 입/출구가 동일한 형태로 스택을 시각화하면 됨
  - ex. 박스 쌓기

### 스택 구현 예제 (python)

- 간단하게 list를 사용해서 append, pop을 사용하면 됨
- append, pop은 시간복잡도 `O(1)`이기 때문에 사용하면 됨

```
# 삽입5, 삽입2, 삽입3, 삽입7, 삭제, 삽입1, 삽입4, 삭제
stack = []
stack.append(5)
stack.append(2)
stack.append(3)
stack.append(7)
stack.pop()
stack.append(1)
stack.append(4)
stack.pop()

# 가장 먼저 들어온 원소부터 보여주고 싶으면 역순으로 보여주면 됨
stack[::-1]
# 순서대로 보여주고 싶으면 해당 stack list 그대로
stack
```

## 큐 자료구조
- 먼저 들어온 데이터가 먼저 나가는 방식: FIFO (First In First Out)
- 입/출구가 모두 뚫려 있는 터널과 같은 형태로 시각화
- 일종의 대기열로 생각하면 된다.

### 스택 구현 예제 (python)

- collections.duque 라이브러리를 사용하면 된다.
- list를 쓸 수 있지만 시간복잡도가 높아져서 deque 라이브러리를 사용하는게 좋음

```
# 삽입5, 삽입2, 삽입3, 삽입7, 삭제, 삽입1, 삽입4, 삭제
from collections import deque

queue = deque()

# 삽입5, 삽입2, 삽입3, 삽입7, 삭제, 삽입1, 삽입4, 삭제
# python에서는 관행적으로 append, popleft 형태를 사용. 
# 데이터가 오른쪽으로 들어와서 왼쪽으로 나감
queue.append(5)  # queue.append는 오른쪽에 추가
queue.append(2)
queue.append(3)
queue.append(7)
queue.popleft()  # 가장 왼쪽의 원소 빠짐
queue.append(1)
queue.append(4)
queue.popleft()

# 먼저 들어온 순서대로
queue

# 나중에 들어온 원소부터 출력
queue.reverse()
queue
```




