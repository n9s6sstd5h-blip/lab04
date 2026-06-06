[![Build](https://github.com/n9s6sstd5h-blip/lab04/actions/workflows/build.yml/badge.svg)](https://github.com/n9s6sstd5h-blip/lab04/actions/workflows/build.yml)

## Laboratory work IV

Лабораторная работа посвящена настройке непрерывной интеграции для CMake-проекта из лабораторной работы №3.

Вместо Travis CI и AppVeyor используется GitHub Actions. Workflow собирает проект на нескольких операционных системах и явно передает выбранный компилятор в CMake.

## Build locally

```sh
cmake -S . -B _build -DCMAKE_INSTALL_PREFIX=_install
cmake --build _build
cmake --install _build
```

## Run

```sh
./_install/bin/hello_world
printf "1 -3 2\n" | ./_install/bin/solver
```

## CI matrix

| OS | Compiler |
| --- | --- |
| Ubuntu | GCC |
| Ubuntu | Clang |
| macOS | Clang |
| Windows | MSVC |
