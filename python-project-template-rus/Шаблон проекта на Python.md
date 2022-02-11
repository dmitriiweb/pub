# Шаблон проекта на Python

Тред: как используя современные инструменты создать новый питоновский проект. TL;DR pyenv, poetry, gh-actions, tox, pytest, mkdocs
```shell
my-project/
 |----.git/
 |----docs/
 |     |----index.md
 |
 |----my_project/
 |----tests/
       |----__init__.py
```

![[intro.png]]

Содержание:
- [[Шаблон проекта на Python#environment]]
    - [[Шаблон проекта на Python#pyenv]]
    - [[Шаблон проекта на Python#poetry]]
    
- [[Шаблон проекта на Python#Линтеры]]
    - [[Шаблон проекта на Python#flake8]]
    - [[Шаблон проекта на Python#isort]]
    - [[Шаблон проекта на Python#black]]
    - [[Шаблон проекта на Python#mypy]]
 
- [[Шаблон проекта на Python#Документация]]
    - [[Шаблон проекта на Python#mkdocs]]
    
- [[Шаблон проекта на Python#Тесты]]
    - [[Шаблон проекта на Python#pytest]]
    - [[Шаблон проекта на Python#tox]]
    
- [[Шаблон проекта на Python#Makefile]]

- [[Шаблон проекта на Python#CI]]
    - [[Шаблон проекта на Python#pre-commit]]
    - [[Шаблон проекта на Python#gh-actions]]
    
## environment
### [pyenv](https://github.com/pyenv/pyenv)
Начнём с Pyenv. Pyenv - менеджер для работы с различными версиями питона. Позволяет изолировать рабочую версию от системной, а также легко переключаться между разными питоновскими версиями.
```shell

# Получить список всех доступных версий для установки
$ pyenv install --list

# Установить Python определённой версии
$ pyenv install 3.10.0

# Получить список установленных версий
$ pyenv versions

# Задать версию питона глобально
$ pyenv global 3.10.0

# Задать версию питона локально
$ cd my-project
$ pyenv local 3.10.0 3.8.12 3.9.7 3.7.12
# или
$ pyenv local 3.10.0
```

![[pyenv.png]]

### [poetry](https://python-poetry.org/)
Poetry - менеджер зависимостей с человеческим лицом. Вместо setup.py используется pyproject.toml, удобно работать с dev и production зависимости, а опубликовать библиотеку в pypi можно двумя командами.

```shell
# Инициализация проекта
$ poetry init

# Активация виртуального окружения
$ poetry shell

# Установка библиотеки
$ poetry add requests

# Установка dev зависимостей
$ poetry add --dev pytest pytest-cov pytest-asyncio tox mkdocs-material mkdocstrings pre-commit black isort mypy flake8
```
![[poetry.png]]

## Линтеры
С основным окружением мы закончили, теперь можно заняться красотой нашего проекта, т.е. линтерами и форматерами.

### flake8
[flake8](https://flake8.pycqa.org/en/latest/) - проверка кода на соответствие pep8, умеет находить импортированные/объявленные, но не используемые библиотеки/переменные.
```ini
# .flake8
[flake8]
max-line-length = 88
select = C,E,F,W,B,B9
ignore = E203, E501, W503
exclude = __init__.py
```

![[flake8.png]]

### isort
[isort](https://github.com/PyCQA/isort) - сортирует импорты по типу и алфавиту. Отредактируйте toml файл, чтобы добавить настройки
```toml
# pyproject.toml

[tool.isort]
profile = "black"
force_single_line = false
atomic = true
include_trailing_comma = true
lines_after_imports = 2
lines_between_types = 1
use_parentheses = true
src_paths = ["my_project", "tests"]
skip_glob = ["*/setup.py"]
filter_files = true
known_first_party = "my_project"
```
![[isort-config.png]]

### black
[black](https://github.com/psf/black) - чудо-инструмент, который умеет превращать мою писанину в читаемый код, т.е. форматирует код согласно заданным правилам.

```toml
# pyproject.toml

[tool.black]
line-length = 88
include = '\.pyi?$'
exclude = '''
/(
\.eggs
| \.git
| \.hg
| \.mypy_cache
| \.tox
| \.venv
| _build
| buck-out
| build
| dist
| tests/.*/setup.py
)/
'''
```
![[black-config.png]]


### mypy
[mypy](https://mypy.readthedocs.io/en/stable/) - проверка типов

```toml
# pyproject.toml

mypy_path = "my_project"
check_untyped_defs = true
disallow_any_generics = true
ignore_missing_imports = true
no_implicit_optional = true
show_error_codes = true
strict_equality = true
warn_redundant_casts = true
warn_return_any = true
warn_unreachable = true
warn_unused_configs = true
no_implicit_reexport = true
```

## Документация
### mkdocs
Для своих проектов уже давно использую [mkdocs](https://www.mkdocs.org/) вместе с [mkdocs-material](https://squidfunk.github.io/mkdocs-material/getting-started/), только посмотрите какая красота получается: https://fastapi.tiangolo.com/
```yml
# mkdocks.yml

site_name: My Project

plugins:
  - search
  - mkdocstrings

theme:
  name: "material"
  palette:
    scheme: slate
    primary: pink
    accent: pink


markdown_extensions:
   - markdown.extensions.admonition
   - markdown.extensions.attr_list
   - markdown.extensions.def_list
   - markdown.extensions.footnotes
   - markdown.extensions.meta
   - markdown.extensions.toc:
       permalink: true
   - pymdownx.arithmatex:
       generic: true
   - pymdownx.betterem:
       smart_enable: all
   - pymdownx.caret
   - pymdownx.critic
   - pymdownx.details
   - pymdownx.emoji:
       emoji_index: !!python/name:materialx.emoji.twemoji
       emoji_generator: !!python/name:materialx.emoji.to_svg
   - pymdownx.highlight
   - pymdownx.inlinehilite
   - pymdownx.keys
   - pymdownx.magiclink:
       repo_url_shorthand: true
       user: squidfunk
       repo: mkdocs-material
   - pymdownx.mark
   - pymdownx.smartsymbols
   - pymdownx.snippets:
       check_paths: true
   - pymdownx.superfences:
       custom_fences:
         - name: mermaid
           class: mermaid
           format: !!python/name:pymdownx.superfences.fence_code_format
   - pymdownx.tabbed
   - pymdownx.tasklist:
       custom_checkbox: true
   - pymdownx.tilde

nav:
  - My Project: index.md
  ```
  ![[mkdocks-config.png]]
  
```shell
# Запустить документацию на локальном сервере
$ mkdocs serve
  
# Опубликовать на gh-pages
$ mkdocs mkdocs gh-deploy --force
```
![[mkdocs-usage.png]]

## Тесты
### pytest
Надеюсь, никому не нужно объяснять для чего нужны тесты. Мы будем использовать [pytest](https://docs.pytest.org/en/7.0.x/) и несколько плагинов.

[pytest-asyncio](https://github.com/pytest-dev/pytest-asyncio) - плагин для написания асинхронных тестов.
```ini
# pytest.ini

[pytest]
asyncio_mode = auto
```
![[pytest-ini.png]]

pytest-cov - статистика (покрытие) тестов и рисует красивые таблички
![[coverage-output.png]]

### tox
[tox](https://tox.wiki/en/latest/) - хочется, чтобы приложение работало на большем количестве версий, что же для этого нужно? Ещё больше тестов!
```ini
# tox.ini

[tox]
isolated_build=True
envlist = py{37,38,39,310}, flake8, mypy, black, isort
skipsdist = True

[testenv]
deps =
	pytest
    pytest-cov
    pytest-asyncio
commands = pytest --cov=my_project -vv tests/

[testenv:flake8]
basepython = python3.8
deps = flake8
commands = flake8 my_project tests

[testenv:mypy]
basepython = python3.8
deps =
	mypy
commands = mypy my_project --implicit-reexport

[testenv:black]
basepython = python3.8
deps = black
commands = black my_project tests

[testenv:isort]
basepython = python3.8
deps = isort
commands = isort my_project tests
```
![[tox.ini.png]]

## Makefile
Меня пугает команда запусков тестов, давайте её сократим до make test. Создадим Makefile (формат файла пришёл из доисторических времён, когда люди ещё пользовались табами)
```
# Makefile

.PHONY: test
test:
	pytest --cov=my_project -vv tests/
	flake8 my_project tests/
	mypy my_project --implicit-reexport
	black my_project tests/
	isort my_project tests/

.PHONY: docs-serve
docs-serve:
	mkdocs serve

.PHONY: docs-publish
docs-publish:
	mkdocs mkdocs gh-deploy --force

.PHONY: publish
publish:
	poetry build
	poetry publish
```
![[makefile.png]]

## CI
### pre-commit
[pre-commit](https://pre-commit.com/) - препятствие на пути говнокода в репозиторий, запускает линтеры и форматеры перед каждым коммитом.
```yml
# .pre-commit-config.yml

repos:
  - repo: https://github.com/psf/black
    rev: 21.12b0
    hooks:
      - id: black

  - repo: https://gitlab.com/pycqa/flake8
    rev: 4.0.1
    hooks:
      - id: flake8

  - repo: https://github.com/timothycrosley/isort
    rev: 5.10.1
    hooks:
      - id: isort
        additional_dependencies: [toml]
        exclude: ^.*/?setup\.py$

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: ''  # Use the sha / tag you want to point at
    hooks:
      - id: mypy

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v3.4.0
    hooks:
      - id: trailing-whitespace
        exclude: |
          (?x)(
            ^tests/.*/fixtures/.*
            | ^tests/console/commands/debug/test_resolve.py
          )
      - id: end-of-file-fixer
        exclude: ^tests/.*/fixtures/.*
      - id: debug-statements
```
![[pre-commit.png]]

### gh-actions
Я - человек ленивый и не люблю лишнюю писанину, поэтому не будем использовать шаблоны от гитхаба, а применим магию
```yml
# .github/workflows/tests.yml

name: Tests

on:
  - push
  - pull_request

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        python-version: ['3.7', '3.8', '3.9', '3.10']

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install tox tox-gh-actions
    - name: Test with tox
      run: tox
```

```ini
# tox.ini

[gh-actions]
python =
	3.7: py37
    3.8: py38, flake8, mypy, black, isort
    3.9: py39
	3.10: py310
``` 
![[gh.png]]
![[tox-gh.png]]

В MD формате: https://github.com/dmitriiweb/pub/tree/main/python-project-template-rus
Код шаблона: https://github.com/dmitriiweb/template-project-python