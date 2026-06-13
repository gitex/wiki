**pytest** - фреймворк для тестирования, который позволяет легко и
быстро писать тесты.

## Флаги

- `--lf` или `--last-failed`. Запускать только те тесты, которые упали
в предыдущем запуске.
- `-x` или `--exitfirst`. Заканчивать тестирование на первом неудачном
тесте.
- `-l` или `--showlocals`. Показывать локальные переменные теста, если
тест упадёт.
- `--ff` или `--failed-first`. Запускать сначала тесты, которые упали
в предыдущем запуске, а затем - остальные.
- `--nf` или `--new-first`. Запускать новые тесты, а затем - остальные.
- `--durations=N`. Выдает `N` самых медленных текстов. Если `N=0`, то
показывает все тесты от медленных к быстрым.

## Полезные модули

- [pytest-cov](https://github.com/pytest-dev/pytest-cov). Добавляет тестирование покрытия кода.
- [pytest-xdist](https://github.com/pytest-dev/pytest-xdist). Запуск тестов в несколько потоков.
- [pytest-timeout](https://github.com/pytest-dev/pytest-timeout). Добавляет параметр `--timeout=N`, который указывает
максимальное время для теста.

## Фикстуры

Фикстуры - это функции, которые запускаются до запуска теста.

```python
--- conftest.py ---

import pytest

@pytest.fixture
def something() -> int:
    return 42

# Фикстуры могут быть наследовать другие фикстуры
@pytest.fixture
def something_else(something) -> int:
    return something + 1

# Фикстуры могут содержать post-операции (закрытие соединений,
# удаление файлов и так далее)
@pytest.fixture
def db():
    db = create_db()
    yield db
    db.close()  # Будет вызвано после завершения теста

--- test_math.py ---
def test_something(something):
    assert something == 42

def test_something_else(something_else):
    assert something_else == 43
```

## Тесты с несколькими параметрами (parametrized testing)

Pytest позволяет легко создавать тесты, которые могут использовать больше
одного параметра за тест.

```python
# --- math.py ---
def sum(a: int, b: int) -> int:
    return a + b

# --- test_math.py ---
@pytest.mark.parametrize("a,b,expected", [
    (1, 1, 2),
    (2, 2, 4),
    (2, 3, 5),
])
def test_sum(a, b, expected):
    assert sum(a, b) == result
```
В этом случае будет создано 3 теста, каждый из которых соответствует
одному из параметров.

В некоторых случаях для читаемости логов тестов полезно присваивать параметрам имена:
```python
@pytest.mark.parametrize('a,b,expected', [
    pytest.param((1, 1, 2), id='1+1=2'),
    pytest.param((2, 2, 4), id='2+2=4'),
])
def test_sum(a, b, expected):
    ...
```

## Полезные встроенные фикстуры

### tmp_path

Используйте `tmp_path` для доступа к временной директории.
`tmp_path` - это `pathlib.Path`.
```python
def test_create_file(tmp_path):
    d = tmp_path / 'sub'
    d.mkdir()
    p = d / 'hello.txt'
    p.write_text("Something")
    assert p.read_text() == "Something"
```

### capsys

`capsys` позволяет получать вывод `stdout` и `stderr`.

```python
# --- somefile.py ---
def greeting(name):
    print(f"Привет, {name}")

# --- test_greetings.py ---
from _pytest.capture import CaptureFixture

def test_greeting(capsys: CaptureFixture[Any]):
    greeting("человек")
    out, err = capsys.readouterr()
    assert out == "Привет, человек\n"
    assert err == ""
```

## Ссылки

- [Документация Pytest](https://docs.pytest.org/en/stable/index.html)
