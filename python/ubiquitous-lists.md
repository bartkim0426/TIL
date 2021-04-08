# 2. Ubiquitous lists

## Python lists are everywhere

python list: "single contiguous block of memory"

보통은 문제가 되지 않지만, 효율성이 중요해지면 performance issue가 발생할 수 있다.

이를 보여주는 피보나치 예시

```
def fibonacci(n):
    a = b = 1
    result = [a, b]
    while n > 2:
        n = n - 1
        a,b = b,a+b
        result.append(b)
    return result
    
def fibonacci_generator(n):
    a = b = 1
    yield a
    yield b
    while n > 2:
        n = n - 1
        a, b = b, a+b
        yield b
```

리스트: contiguous block of memory
- potential to waste resources
  - list의 모든 element는 레퍼런스 -> 모든 메모리가 사용됨
- operation can become expensive
  - del myList[0]: O(n)
  - 모든 element들을 n-1으로 옮겨야 하기 때문

### example: Use List as Stack

- Stack: abstract data type
  - push(v): add value to the top of stack
  - pop(): remove topmost value and return it
  - isEmpty()

이를 list로 구현해보자.

```
class Stack:
    def __init__(self):
        self.stack = []
        
    def push(self, v):
        self.stack.append(v)
        
    def pop(self):
        return self.stack.pop()
        
    def isEmpty(self):
        return len(self.stack) == 0
```

performance
- isEmpty: use len in list -> O(1)
- push: O(1)
- pop: O(1)


## Principle: Seperate structure from function

- Don't use built-in data types "as is" and assume programmers will do the right thing
  - Concept known as "Information hiding"
  - list; 다른 프로그래머가 sort, reverse를 쓸 수도 있음을 기억해야함

- Tuple: read-only structures
  - 정보가 변하면 안 될 때 유용
