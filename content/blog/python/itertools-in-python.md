---
title: Itertools in Python
date: 2023-01-25 22:01:06
category: Python
thumbnail: { thumbnailSrc }
draft: false
---

# Introduction

파이썬에서 기본으로 제공하는 모듈로, **이터레이터(iterator)**와 관련된 기능을 제공합니다. 인자로 전달된 **반복가능한 객체**를 기반으로 무한히 반복하는 이터레이터를 생성하거나 특정 기능이 적용된 이터레이터를 생성할 수 있습니다. 예를 들어, itertools에서 제공하는 함수 중 `count(x)` 함수는 x부터 하나씩 순차적으로 증가하는 이터레이터를 반환합니다.

itertools에서 제공하는 함수들은 **빠르고 메모리 효율적**으로 동작하도록 구현되어 있어 이터레이터 관련해서 작업이 필요할 때 성능에 대한 고민 없이 itertools에서 제공하는 기능의 조합으로 빠르게 구현할 수 있습니다.

## Iterable and Iterator

파이썬에서 **iterable**은 반복 가능한 혹은 반복 가능한 객체를 의미합니다. 대표적인 반복 가능한 객체(Iterable Object)는 리스트, 문자열, 튜플 등이 있습니다. 이러한 반복가능한 객체는 `iter(iterable)` 함수를 통해 **이터레이터(Iterator)**로 변환할 수 있습니다. `iter()` 함수는 인자로 전달된 반복 가능한 객체 내부의 매직 메소드인 `__iter__` 함수를 호출하여 이터레이터를 생성합니다. 만약 `__iter__` 함수가 정의되어 있지 않은 경우 또 다른 매직 메소드인 `__getitem__`을 호출하여 첫 번째 인덱스부터 순차적으로 항목을 꺼내옵니다.

이렇게 생성된 이터레이터는 `next(iterator)` 함수를 통해 순차적으로 객체 내부의 항목에 접근할 수 있습니다. `next()`를 사용하여 호출된 이터레이터는 클래스 내부에 구현된 매직 메소드 `__next__`를 호출하여 객체 내부의 항목에 접근합니다. `next()` 함수를 사용하여 이터레이터의 모든 항목을 꺼낸 뒤 한 번 더 호출하면 `StopIteration` 예외를 발생시킵니다.

아래는 하나의 리스트를 생성하고, 이를 이터레이터로 변한한 뒤 모든 항목을 꺼낸 뒤 발생된 예외를 확인하는 코드입니다.

```python
a_list = [1, 2, 3, 4, 5]
a_iterator = iter(a_list)

next(a_iter)  # 1
next(a_iter)  # 2
next(a_iter)  # 3
next(a_iter)  # 4
next(a_iter)  # 5
next(a_iter)  # StopIternation 예외 발생
```

# Functions

**itertools**에서 제공하는 함수들은 아래와 같습니다.

## accumulate

```python
from itertools import accumulate

accumulate(iterable, func=operator.add, *, initial=None)
```

```python
accumulate([1, 2, 3, 4, 5])                # 1   3   6   10  15
accumulate([1, 2, 3, 4, 5], initial=100)   # 100 101 103 106 110 115
accumulate([1, 2, 3, 4, 5], operator.mul)  # 1   2   6   24  120
```

`iterable` 인자에 대해 계산된 함수 `func`의 누적 결과를 반환하는 이터레이터를 반환합니다.

func로 전달되는 함수는 두 개의 인자를 전달 받는 함수로 첫 번째 인자에 현재 누적 결과와 두 번째 인자에 새롭게 누적할 iterable 인자의 항목이 전달됩니다. func의 기본 인자인 operator.add는 두 인자의 덧셈 결과를 반환하는 함수입니다.

`initial` 인자가 전달되는 경우 initial 값부터 누적이 시작되어 반환된 이터레이터가 iterable보다 항목가 하나 더 많고, 기본 값인 None인 경우 iterable의 첫 번째 항목을 누적 결과 시작으로 사용합니다.

## chain

```python
from itertools import chain

chain(*iterables)
```

```python
# case 1
chain("abc", [1, 2, 3], ("x", "y", "z"))  # a b c 1 2 3 x y z

# case 2
array_2d = [[0, 1, 2], [3, 4, 5], [6, 7, 8]]
chain(*array_2d)  # 0 1 2 3 4 5 6 7 8 9
```

첫 번째로 전달된 반복가능한 객체에서 항목을 꺼내고 모든 항목을 꺼내면 다음 객체로 넘어가 항목을 꺼내오는 이터레이터를 생성합니다. 보통 여러 시퀀스를 하나로 연결한 단일 시퀀스처럼 처리하는 경우 사용합니다.

## combinations

```python
from itertools import combinations

combinations(iterable, r)
```

```python
# case 1
combinations("ABCD", 2)  # AB AC AD BC BD CD

# case 2
combinations(range(4), 3)  # 012 013 023 123
```

첫 번째 인자 `iterable`의 항목들을 사용하여 길이 `r`이 되는 조합을 항목으로 갖는 이터레이터를 반환합니다.

## combinations\_with\_replacement

```python
from itertools import combinations_with_replacement

combinations_with_replacement(iterable, r)
```

```python
combinations_with_replacement("ABC", 2)  # AA AB AC BB BC CC
```

첫 번째 인자 `iterable`의 항목들을 **중복으로** 사용하여 길이 `r`이 되는 모든 조합을 항목으로 갖는 이터레이터를 반환합니다.

## compress

```python
from itertools import compress

compress(data, selectors)
```

```python
# case 1
compress("ABCDEF", [1, 0, 1, 0, 1, 1])  # A C E F

# case 2
compress("ABCDEF", [True, False, True])  # A C

# case 3
compress("ABC", [True, False, True, False, True])  # A C
```

