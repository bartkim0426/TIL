# python data science handbook

[wikibooks 링크](https://wikibook.co.kr/python-ds-handbook/)

- Start reading: 2020-08-07

## 01. IPython

이 부분은 간단하게 읽고 넘어감. 새로운 내용만 정리해둔다.

#### `??`: 소스코드 접근 가능

`?`로 help text는 자주 사용하였는데 `??`도 쓸 쑤 있는줄은 처음 알았다.

```
In [9]: import datetime

datetime??
Type:        module
String form: <module 'datetime' from '/Users/seulchankim/anaconda3/lib/python3.7/datetime.py'>
File:        ~/anaconda3/lib/python3.7/datetime.py
Source:
"""Concrete date/time and related types.

See http://www.iana.org/time-zones/repository/tz-link.html for
time zone and DST data sources.
"""

import time as _time
import math as _math
import sys

def _cmp(x, y):
    return 0 if x == y else 1 if x > y else -1
...
```


#### `*`: wildcard

```
In [10]: *Error?

ArithmeticError
AssertionError
AttributeError
BlockingIOError
BrokenPipeError
BufferError
ChildProcessError
ConnectionAbortedError
ConnectionError
ConnectionRefusedError
ConnectionResetError
```

#### `%timeit`: 코드 실행 시간 측정

```
In [13]: %%timeit
    ...: L = []
    ...: for n in range(1000):
    ...:     L.append(n ** 2)
    ...:
287 µs ± 16.5 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

#### `In/Out` 객체

ipython에서 사용한 Input, Output을 객체로 저장해둠.. 항상 명령줄에서 다시 확인했는데 엄청 편리하겠다.

```
In [1]: print('hello world')
hello world

In [2]: 1 + 2
Out[2]: 3

In [3]: In
Out[3]: ['', "print('hello world')", '1 + 2', 'In']

In [4]: Out
Out[4]: {2: 3, 3: ['', "print('hello world')", '1 + 2', 'In', 'Out']}
```

#### `_`: 이전 출력값

```
In [8]: sum([1, 2, 3])
Out[8]: 6

In [9]: print(_)
6
```

#### `%debug`: debuging

이 디버거의 확장 버전이 ipython debugger인 `ipdb`라고 한다.

#### `%pdb on`: breakpoint



## 02. Numpy

파이썬은 동적 타이핑 언어. 이게 numpy랑 무슨 상관일까?를 잘 설명해줌

파이썬의 정수는 정수 이상이다

- 파이선 객체는 값 뿐만 아니라 다른 정보까지 포함하는 위장한 C 구조체
- C 정수는 근본적으로 정수 값을 나타내는 바이트를 포함하는 메모리 위치를 가리키는 레이블
- 파이썬 정수는 정수값을 담고 있는 바이트를 포함한 모든 파이썬 객체 정보를 포함하는 메모리의 위치를 가리키는 포인터

파이썬의 리스트는 리스트 이상이다
- 리스트도 마찬가지로 동적 타이핑의 비용이 따름
- 모든 변수가 같은 타입일 경우에는 고정 타입 배열이 효율적 -> Numpy 배열

python 3.3부터는 내장 array 모듈로 고정타입 방식을 제공

```
import array
L = list(range(10))
A = array.array('i', L)  # 'i'는 정수를 가리키는 타입 코드
```

하지만 이보다 `ndarray`가 더 유용함. 여러 효율적인 연산을 추가해주기 때문

`ndarray`는 타입이 같아야됨. 다른 타입일 경우 가능한 상위타입을 취함

명시적으로 `dtype`으로 데이터 타입 설정 가능

명시적으로 다차원이 가능 (vector)

```
In [9]: np.array([range(i, i+3) for i in [2, 4, 6]])

Out[9]:
array([[2, 3, 4],
       [4, 5, 6],
       [6, 7, 8]])
```

### numpy의 하위 배열은 copy가 아닌 view

일반 파이썬 list와 다르기 numpy 배열은 슬라이싱을 해도 원본 데이터의 view를 반환함.

```
[ins] In [10]: print(x2)
[[12  5  2  4]
 [ 7  6  8  8]
 [ 1  6  7  7]]

[ins] In [11]: x2_sub = x2[:2, :2]

[ins] In [12]: x2_sub
Out[12]:
array([[12,  5],
       [ 7,  6]])

[ins] In [13]: x2_sub[0, 0] = 99

[ins] In [14]: x2
Out[14]:
array([[99,  5,  2,  4],
       [ 7,  6,  8,  8],
       [ 1,  6,  7,  7]])

[ins] In [15]: x2_sub
Out[15]:
array([[99,  5],
       [ 7,  6]])
```

> 일반 python 배열과 다르기 x2_sub를 수정하면 x2도 수정되는 것을 볼 수 있다.

### UFuncs

여러 연산에 대해 컴파일된 루틴에 편리한 인터페이스를 제공. 일반적인 사칙연산 말고도 다양한 함수를 제공하니 가능하면 일반 파이썬 표현식을 UFuncs로 변경하는게 좋을듯

  지금 따로 정리하지 않고 필요할 때 찾아서 쓰면 될 듯 하다.
