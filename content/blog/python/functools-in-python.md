---
title: Functools in Python
date: 2023-02-18 00:02:68
category: Python
thumbnail: { thumbnailSrc }
draft: false
---

# Introduction

파이썬에서 기본적으로 제공하는 모듈 중 하나인 `functools` 모듈은 고차 함수(high-order function) 작업을 편리하게 할 수 있는 유용한 기능을 제공합니다. 고차 함수란 함수를 반환하거나 함수를 인자로 받는 함수를 의미하며, 고차 함수를 사용하여 함수나 호출 가능한 객체를 쉽게 재사용하거나 확장할 수 있어 유지보수가 용이한 코드를 작성할 수 있다는 장점이 있습니다.

# Functions

**functools**에서 제공하는 기능들은 아래와 같습니다.

## cache

```python{3}
from functools import cache

@cache
def some_fn(...):
    ...
```

파이썬 3.9 버전에 추가된 데코레이터로, 인자에 대한 함수 결과를 캐싱하여 동일한 인자로 함수가 다시 호출되었을 때 캐싱된 결과를 반환하는 함수입니다. 이 데코레이터는 함수의 결과를 캐시하기 위해 내부적으로 딕셔너리를 사용하여 저장합니다.

`cache` 데코레이터는 **함수의 인자 값을 키로 사용하여 함수의 결과를 캐싱하기 때문에 함수의 인자는 불변형(immutable)이어야 합니다.** 만약 리스트나 딕셔너리와 같이 가변형 파라미터를 사용하는 함수를 사용하는 경우 캐시에 문제가 발생할 수 있습니다. **또 동일한 인자에 대해 다른 함수 결과를 반환할 가능성이 있는 경우에 `cache` 데코레이터를 사용하는 경우에도 문제가 발생할 수 있습니다.**

또한 내부적으로 함수 결과를 딕셔너리를 사용해서 저장하기 때문에 **메모리 사용량**에 유의해야 합니다.

추가로 함수 결과를 저장할 때 전달된 인자 기준으로 함수 결과가 저장되기 때문에 키워드 인자를 사용하는 경우 예상한 캐시 결과가 반환되지 않을 수 있습니다.

```python{4}
from functools import cache
from time import sleep

@cache
def sleep_add(a: int, b: int, sleep_time: int = 5) -> int:
    sleep(sleep_time)

    return a + b

# 아래 4번의 호출 모두 캐싱된 결과를 사용하지 않고 5초 후 덧셈 결과가 출력됩니다.
print(sleep_add(1, 5))
print(sleep_add(1, 5, 5))
print(sleep_add(1, 5, sleep_time=5))
print(sleep_add(a=1, b=5, sleep_time=5))
```

위 코드의 주석과 같이 실제 함수 인자는 동일한 값이 전달되나 내부 딕셔너리에 결과를 저장되는 키 값이 모두 달라지기 때문에 5초 후 덧셈 결과가 출력됩니다. **이와 같은 결과가 발생하는 이유는 `cache` 데코레이터가 내부적으로 함수에 전달된 인자를 전달 받을 때 `*args, **kwargs` 와 같이 인자를 전달 받기 때문에 실제 함수에 키워드를 사용하냐 아니냐에 따라 `args` 또는 `kwargs` 에 전달되기 때문에 생성되는 키 값이 달라집니다.** 따라서 `cache` 데코레이터를 사용하는 경우 이러한 키워드 인자에도 유의해야 합니다.

## cached_property

```python{4}
from functools import cached_property

class SomeClass:
    @cached_property
    def some_property(self):
        ...
```

파이썬 3.8 버전에 추가된 데코레이터로, `cached_property`는 클래스에서 사용되는 `property`와 동일하게 동작하지만 **반환 값을 캐싱한다는 점**이 다릅니다. 클래스의 속성을 반환하는 `property`에서 속성을 반환하는데 오랜 시간이 걸릴 때 주로 사용합니다.

다만 기존의 `property` 데코레이터는 `setter`가 정의되지 않은 경우 속성 값 쓰기가 금지된 반면, **`cached_property` 데코레이터는 `setter` 정의 없이도 해당 속성에 쓰기가 가능합니다.**

캐시 된 값을 삭제할 필요가 있는 경우에는 해당 속성 값을 삭제하여 캐시 된 속성 값을 삭제할 수 있습니다.

## lru_cache

