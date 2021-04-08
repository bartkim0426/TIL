# time complexity and implementation of python data structure

## Time complexity for python data structure

"average case"와 "amortiezed worst case" 로 나눌 수 있다.

### List

- size(len): O(1) / O(1)
- append: O(1) / O(1)
  - list의 마지막에 추가만 하면 되기 때문에 O(1)
- insert: O(n) / O(n)
  - 특정 index에 넣어야 하기 때문
- pop: O(1) / O(1)
  - list의 마지막 element를 빼기만 하면 됨
- sort: O(n log n) / O(n log n)
  - 내부적으로 timesoft 알고리즘을 쓰기 때문에 n log n이 사용됨
  - [timsort algorithm](https://d2.naver.com/helloworld/0315536)
- contains: O(n)
  - `x in array`는 내부적으로 `__iter__`를 호출하기 때문에 O(n)

### dictionary, set

dictionary와 set은 내부적으로 hash table을 사용하여 구현되어 있기 때문에 time complexity가 거의 동일하다.

amortiezed worst case는 hash 구현에 따라 달라지지만 모두 같은 key에 매핑될 수 있는 최악의 경우를 상정. 일반적인 경우에는 average case를 따른다고 생각하면 된다.

- add: O(1) / O(n)
  - 중복된 hash key가 없으면 일반적으로 O(1)
- remove: O(1) / O(n)
- contains: O(1) / O(n)

> set도 dictionary와 같기 때문에 unique element에서 contain을 사용하는 경우 list 대신 set을 사용하는것이 좋다.


### deque

파이썬에서 제공하는 `collections.deque` (double ended queue)는 내부적으로 doubly linked list로 구현되어 있다.

양쪽의 ends에 모두 접근 가능하지만, 가운데 값을 찾거나 추가, 제거하는 경우에는 느리다.

- add: O(1) / O(1)
  - append: O(1) / O(1)
  - appendleft: O(1) / O(1)
- remove:
  - pop: O(1) / O(1)
  - popleft: O(1) / O(1)
  - remove: O(n) / O(n)
    - 양 끝 값이 아닌 중간 값을 제거
- contains: O(n)


> 더 자세한 정보는 [python timecomplexity docs](https://wiki.python.org/moin/TimeComplexity) 참고

