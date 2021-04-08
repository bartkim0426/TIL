# pramp.com

### 2021.04.04

[Getting a different number](https://www.pramp.com/challenge/aK6V5GVZ9MSPqvG1vwQp)
- find smallest integer not in array
- 처음에는 `O(n**2)`로 풀었음o
- 이후 O(nlogn)으로 리팩토링
- 마지막으로 O(n)으로 수정 (set 사용)


num 만큼 돌아서 시간 복잡도가 O(n)이라고 생각했는데 `num in arr`: 리스트에서 num을 찾는게 O(n)만큼 더 걸려서 결국 `O(n**2)`가 나왔다.
```
num = 0
while num <= len(arr):
  if num not in arr:
      return num
  num += 1
```

그래서 loop를 한번만 하려고 sort를 했다.

여기서도 `sort` 함수의 시간복잡도를 생각을 하지 못함. 파이썬 list의 sort는 O(nlogn)이기 때문에 최종 시간복잡도는 O(nlogn + n)

```
num = 0
sort(arr)
for x in arr:
    ...
```

그래서 인터뷰어의 도움을 받아 arr의 데이터 타입을 변경하였다.

탐색 (`num in arr`)의 시간복잡도를 최소화 할 수 있는 데이터 타입을 생각해 보라고 해서 dict밖에 생각해 내지 못했는데, `set`도 마찬가지로 탐색 시간복잡도가 O(1)이였다.

set도 파이썬에서는 내부적으로 hash table로 구현되어 있기 때문에 dictionary와 시간복잡도가 거의 흡사하다.

그래서 set으로 변경해서 문제를 풀면 시간복잡도가 O(n)이 나온다.

```
num = 0
arr = set(arr)
while num <= len(arr):
  if num not in arr:
      return num
  num += 1
```

#### feedback: 공부할것 & 느낀점
- python 내장 데이터 타입의 시간복잡도 및 구현
  - x in list, sort(list)의 runtime(time complexity)에 대한 질문을 받았을때 순간 당황했다.
  - 그냥 사용만 해서 내부적으로 어떻게 구현되어 있는지에 대해서 생각을 별로 해 본 적이 없는데, 이번 기회에 정리가 필요할거같다
  - 특히 list & set/dict 구현과 시간복잡도를 위주로 정리해봐야겠다
- 문제 풀이 방식
  - 상대방은 상당히 익숙하게 문제를 풀었다
  - 문제를 받자마자 이를 읽으면서 헷갈리는 내용을 물어보며 문제 내용을 정리
  - 수도코드로 로직을 작성하면서 이 방식으로 풀어도 괜찮겠냐고 먼저 컨펌. 이때 시간/공간 복잡도를 명시적으로 얘기함
  - 실제 구현한 이후에 바로 run test를 하지 않고 example code를 직접 대입해보며 검증함
  - 이런 방식으로 문제를 풀 수 있도록 연습이 필요하겠다.