`data` 인자와 `selectors` 인자의 항목들을 같이 순회하면서 `selectors` 의 항목이 True인 `data` 의 항목만을 요소로 갖는 이터레이터를 반환합니다. 만약 `data` 인자와 `selectors` 인자의 길이가 같지 않으면 더 짧은 인자까지만 순회하고 중지합니다.

## count

```python
from itertools import count

count(start=0, step1)
```

```python
# case 1
count(10)  # 10 11 12 13 14 ...

# case 2
count(2.5, 0.5)  # 2.5 3.0 3.5 4.0 ...
```

`start` 인자부터 시작하여 `step`만큼 증가하는 무한한 이터레이터를 반환합니다.

## cycle

```python
from itertools import cycle

cycle(iterable)
```

```python
# case 1
cycle("ABCD")  # A B C D A B C D A B ...

# case 2
cycle([1, 2, 3])  # 1 2 3 1 2 3 1 2 ...
```

`iterable` 인자의 항목을 무한히 순회하는 이터레이터를 반환합니다.

## groupby

```python
from itertools import groupby

groupby(iterable, key=None)
```

```python
# case 1
groupby("AAAABBBCCDAA")  # (A, AAAA) (B, BBB) (C, CC) (D, D) (A, AA)

# case 2
for key, group in groupby([1, 1, 1, 2, 2, 3, 3, 3]):
  print(key, list(group))
  # 1, [1, 1, 1]
  # 2, [2, 2]
  # 3, [3, 3, 3]

# case 3
for k, g in groupby([1, 1, 2, 4, 6, 9, 11, 14, 14], key=lambda x: x % 2):
    print(k, list(g))
  # 1 [1, 1]
  # 0 [2, 4, 6]
  # 1 [9, 11]
  # 0 [14, 14]
```

`iterable` 인자에서 연속된 항목끼리 그룹으로 묶은 항목을 갖는 이터레이터를 반환합니다. 반환된 이터레이터 항목의 첫 번째 원소는 그룹으로 묶인 항목을 반환하고, 두 번째 원소는 묶인 그룹에 대한 이터레이터를 갖습니다.

`key` 인자는 그룹으로 묶을 때 사용되는 함수로 하나의 인자를 가지며 `iterable` 인자의 항목이 전달됩니다. `key` 인자로 전달된 함수를 통해 같은 값을 갖는 항목이 연속으로 존재하는 경우 그룹으로 묶이며 기본 값인 None은 항목 자체를 그대로 사용합니다.

## permutations

```python
from itertools import permutations

permutations(iterable, r=None)
```

```python
permutations("ABCD", 2)  # AB AC AD BA BC BD CA CB CD DA DB DC
```

첫 번째 인자 `iterable`의 항목을 사용하여 길이 `r`이 되는 모든 순열을 갖는 이터레이터를 반환합니다. `r` 인자를 따로 지정하지 않는 경우 `iterable` 인자의 길이를 사용합니다.

## product

```python
from itertools import product

product(*iterables, repeat=1)
```

```python
# case 1
product("ABCD", "xy")  # Ax Ay Bx By Cx Cy Dx Dy

# case 2
product(range(2), repeat=3)  # 000 001 010 011 100 101 110 111

# case 3
product("AB", range(2), repeat=2)  # A0A0 A0A1 A0B0 A0B1 A1A0 A1A1 ...
```

`*iterables` 인자로 전달된 모든 iterable 객체들 간의 데카르트 곱 결과를 항목으로 갖는 이터레이터를 반환합니다. 모든 iterable 객체를 순회하는 for-루프와 동일한 결과를 반환합니다. 예를 들어, `product(A, B)` 는 `(x, y) for x in A for y in B` 와 같은 결과를 갖습니다.

`repeat` 인자는 전달된 `*iterables` 인자를 `repeat` 반복한 데카르트 곱 결과를 계산할 때 사용합니다.

## repeat

```python
from itertools import repeat

repeat(object, times=None)
```

```python
repeat(10)  # 10 10 10 10 10 10 ...
repeat(10, 3)  # 10 10 10
```

`object` 인자를 반복해서 반환하는 이터레이터를 반환합니다. 만약 `times` 인자가 전달되면 `times` 만큼 반복하는 이터레이터를 반환하고, 전달되지 않거나 기본 값인 None이 전달되면 무한해서 반복하는 이터레이터가 생성됩니다.

## takewhile

```python
from itertools import takewhile

takewhile(predicate, iterable)
```

```python
takewhile(lambda x: x < 5, [1, 4, 6, 4, 1])  # 1, 4
takewhile(lambda x: x.isdigit(), "123abc456")  # "1", "2", "3"
```

`iterable` 인자의 항목을 `predicate` 함수 인자에 넣었을 때 False를 반환할 때까지의 `iterable` 인자의 항목을 반환하는 이터레이터를 반환합니다.

## zip_longest

```python
from itertools import zip_longest

zip_longest(*iterables, fillvalue=None)
```

```python
zip_longest(range(3), range(5))  #  (0, 0), (1, 1), (2, 2), (None, 3), (None. 4)
zip_longest("ABCD", "xy", fillvalue="-")  # ("A", "x"), ("B", "y"), ("C", "-"), ("D", "-")
```

`*iterables` 인자로 전달된 모든 iterable 객체들 중 가장 길이가 긴 객체만큼의 길이를 가지며, 각 객체들의 항목을 원소로 갖는 튜플을 반환하는 이터레이터를 반환합니다. 각 객체의 길이가 서로 달라 항목을 꺼낼 수 없는 객체의 경우 대신 `fillvalue` 인자 값이 튜틀의 원소 값으로 채워집니다.