```python{3,7}
from functools import lru_cache

@lru_cache
def some_fn(...):
    ...

@lru_cache(max_size=128, typed=False)
def some_fn(...):
    ...
```

파이썬 3.2 버전에 추가된 데코레이터로, `cache` 데코레이터와 동일하게 함수의 결과 값을 캐싱하는 데코레이터입니다. **다만 최대 `maxsize`만큼의 함수 결과 값을 저장한다는 점이 다릅니다. 동일하게 함수 결과 값을 캐시하는데 내부적으로 딕셔너리를 사용하기 때문에 함수의 인자는 해시 가능한 불변형이어야 합니다.**

또한 키워드 인자를 사용 여부에 따라서도 다른 키 값을 갖습니다. 즉 서로 다른 인자 패턴에 대해 별도의 캐시 항목을 갖기 때문에 키워드 인자 사용에 유의가 필요합니다. 에를 들어 `f(a=1, b=2)`와 `f(b=2, a=1)`은 전달 되는 인자 값은 같으나 인자의 순서가 다르기 때문에 별도의 캐시 항목을 갖습니다.

위 코드의 두 번째 예시처럼 `lru_cache` 사용시 `max_size` 인자와 `typed` 인자를 전달할 수 있습니다.

`max_size` 인자는 몇 개의 캐시를 저장할지 나타내는 인자로 기본 값은 128이며, None으로 설정할 시 LRU 기능이 비활성화되고 캐시를 제한 없이 저장합니다. 만약 저장된 캐시의 수가 `max_size`에 도달하면 LRU 알고리즘에 의해 가장 오래된 캐시 값이 삭제됩니다.

`typed` 인자는 인자의 타입을 확인하는 인자로 True인 경우 같은 값을 같더라도 서로 다른 타입인 경우 별도의 캐시 값을 갖습니다. 예를 들어, `f(3)`과 `f(3.0)`은 `typed=True`인 경우 다른 캐시 값을 갖습니다.

## total_ordering

```python{3}
from functools import total_ordering

@total_ordering
class SomeClass:
    def __lt__(self, other):
        ...
```

파이썬 3.2 버전에 추가된 데코레이터로, 클래스의 매직 메소드 중 **비교하는데 사용되는 매직 메소드가 정의되어 있는 경우 비교하는데 사용되는 나머지 매직 메소드를 제공하는 데코데이터**입니다.

비교하는데 사용되는 매직 메소드는 `__lt__`, `__le__`, `__gt__`, `__ge__` 그리고 `__eq__` 입니다. `__eq__` 메소드와 앞의 4개의 메소드 중 하나라도 구현이 된 상태에서 `total_ordering` 데코레이터를 사용하면 해당 데코레이터가 나머지 메소드를 제공합니다.

이 데코레이터를 사용하면 모든 비교 연산에서 동작하는 클래스를 쉽게 구현할 수 있으나, 제공된 매직 메소드에서 실행 속도가 느려지고, 스택 트레이스(stack trace)가 복잡해지는 단점이 있습니다. 따라서 성능 벤치마킹 시에 이 부분이 병목임이 확인되면 `total_ordering` 데코레이터를 사용하지 않고 매직 메소드를 모두 구현하는 것이 바람직합니다.

## partial

```python
from functools import partial

# example
bin_num = partial(int, base=2)  # int 함수의 키워드 인자인 base 인자를 2로 고정합니다.
print(bin_num("10010"))  # 18
```

**함수의 위치 인자 또는 키워드 인자의 일부를 고정한 `partial` 객체를 반환하는 함수**입니다. `partial` 함수의 첫 번째 인자는 `partial` 객체로 변환할 함수이며, 위치 인자와 키워드 인자는 첫 번째 인자로 전달된 함수의 위치 인자와 키워드 인자로 고정하는데 사용됩니다.

위 코드 예시처럼 `int` 함수의 base 키워드 인자를 2로 고정한 `partial` 객체를 생성하여 이진수로 변환하는 함수를 쉽게 생성할 수 있습니다.

## partialmethod

```python{7,8}
from functools import partial_method

class SomeClass:
    def set_state(self, state):
        self._state = state

        set_alive = partialmethod(set_state, "alive")
        set_dead = partialmethod(set_state, "dead")
```

파이썬 3.4 버전에 추가된 `partialmethod` 함수는 `partial`과 동일하게 동작하지만 직접 호출이 아닌 메소드 정의로 사용되는 함수입니다.

