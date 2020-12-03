# singleton pattern in python

싱글턴 패턴: 인스턴스를 하나만 생성하여 불필요한 메모리 낭비를 방지하고 해당 클래스의 인스턴스를 바로 호출 할 수 있게 하는 패턴.

한번에 아주 많은 요청을 처리해야하는데 매번 클라이언트를 호출해야 할 일이 있어 처음에는 전역변수를 사용하였다가 싱글톤 패턴을 사용하는 김에 정리해둔다.

클래스로 직접 구현하거나 데코레이터로 구현이 가능하다. 각 방법과 장단점을 소개

```
# 예시 BaseClient - 공통으로 사용
class BaseClient:
    @classmethod
    def class_call(cls):
        return {'result': True}
    def call(self):
        return {'result': True}
```

### 1. decorator

```
def singleton_decorator(class_):
    instances = {}
    def getinstance(*args, **kwargs):
        if class_ not in instances:
            instances[class_] = class_(*args, **kwargs)
        return instances[class_]
    return getinstance

@singleton_decorator
class DecoratorClient(BaseClient):
    pass
```

클래스로 만들어서 상속받는 아래의 방법들보다 직관적인 방법이다.

다만 object가 아닌 클래스 자체는 singleton 객체가 아닌 함수이기 때문에 클래스 메소드를 부를 수 없다.

```
>>> c1 = DecoratorClient()
>>> c2 = DecoratorClient()
>>> c1 == c2
True
>>> DecoratorClient.class_call()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'function' object has no attribute 'class_call'
```

### 2. base class 생성

싱글톤 클래스를 생성하여 상속받는 방법. 순수 클래스를 사용하는 장점이 있지만 상속을 받아야하는 단점이 있고, 상속중에 `__new__` 클래스가 override 되지 않게 주의해야한다.

```
class SingletonClass(object):
    _instance = None
    def __new__(class_, *args, **kwargs):
        if not isinstance(class_._instance, class_):
            class_._instance = object.__new__(class_, *args, **kwargs)
        return class_._instance

class ClassClient(SingletonClass, BaseClient):
    pass
```

실제로 호출해보면 클래스의 메소드도 모두 동일한 것을 볼 수 있다.

```
>>> c1 = ClassClient()
>>> c2 = ClassClient()
>>> c1 == c2
True
>>> c1.call() == c2.call()
True
>>> c1.class_call() == ClassClient.class_call()
True
```

### 3. metaclass 사용

type으로 싱글톤 클래스를 정의하고 메타클래스를 사용하여 구현이 가능하다.

개인적으로는 가장 좋은 방법이라고 생각.


```
class SingletonType(type):
    def __call__(cls, *args, **kwargs):
        try:
            return cls.__instance
        except AttributeError:
            cls.__instance = super().__call__(*args, **kwargs)
            return cls.__instance

class MetaclassClient(BaseClient, metaclass=SingletonType):
    pass
```

`__call__` 메소드는 try/except을 사용하지 않고 instances 변수를 사용해서 구현해도 된다.

```
>>> c1 = MetaclassClient()
>>> c2 = MetaclassClient()
>>> c1 == c2
True
>>> c1.call() == c2.call()
True
>>> MetaclassClient.class_call() == c1.class_call()
True
```

