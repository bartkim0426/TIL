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


## Example: Circular Buffer

주식시장의 "moving average"를 예시로 듦. 7일, 30일 등의 moving average를 구하기 위해서는 window 형태가 필요함

![image](https://i.imgur.com/YqxKFft.png)

이를 파이썬 리스트로 나이브하기 구현하면 쉽게 할 수 있지만, 리스트에서 엘리먼트를 제거하는게 O(n) 시간복잡도이기 때문에 매우 비효율적인 코드

```
def window(array, size):
  if len(array) == size):  # O(1)
      del array[0]  # O(n)
  array.append(size)
```

### Circular buffer solution

이를 더 효율적이게 만들기 위해서 파이썬의 리스트를 fixed-size buffer 형태로 구성
- 선형적인 리스트가 아니라 시계방향으로 돌아가는 원형 데이터라고 상상
- queue처럼 작동하게 만듦
  - 마지막에 추가
  - 처음 값을 제거
  - 항상 O(1)의 시간복잡도를 가짐

![image](https://i.imgur.com/ikTxPED.png)

- 실제 리스트처럼 index를 가지고 있지 않고, 시작과 끝 지점으로 인덱스를 기록


```
class CircularBuffer:
    def __str__(self):
        return f'{self.buffer}'

    def __iter__(self):
        '''
        그냥 buffer는 index가 0부터 순서대로 나오지만
        이렇게 iter를 구현하면 실제 다음에 제거하기 원하는 순서 (low -> high)대로 나오는것을 볼 수 있다

        >>> c = CircularBuffer(5)
        >>> for i in range(1, 6):
        ...     c.add(i)
        >>> c.add(6)
        >>> c.buffer
        [6, 2, 3, 4, 5]
        >>> c
        [2, 3, 4, 5, 6]
        '''
        idx = self.low
        num = self.count
        while num > 0:
            yield self.buffer[idx]
            idx = (idx+1) % self.size
            num -= 1
    
    def __repr__(self) -> str:
        if self.isEmpty():
            return 'cb:[]'
        else:
            # iterator에서 읽어서 repr 해줌
            return 'cb:[' + ','.join(map(str, self)) + ']'

    def __init__(self, size: int):
        """contruct fixed size buffer"""
        self.size = size
        self.buffer = [None]*size
        self.low = 0
        self.high = 0
        self.count = 0

    def isEmpty(self):
        return self.count == 0

    def isFull(self):
        return self.count == self.size

    def add(self, value):
        if self.isFull():
            # if self.low ends up to self.size-1, 0
            self.low = (self.low+1) % self.size
        else:
            self.count += 1

        self.buffer[self.high] = value
        self.high = (self.high+1) % self.size

    def remove(self):
        if self.count == 0:
            raise Exception("Circular buffer is empty")
        value = self.buffer[self.low]
        self.low = (self.low+1) % self.size
        self.count -= 1
        return value
```


## Project: Moving Average / Stdev

### Moving Standard deviation as well

moving stdev?

Python list의 약점
- 단순히 iterated 되기만 하는 리스트 사용을 피하고
- generator 사용을 고려

피보나치 예시

```
# 리스트로 구현된 피보나치
def fibonacci(n):
    '''리스트로 구현된 피보나치'''
    a = b = 1
    result = [a, b]
    while n > 2:
        n = n - 1
        a, b = b, a + b
        result.append(b)
    return result

result = fibonacci(10)
for _ in result:
    print(_)
```

result를 생성하지만 위에서는 단순히 iterate만 하고 있다.

이를 generator로 바꾸면 아래와 같음


```
def fibonacci_generator(n):
    '''제너레이터로 구현한 피보나치'''
    a = b = 1
    yield a
    yield b
    while n > 2:
        n = n - 1
        a, b = b, a + b
        yield b
        
for _ in fibonacci_generator(10):
    print(_)
```

### Ubiquitous List Conclusion

리스트는 아주 강력하지만 많은 파이썬 프로그래머가 리스트에만 의존함

리스트의 가장 큰 단점은 메모리 스토리지
- highly optimized in CPython but we can do better
- 데이터를 잘 structure하여 효율적으로 만들 수 있음
