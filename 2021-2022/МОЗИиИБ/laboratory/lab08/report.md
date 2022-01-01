---
# Front matter
title: "Математические основы защиты информации и информационной безопасности. Лабораторная работа №8"
subtitle: "Целочисленная арифметика многократной точности"
author: |
 Студент: Масолова Анна Олеговна НФИмд-02-21  
 Преподаватель: Кулябов Дмитрий Сергеевич

# Generic otions
lang: ru-RU
toc-title: "Содержание"

# Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

# Pdf output format
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
### Fonts
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Misc options
indent: true
header-includes:
  - \linepenalty=10 # the penalty added to the badness of each line within a paragraph (no associated penalty node) Increasing the value makes tex try to have fewer lines in the paragraph.
  - \interlinepenalty=0 # value of the penalty (node) added after each line of a paragraph.
  - \hyphenpenalty=50 # the penalty for line breaking at an automatically inserted hyphen
  - \exhyphenpenalty=50 # the penalty for line breaking at an explicit hyphen
  - \binoppenalty=700 # the penalty for breaking a line at a binary operator
  - \relpenalty=500 # the penalty for breaking a line at a relation
  - \clubpenalty=150 # extra penalty for breaking after first line of a paragraph
  - \widowpenalty=150 # extra penalty for breaking before last line of a paragraph
  - \displaywidowpenalty=50 # extra penalty for breaking before last line before a display math
  - \brokenpenalty=100 # extra penalty for page breaking after a hyphenated line
  - \predisplaypenalty=10000 # penalty for breaking before a display
  - \postdisplaypenalty=0 # penalty for breaking after a display
  - \floatingpenalty = 20000 # penalty for splitting an insertion (can only be split footnote in standard LaTeX)
  - \raggedbottom # or \flushbottom
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Изучение целочисленной арифметики для больших чисел.

# Задачи

Реализовать программно алгоритмы сложения неотрицательных целых чисел, вычитания неотрицательных целых чисел, умножения неотрицательных целых чисел столбиком, быстрого столбика и деления многоразрядных целых чисел.

# Теоретические сведения

Для арифметических операций над большими целыми числами в системе счисления $b$, где $b$ - натуральное число, $b >= 2$, применяется запись этого числа посимвольно. Натуральное $b$-разрядное число будем записывать в виде
$$ u = u_{1} u_{2} ... u_{n} $$

## Арифметические операции

В данной работе операции по работе с большими целыми числами производится в некоторой системе счисления $b$. Предусматрвиается реализация алгоритмов сложения, вычитания, умножения и деления. На вход каждой из функций подается два числа и система счисления, на выходе возвращается результат арифметической операции.

Подробнее об арифметических алгоритмах: [@bc, @da].

# Выполнение работы

## Реализация алгоритмов

```
def algorithm1(u, v, n, b):
    j = n
    k = 0
    w = [None for i in range(j + 1)]
    while True:
        w[j] = (u[j - 1] + v[j - 1] + k) % b
        j = j - 1
        if j > 0:
            continue
        elif j == 0:
            w[0] = k
            break
    return w


def algorithm2(u, v, n, b):
    j = n
    k = 0
    w = [None for i in range(j)]
    while True:
        w[j - 1] = (u[j - 1] - v[j - 1] + k) % b
        j = j - 1
        if j > 0:
            continue
        elif j == 0:
            break
    return w


def algorithm3(u, v, b):
    m = len(v) - 1
    n = len(u) - 1
    w = [0 for i in range(n + m + 1)]
    j = m
    while True:
        if (v[j] == 0):
            w[j] = 0
        else:
            i = n
            k = 0
            while True:
                t = u[i] * v[j] + w[i + j] + k
                w[i + j] = t % b
                k = t / b
                i = i - 1
                if i > 0:
                    continue
                else:
                    w[j] = k
                    break
        j = j - 1
        if j > 0:
            continue
        elif j == 0:
            break
    return w


def algorithm4(u, v, b):
    t = 0
    m = len(v)
    n = len(u)
    w = [0 for i in range(n + m + 1)]
    for s in range(m + n):
        for i in range(s):
            t = t + (u[n - i - 1] * v[m - s + i - 1])
        w[m + n - s] = int(t % b)
        t = t / b
    return w


def algorithm5(u, v, b):
    n = len(u)
    t = len(v)
    q = [0 for j in range(n - t + 1)]
    while u >= v * (b ** (n - t)):
        q[n - t] += 1
        u = u - v * (b ** (n - t))
        for i in range(n, t + 1):
            if (u[i] >= v[t]):
                q[i - t - 1] = b - 1
            else:
                q[i - t - 1] = ((u[i] * b) + u[i - 1]) / v[t]
            while q[i - t - 1] * ((v[t] * b) + v[t - 1]) > u[i] * (b ** 2) + u[i - 1] * b + u[i - 2]:
                q[i - t - 1] -= 1
            u = u - q[i - t - 1] * (b ** (i - t - 1)) * v
            if u < 0:
                u = u + v * (b ** (i - t - 1))
                q[i - t - 1] -= 1
    r = u
    return q, r


if __name__ == '__main__':
    while True:
        try:
            result_code = int(input(
                """
Выберите алгоритм:
1 - Сложение неотрицательных целых чисел;
2 - Вычитание неотрицательных целых чисел;
3 - Умножение неотрицательных целых чисел столбиком;
4 - Быстрый столбик;
5 - Деление многоразрядных целых чисел
-------------------------
0 - Выход из программы
Введите номер операции: """
            ))
            if result_code > 5:
                print("Ошибка ввода!")
                continue
            if result_code == 0:
                break
        except:
            print("Ошибка ввода!")
            continue
        arr1 = input("Введите первое целое число: ")
        arr1 = list(arr1)
        arr2 = input("Введите второе целое число: ")
        arr2 = list(arr2)
        system = int(input("Введите систему счисления 2..16: "))
        arr1 = list(map(lambda x: int(x), arr1))
        arr2 = list(map(lambda x: int(x), arr2))

        if result_code == 1:
            print(algorithm1(arr1, arr2, len(arr1), system))

        if result_code == 2:
            print(algorithm2(arr1, arr2, len(arr1), system))

        if result_code == 3:
            print(algorithm3(arr1, arr2, system))

        if result_code == 4:
            print(algorithm4(arr1, arr2, system))

        if result_code == 5:
            print(algorithm5(arr1, arr2, system))

```

## Пример работы алгоритмов

На рис. [-@fig:001] представлен пример взаимодействия пользователя с алгоритмами через консольное меню: 

![Пример работы](image/1.png){ #fig:001 width=70% height=70%}

# Выводы

В ходе выполнения работы была успешно изучена целочисленная арифметика для больших чисел. Были программно реализованы алгоритмы сложения неотрицательных целых чисел, вычитания неотрицательных целых чисел, умножения неотрицательных целых чисел столбиком, быстрого столбика и деления многоразрядных целых чисел.

# Список литературы{.unnumbered}

::: {#refs}
:::