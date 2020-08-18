# type hinting multiple return type: Union

파이썬 type hinting에서 여러 가지 유형의 return type을 지정하고 싶을 때는`Union`을 사용하면 된다.

```
from typing import Union

def foo() -> Union[str, int]:
    ...
```

`Uinon[X, None]`을 사용하고 싶을때는 `Optional[X]`를 사용하면 된다.

```
from typing import Optional

def foo() -> Optional[str]:
    ...
```
