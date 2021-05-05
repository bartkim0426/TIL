# Recursive Structure

## Binary Tree

### Recursive Thinking Search list

- A list is a linear structure
  - Recursion can be also be used

아래 예시는 list를 사용한 step by step structure

```
def search(aList, target):
    for v in aList:
        if v == target:
            return True
    return False 
```

Recursive solution

```
def searchRecursive(aList, target):
    if len(aList) == 0:
        return False
    
    if aList[0] == target:
        return True
    return searchRecursive(aList[1:], target)
```

- recursion: 자기 자신을 다시 호출하는 경우들을 말함
  - Base case: 언제 recursion을 끝내야 하는지 / 언제 문제가 해결되는지
  - 케이스를 잘게 쪼개서 해결: Divide and conquer

위의 예시를 LinkedList로 구현하면 아래와 같음
- 위에서 list를 사용했을 때에는 매번 자기 자신을 호출할 때마다 새로운 리스트를 생성하는 오버헤드 (`aList[1:]`)가 있었음
- LinkedList를 사용하면 extra storage로 인한 overhead가 없음


```
class LinkedNode:
    def __init__(self, value):
        self.value = value
        self.next = None

def search(n, target):
    # base case
    if n.next is None:
        return False
        
    # Action and Recursive step
    if n.value == target:
        return True
    return search(n.next, target)
```

비슷한 예시: linked list의 값을 모두 더하는 함수

```
def sumList(n):
    # base case
    if n.next is None:
        return 0
        
    # Action and Recursive step
    return n.value + sumList(n.next)
```


### Recursive Thinking Fibonacci

이전 예시에서는 list와 generator를 사용해서 구현했었음
- fib(40)을 계산하는데에는 거의 1분이 걸림 -> 시간복잡도가 아주 높음

recursion으로도 구현 가능
- recursion은 현명하게 사용해야함
- recursive data structure와 잘 맞는 경우 사용해야됨

```
def fib(n):
    # base case
    if n < 2: return 1
    
    # Action and Recursive case
    return fib(n-1) + fib(n-2)
```

### Better structure: Binary Search Tree

모든 recursive data structure의 조상
- BST (Binary Search Tree) case에서 자주 사용됨
- linked list와 비슷하게 값을 가진 node로 구성됨
- 각각의 BinrayNode는 value를 가짐
- 각강의 BinaryNode 왼쪽, 오른쪽 child node를 가질 수 있음

Binary Search Tree Property
- n 왼쪽에 있는 모든 노드의 value는 n.value보다 작음
- n 오른쪽에 있는 모든 노드의 value는 n.value보다 큼

Observation
- 각각의 노드는 BST의 root가 됨

### Performance

지금까지는 O(1), O(n), `O(n**2)`를 다룸.

#### Binary Search find

`O(logn)`
- 정렬된 전화번호부를 찾을 때 원하지 않는 나머지 반쪽을 버리는것처럼 반씩 제거할 수 있는 시간복잡도
- Full balanced trees are best

binary search는 모든 recursive data structure의 기초이기 때문에 잘 이해하는게 중요함


#### Binary Search Tree Add Intuition

마지막에 값을 추가하는것을 제외하고는 search와 동일함.



