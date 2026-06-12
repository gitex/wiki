---
tags:
    - программирование
    - кэширование
---

**Джиттер (англ. jitter - дрожание)** - это добавление случайного времени
к TTL, чтобы обновление записей не истекало/выполнялось разом.

Если у вас `TTL = 60s` и несколько кэшей, то через 60 секунд они вместе
истекут и пойдут обновляться, создавая пиковые нагрузки.
Если же `TTL = 60s +- random(0..10s)`, то один кэш
будет длиться 63 секунды, второй - 58 секунд, третий - 61 секунду
и обновление кэша размазывается по времени.

Примеры кода:

```python
def jitter(ttl: int) -> int:
    return ttl + random.randint(-ttl//10, ttl//10)

def jitter_abs(ttl: float, delta: float) -> float:
	""" jitter_abs(10.0, 2.0) -> [8.0..12.0] """
	return ttl + random.uniform(-delta, delta)

def jitter_pct(ttl: float, pct: float) -> float:
	""" jitter_pct(100.0, 0.1) -> [90.0..110.0] """
	delta = ttl * pct
	return ttl + random.uniform(-delta, delta)
```

