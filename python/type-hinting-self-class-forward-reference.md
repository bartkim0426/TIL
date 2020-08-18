# type hinting self class forward reference

요새 파이썬을 사용할 때에는 type hinting을 적극적으로 이용하려고 하고 있다.

type hint에서 사용할 이름이 아직 정의되지 않았는데 이를 사용해야 할 경우에 어떻게 처리하는게 맞는지 찾아보던 중 `Forward references`를 발견해 정리해둔다.


```
>>> class Tree:
...     def __init__(self, left: Tree, right: Tree):
...         self.left = left
...         self.right = right
...
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in Tree
NameError: name 'Tree' is not defined
```

이럴 경우 quote 처리를 해주면 된다.

```
>>> class Tree:
...     def __init__(self, left: 'Tree', right: 'Tree'):
...         self.left = left
...         self.right = right
...
```

