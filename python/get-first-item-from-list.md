## Find string and get first item from list 

리스트에서 string을 검색하는 방법은 다양하지만 generator를 사용해서 찾는게 가장 pythonic 한듯 하다.

```
my_list = [1, 2, 'test']
try:
    next((x for url in my_list if 'test' in x))
except StopIteration:
    ...
```

`StopIteration` 에러를 발생시키지 않고 default 값을 주려면 `next()`에 파라미터를 넘겨주면 된다.

```
next((x for url in my_list if 'test' in x), 'default_value')
```

첫번째 값이 아닌 전체를 얻고 싶은 경우 filter를 사용하면 된다.

```
my_list = [1, 2, 3, 4]

result_generator = filter(lambda x: x % 2 == 0, my_list)
```

> python2에서는 list, python3에서는 제너레이터 객체를 반환한다.
