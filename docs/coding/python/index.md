## Разное

### `__slots__`

Добавление `__slots__` отключает создание `__dict__` и `__weakref__`,
что может сэкономить память и ускорить создание класса.

```python
from dataclasses import dataclass

@dataclass
class Position:
    __slots__ = ['lon', 'lat']
    lon: float
    lat: float
```



