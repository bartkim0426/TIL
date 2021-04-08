# 01. python data model

## 특별 메서드

```
from collections import namedtuple

Card = namedtuple('Card', 'rank suit')

class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = ['spades', 'diamonds', 'clubs', 'hearts']

    def __init__(self):
        self._cards = [Card(rank=rank, suit=suit) for rank in self.ranks for suit in self.suits]

    def __len__(self):
        return len(self._cards)

    def __getitem__(self, position):
        return self._cards[position]
```

`__getitem__` 특별 메소드를 구현해서 반복, 인덱스 등도 사용 가능.

```
suit_values = dict(spades=3, hearts=2, diamonds=1, clubs=0)

def spades_high(card):
    rank_value = FrenchDeck.ranks.index(card.rank)
    return rank_value * len(suit_values) + suit_values[card.suit]

# 정렬

deck = FrenchDeck()
for card in sorted(deck, key=spades_high):
    print(card)
```

반복은 암묵적으로 수행되는 경우가 많음.

`__contains__()` 메소드가 없다면 `in` 연산자는 차례대로 검색 (반복)

`for i in x`문은 내부적으로 `iter()`를 호출하고 이는 `x.__iter__()`를 호출한다

> 그래서 `x in arr`를 수행하면 시간복잡도가 O(n)

### 수치형 흉내내기

2차원 유클리드 벡터를 나타내는 클래스를 구현하는 예시.

add, multiply, abs를 사용할 수 있게 만들어보자.

여러 특별 메서드를 사용해서 구현

```
from math import hypot
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        
    def __repr__(self):
      return f'Vector({self.x}, {self.y})'

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)
        
    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)
        
    def __abs__(self):
      return hypot(self.x, self.y)
      
    def __bool__(self):
      return bool(abs(self))
```

### 사용자 정의 boolean

bool을 판단하기 위해 파이썬은 `bool(x)`를 사용. 

`__bool__()`이나 `__len__()`이 구현되어 있지 않은 경우 기본적으로 사용자 정의 클래스의 객체는 참이라고 간주한다.

`bool(x)`는 `x.__bool__`를 호출. 구현되어 있지 않으면 `x.__len__()`을 호출한다. 이 메서드가 0이면 false, 나머지는 true



> [python data model docs](https://docs.python.org/ko/3/reference/datamodel.html)를 읽으면 좋다.
