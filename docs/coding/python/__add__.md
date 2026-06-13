Поведение `__add__` порой бывает не совсем очевидно. Давайте разберем некоторые случаи.

```python
class Name:
    def __str__(self):
        return str(self.__class__.__name__)

class A(Name):
    def __add__(self, other):
        return str(self) + str(other)

class B(Name): ...

A() + B()
```
**Результат**: `AB`.
Был вызван `A.__add__(B)`.

Что будет если убрать `__add__` и добавить правому операнду `__radd__`?

```python
class A(Name): ...
class B(Name):
    def __radd__(self, other):
        return str(self) + str(other)

A() + B()
```
Результат: `BA`. Если в левом операнде нет `__add__`, то python ищет
`__radd__` в правом операнде. Фактически, это просто вызов `B.__radd__(A)`.

Давайте попробуем добавить и `__add__`, и `__radd__`.

```python
class A(Name):
    def __add__(self, other):
        print("Call A.__add__")
        return str(self) + str(other)

class B(Name):
    def __radd__(self, other):
        print("Call B.__radd__")
        return str(self) + str(other)

A() + B()
```
Результат:
```bash
Call A.__add__
'AB'
```
Ожидаемый `A.__add__(B)`. А что если `B` наследуется от `A`?

```python
class A(Name):
    def __add__(self, other):
        print("Call A.__add__")
        return str(self) + str(other)

class B(A):
    def __radd__(self, other):
        print("Call B.__radd__")
        return str(self) + str(other)

A() + B()
```
Результат:
```bash
Call B.__radd__
'BA'
```
Это странное поведение происходит потому, что `B` наследуется от `A` и для python
`B` является более сложным типом, которому отдается приоритет.

Поведение становится понятным, если разобрать его на реальном примере:
```python
class Vector: ...
class Matrix(Vector): ...

Vector() + Matrix()
```
Тут становится логично, что *более сложный тип лучше знает, как ему
складываться с более простым типом*. `Matrix`, как более сложный тип, знает про
`Vector` и, вероятно, содержит особую имплементацию сложения с ним.
