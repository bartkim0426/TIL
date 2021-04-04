# Fundamentals

## 1.1 List: fundamental type

### Stack
- First-In Last-Out

### Queue
- First-In First-Out
- python support deque (double end queue)
  - it can pop both left and right


## 1.2. Analyis techniques

how do we determine the efficiency of the program?

### Asymptotic analysis for performance: O(n)

- time complexity
  - time for a method to complete
  - calculate time as function `t(n)`
- space complexity
  - amount of computer storage required (`s(n)`)


> asymptotic performance에 대해서 더 찾아보기

### Performance computation

`timeit`: simply evaluating performance

```
import random
import timeit

for trial in [2**_ for _ in range(1, 9)]:
    numbers = [random.randint(1, 9) for _ in range(trial)]
    m = timeit.timeit(stmt='sum = 0\nfor d in numbers:\n\tsum = sum + d', setup='import random\nnumbers = ' + str(numbers))
    print('{0:d} {1:f}'.format(trial, m)) 
```

- setup fragment: generate numbers
- stmt: state for evaluating performance

in timeit method, use code below

```
sum = 0
for d in numbers:
  sum = sum + d
```

The result will be incresing by the size of n: Asymptotic (점근적인)

```
2 0.122470
4 0.160752
8 0.279808
16 0.506057
32 1.049626
64 2.189525
128 5.043972
256 10.776976
```


### Asymptotic space complexity

- How much extra storage is required
  - A secondary concern that cannot be ignored

example problem: 리스트가 중복된 값을 가지고 있는지 확인하는 문제

#### Fast with extra storage
- use set for extra storage
- Amortized constant time for set
- performance: O(n) with O(n) extra space

```
def uniqueCheckFast(aList):
    check = set()
    for v in aList:
      if v in check:
          return True
      check.add(v)
    return False
```

#### Slow without extra storage
- Nested for loop
- performance is O(n**2) or `quadratic`

```
def uniqueCheckSlow(aList):
    for i in range(len(aList) - 1):
        for j in range(i+1, len(aList)):
            if aList[i] == aList[j]:
                return True
    return False
```


## 1.3. Design principles for data structures

### built-in types complexity

각 api들의 performanc가 중요함

- length is O(1) in all cases

**List**

- Add
  - insert: O(n), append: O(1)
- Remove: O(n)
- Contains: O(n)

![image](https://i.imgur.com/5VniS0v.png)


### Relationships between DS and ADT
- DS: Data structure
  - 실제 존재하는 type(list, set) 을 이용하여 데이터를 효율적으로 organize
- ADT: Abstract Data Type
  - Modeling approach to define new data type by it's behavior from user's perspective

### Design principles
- Space vs Time tradeoff
  - extra storage를 사용하여 performance를 향상시킬 수 있다.
  - Extra O(1) storage: 대개 좋은 방법
  - O(n) storage가 필요하다면 일정수준의 확신이 필요

- Seperate structure from behavior
   
