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



