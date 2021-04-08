# Evrone Python Codestyle

[<img src="https://evrone.com/logo/evrone-sponsored-logo.png" width=300>](https://evrone.com/?utm_source=github.com&utm_campaign=evrone-python-codestyle)

## Основные принципы при написании кода
- **Поддерживаемость** (представьте, сможете ли вы понять свой код через год или через два)
- **Простота** (между сложным и простым решением следует выбрать простое)
- **Очевидность** (представьте, когда подключится новый программист, насколько ему будет понятно, почему именно **так** написан этот код)


## Пакетный менеджер

[poetry](https://python-poetry.org) - менеджер зависимостей и сборщик пакетов 


## Форматирование кода

[black](https://black.readthedocs.io/en/stable/) - автоформаттер кода по PEP8

Рекомендуемый конфиг в `pyproject.toml`:
```toml

[tool.black]
line-length = 100
target-version = ['py38']
exclude = '''
(
  \.eggs
  |\.git
  |\.hg
  |\.mypy_cache
  |\.nox
  |\.tox
  |\.venv
  |_build
  |buck-out
  |build
  |dist
)
'''

```


[isort](https://pycqa.github.io/isort/) - автоформаттер блока импортов

Рекомендуемый конфиг в `pyproject.toml`:
```toml

[tool.isort]
line_length = 100
sections = ["FUTURE", "STDLIB", "DJANGO", "THIRDPARTY", "FIRSTPARTY", "LOCALFOLDER"]
multi_line_output = 3
known_django = "django"
profile = "django"
src_paths = "app"
lines_after_imports = 2

```


[flake8](https://flake8.pycqa.org/en/latest/) - валидатор соответствия PEP8

Рекомендуемый конфиг в `.flake8`:
```ini
[flake8]
max-line-length = 100
max-complexity = 5
exclude = .venv,venv,**/migrations/*,snapshots
per-file-ignores =
    tests/**: S101
    **/tests/**: S101
```


## Pre-commit хуки

[pre-commit](https://pre-commit.com) - фреймворк для управления `pre-commit` хуками

Рекомендуемый конфиг `.pre-commit-config.yaml`:

```yaml
default_language_version:
    python: python3.8

repos:
  - repo: local
    hooks:
      - id: black
        name: black
        entry: black app
        language: python
        types: [python]

      - id: isort
        name: isort
        entry: isort app
        language: python
        types: [python]
```


## Типизация

[mypy](http://mypy.readthedocs.io) - чекер для статической типизации


Рекомендуемый конфиг `mypy.ini`:

```ini
[mypy]
ignore_missing_imports = True
allow_untyped_globals = True

[mypy-*.migrations.*]
ignore_errors = True

```


## Импорты

Плохо ❌:
```python
# spam.py
from . import foo, bar
```

Хорошо ✅:
```python

# spam.py
from some.absolute.path import foo, bar
```

**Почему?** Потому что абсолютный импорт явно определяет локацию (путь) модуля, который импортируется. При релативном
импорте всегда нужно помнить путь и вычислять в уме локацию модулей `foo.py`, `bar.py` относительно `spam.py`


## Размеры методов, функций и модулей

Предельный размер метода или функции - **50** строк.
Достижение предельного размера говорит о том, что функция (метод) делает слишком много - декомпозируйте действия внутри функции (метода). 


Предельный размер модуля - **300** строк.
Достижение предельного размера говорит о том, что модуль получил слишком много логики - декомпозируйте модуль на несколько.

Длина строки - 100 символов.


## Файлы `__init__.py`

В `__init__.py` файлах пишем только импорты.

**Почему?** Потому что `__init__.py` - последнее место, в которое посмотрит программист, который будет читать код в будущем.


## Докстринги
Рекомендуем добавлять докстринги в функции, методы и классы. 

**Почему?** Потому что программист, который впервые увидит ваш код, сможет быстрее понять, что в нем происходит. 
Код читается намного больше, чем пишется. 


## Документация к REST API
Рекомендуемый формат документации - [OpenAPI](https://www.openapis.org).
Схема для OpenAPI должна генерироваться "на лету", чтобы обеспечивать клиентов API свежими изменениями.

**Почему?** Потому что это один из распространенных форматов для документирования REST API, которая вышла из Swagger. Данный формат документации поддерживается большим количеством клиентов (Swagger, Postman, Insomnia Designer и многие другие). Также, рукописная документация имеет свойство быстро устаревать, а документация, которая генерируется напрямую из кода позволяет не думать о постоянном обновлении документации.


## Про Pull Request
**1 Pull Request = 1 issue**

Один Pull Request должен решать ровно одно issue.

**Почему?** Потому что ревьюверу сложнее держать контекст нескольких задач в голове и переключаться между ними.


## Размер Pull Request
Итоговый дифф PR не должен превышать +/- 600 измененных строк.

Плохо ❌:

![bad](https://user-images.githubusercontent.com/8825727/113953748-6fc7ba80-9853-11eb-9673-827995e54f73.png)
```
Дифф 444 + 333 = 777
```

Хорошо ✅:

![good](https://user-images.githubusercontent.com/8825727/113953831-a30a4980-9853-11eb-854b-d4c4f6559f2c.png)
```
Дифф 222 + 111 = 333
```


**Почему?** Потому что чем больше PR - тем более он становится неконтролируемым и мерж производится "закрыв глаза и заткнув уши". 
Также, большинству ревьюверов будет сложно воспринять такой объем изменений за один раз.
