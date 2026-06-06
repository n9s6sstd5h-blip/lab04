# Отчет по лабораторной работе №4
## Системы непрерывной интеграции на примере GitHub Actions

**Студент:** n9s6sstd5h-blip  
**Дата выполнения:** 07.06.2026  
**Репозиторий:** https://github.com/n9s6sstd5h-blip/lab04.git

---

## 1. Подготовка проекта

### 1.1 Получение задания

```bash
git clone https://github.com/tp-labs/lab04.git tasks/lab04
```

Исходный репозиторий лабораторной работы содержит описание задания, лицензию и изображение `preview.png`.

### 1.2 Перенос проекта из лабораторной работы №3

Для выполнения лабораторной работы №4 был использован CMake-проект из предыдущей лабораторной работы:

```bash
git clone https://github.com/n9s6sstd5h-blip/lab03.git lab03
git clone https://github.com/n9s6sstd5h-blip/lab04.git lab04
rsync -a --exclude .git lab03/ lab04/
```

После переноса проект содержит:

```text
CMakeLists.txt
formatter_lib/
formatter_ex_lib/
solver_lib/
hello_world_application/
solver_application/
```

---

## 2. Настройка GitHub Actions

### 2.1 Причина замены Travis CI и AppVeyor

В исходном задании предлагаются Travis CI для Linux и AppVeyor для Windows. В данной работе используется GitHub Actions, потому что он встроен в GitHub и позволяет хранить сборку Linux, macOS и Windows в одном workflow.

### 2.2 Добавление значка сборки

В начало `README.md` добавлен значок GitHub Actions:

```markdown
[![Build](https://github.com/n9s6sstd5h-blip/lab04/actions/workflows/build.yml/badge.svg)](https://github.com/n9s6sstd5h-blip/lab04/actions/workflows/build.yml)
```

### 2.3 Создание workflow

Файл `.github/workflows/build.yml` описывает матрицу сборок:

```yaml
strategy:
  fail-fast: false
  matrix:
    include:
      - os: ubuntu-latest
        compiler: gcc
        cc: gcc
        cxx: g++
        cmake_args: -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++
      - os: ubuntu-latest
        compiler: clang
        cc: clang
        cxx: clang++
        cmake_args: -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++
      - os: macos-latest
        compiler: clang
        cc: clang
        cxx: clang++
        cmake_args: -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++
      - os: windows-latest
        compiler: msvc
        cc: cl
        cxx: cl
        cmake_args: -G "Visual Studio 17 2022" -A x64
```

Важный момент: выбранные варианты компилятора не только указаны в матрице, но и реально управляют конфигурацией CMake через переменные `CC`, `CXX` и параметры `-DCMAKE_C_COMPILER`, `-DCMAKE_CXX_COMPILER`. Для Windows используется генератор Visual Studio 2022, что выбирает компилятор MSVC.

Основные шаги workflow:

```yaml
- uses: actions/checkout@v4
- run: cmake -S . -B _build -DCMAKE_INSTALL_PREFIX=_install ${{ matrix.cmake_args }}
- run: cmake --build _build --config Release --parallel
- run: cmake --install _build --config Release --prefix _install
- run: ./_install/bin/hello_world
- run: printf "1 -3 2\n" | ./_install/bin/solver
```

Для Windows запуск выполняется через файлы с расширением `.exe`.

---

## 3. Доработка CMake-проекта

### 3.1 Обновление имени проекта

В корневом `CMakeLists.txt` имя проекта изменено с `lab03` на `lab04`:

```cmake
project(lab04)
```

### 3.2 Добавление правил установки

Для библиотек добавлены правила установки статических архивов и заголовочных файлов:

```cmake
install(TARGETS formatter
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(FILES formatter.h DESTINATION include)
```

Аналогичные правила добавлены для `formatter_ex` и `solver_lib`.

Для приложений добавлены правила установки исполняемых файлов:

```cmake
install(TARGETS hello_world
    RUNTIME DESTINATION bin
)
```

Аналогичное правило добавлено для приложения `solver`.

---

## 4. Локальная проверка сборки

### 4.1 Конфигурация CMake

```bash
cmake -S . -B _build -DCMAKE_INSTALL_PREFIX=_install
```

### 4.2 Сборка и установка

```bash
cmake --build _build
cmake --install _build
```

После установки появляются исполняемые файлы и библиотеки:

```text
_install/bin/hello_world
_install/bin/solver
_install/include/formatter.h
_install/include/formatter_ex.h
_install/include/solver.h
_install/lib/libformatter.a
_install/lib/libformatter_ex.a
_install/lib/libsolver_lib.a
```

### 4.3 Проверка приложений

```bash
./_install/bin/hello_world
printf "1 -3 2\n" | ./_install/bin/solver
```

Ожидаемый вывод:

```text
-------------------------
hello, world!
-------------------------
-------------------------
x1 = 1.000000
-------------------------
-------------------------
x2 = 2.000000
-------------------------
```

---

## 5. Итог

В лабораторной работе №4 выполнены следующие действия:

- проект из лабораторной работы №3 перенесен в репозиторий `lab04`;
- Travis CI и AppVeyor заменены на GitHub Actions;
- добавлен бейдж сборки в `README.md`;
- создан workflow `.github/workflows/build.yml`;
- настроена матрица сборок для Ubuntu GCC, Ubuntu Clang, macOS Clang и Windows MSVC;
- выбранный компилятор реально передается в CMake;
- добавлены правила установки для библиотек, заголовочных файлов и приложений;
- выполнена локальная проверка сборки, установки и запуска приложений.