## reduce

```python
from functools import reduce

reduce(lambda x, y: x + y, [1, 2, 3, 4, 5])  # 15
reduce(lambda x, y: x + y, [1, 2, 3, 4, 5], initializer=5)  # 20
```

`reduce` 함수는 순회 가능한 객체를 인자로 받아 **두 개의 인자를 전달 받는 함수를 사용하여 결과를 누적하는 함수**입니다. `reduce` 함수의 첫 번째 인자 `function`은 두 개의 인자를 받는 함수로 첫 번째 인자는 누적 결과를, 두 번째 인자는 순회 가능한 객체의 요소입니다. `reduce` 함수의 `initializer` 인자가 전달되면 누적 값은 `initializer`부터 시작하여 계산됩니다. `initializer`가 전달되지 않는 경우 누적 값은 객체의 첫 번째 요소부터 시작됩니다.

## singledispatch

```python{3,7,11,16}
from functools import singledispatch

@singledispatch
def some_fn(arg):
    print("Default Implementation")

@some_fn.register(int)
def _(arg):
    print("Integer Implementation")

@some_fn.register(str)
def _(arg):
    print("String Implementation")

# 데코레이터에 타입을 전달하는 대신 인자의 타입 어노테이션으로 사용할 수 있습니다.
@some_fn.register
def _(arg: str):
    print("String Implementation")
```

파이썬 3.4 버전에 추가된 `singledispatch` 데코레이터는 **함수를 제너릭 함수로 만드는 데코레이터**입니다.  제네릭 함수는 인자의 타입에 따라 다른 함수가 실행되도록 합니다. 일반적으로 파이썬은 동적 타입 언어로 인자의 타입을 제한할 필요가 있는 경우 함수 내부에서 검사하여 타입에 맞는 함수가 실행되도록 구현되는데, `singledispatch` 데코레이터를 사용하면 인자의 타입에 따라 다른 함수가 동적으로 실행되도록 할 수 있습니다.

## singledispatchmethod

```python{4,8,13}
from functools import singledispatchmethod

class SomeClass:
    @singledispatchmethod
    def some_method(self, arg):
        print("Default Method")

    @some_method.register(int):
    def _(self, arg):
        print("Integer Method")

    # 위 함수와 동일합니다.
    @some_method.register
    def _(self, arg: int):
        print("Integer Method")
```

`singledispatchmethod` 데코레이터는 `singledispatch` 데코레이터의 클래스 메소드 전용 데코레이터입니다.

## update_wrapper

```python{7}
from functools import update_wrapper

def some_decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)

    update_wrapper(wrapper, func)

    return wrapper
```

**데코레이터를 정의할 때 감싸진 함수의 속성을 사용하도록 wrapper 함수의 속성을 갱신하는 함수**입니다. 일반적으로 데코레이터를 생성할 때 사용되는 wrapper 함수는 원본 함수의 독스트링(docstring), 어노테이션(annotation) 등의 정보를 잃어버리는데, `update_wrapper` 함수는 이러한 정보를 유지할 수 있도록 도와줍니다.

`update_wrapper` 함수에 전달가능한 인자는 아래와 같습니다.

- `wrapper` : `wrapped` 함수를 감싼 함수로 `assigned` 인자와 `updated` 인자에 명시된 속성을 따라 `wrapped` 함수로부터 해당 속성 값을 가져옵니다.
- `wrapped` : 감싸지게 되는 원본 함수입니다.
- `assigned` : `wrapper` 함수에 할당할 속성명이 정의된 문자열 튜플로, 기본 값은 `functools.WRAPPER_ASSIGNMENTS` 이며 `(__module__, __name__, __qualname__, __doc__, __annotations__)` 와 같습니다.
- `updated` : `wrapper` 함수에서 업데이트할 속성명이 정의된 문자열 튜플로, 기본 값은 `functools.WRAPPER_UPDATES` 이며 `(__dict__,)` 와 같습니다.

## wraps

```python{4}
from functools import wraps

def some_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)

    return wrapper
```

**`wraps` 데코레이터는 데코레이터를 정의할 때 `update_wrapper`를 편하게 사용하기 위한 데코레이터**입니다. wrapper 함수를 정의할 때 `wraps(func)`와 같이 원본 함수를 인자로 전달한 `wraps` 데코레이터를 사용하여 원본 함수의 속성들을 `wrapper` 함수에 사용할 수 있습니다.
