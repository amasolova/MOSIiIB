---
# Front matter
title: "Математические основы защиты информации и информационной безопасности. Лабораторная работа №7"
subtitle: "Дискретное логарифмирование в конечном поле"
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

Изучение алгоритма p-Полларда для задач дискретного логарифмирования.

# Задачи

Реализовать программно алгоритм, реализующий p-метод Полларда для задач дискретного логарифмирования

# Теоретические сведения

Задача обращения функции $g^{x}$ в некоторой конечной мультипликативной группе $G$.

Наиболее часто задачу дискретного логарифмирования рассматривают в мультипликативной группе кольца вычетов или конечного поля, а также в группе точек эллиптической кривой над конечным полем. Эффективные алгоритмы для решения задачи дискретного логарифмирования в общем случае неизвестны.

Для заданных $g$ и $a$ решение $x$ уравнения $g^{x}=a$ называется дискретным логарифмом элемента $a$ по основанию $g$.

## p-метод Полларда для задач дискретного логарифмирования

* Вход. Простое число $p$, число $a$ порядка $r$ по модулю $p$, целое число $b$, $1<b<p$; отображение $f$, обладающее сжимающими свойствами и сохраняющее вычислимость логарифма.
* Выход. Показатель $x$, для которого $a^{x} ≡ b (mod p)$, если такой показатель существует.

1. Выбрать произвольные числа $u,v$ и положить $c = a^{u}b^{v}(mod p), d = c$
2. Выполнять $c = f(c)(mod p), d = f(f(d))(mod p)$, вычисляя при этом логарифмы для $c$ и $d$ как линейные функции от $x$ по модулю $r$, до получения равенства $c ≡ d (mod p)$
3. Приравняв логарифмы для $c$ и $d$, вычислить логарифм $x$ решением сравнения по модулю $r$. Результат: $x$ или "Решений нет".

Подробнее об алгоритме: [@pollard, @descret]

# Выполнение работы

## Реализация алгоритмов

```
def ext_euclid(a, b):
    if b == 0:
        return a, 1, 0
    else:
        d, xx, yy = ext_euclid(b, a % b)
        x = yy
        y = xx - (a // b) * yy
        return d, x, y


def inverse(a, n):
    return ext_euclid(a, n)[1]


def xab(x, a, b, value):
    (G, H, P, Q) = value
    sub = x % 3

    if sub == 0:
        x = x * value[0] % value[2]
        a = (a + 1) % Q

    if sub == 1:
        x = x * value[1] % value[2]
        b = (b + 1) % value[2]

    if sub == 2:
        x = x * x % value[2]
        a = a * 2 % value[3]
        b = b * 2 % value[3]

    return x, a, b


def verify(g, h, p, x):
    return pow(g, x, p) == h


def pollard(G, H, P):
    Q = int((P - 1) // 2)

    x = G * H
    a = 1
    b = 1

    X = x
    A = a
    B = b

    for i in range(1, P):
        x, a, b = xab(x, a, b, (G, H, P, Q))

        X, A, B = xab(X, A, B, (G, H, P, Q))
        X, A, B = xab(X, A, B, (G, H, P, Q))

        if x == X:
            break

    nom = a - A
    denom = B - b

    res = (inverse(denom, Q) * nom) % Q

    if verify(G, H, P, res):
        return res

    return res + Q


if __name__ == '__main__':
    args = [(10, 64, 107)]
    for arg in args:
        res = pollard(*arg)
        print("{} ** {} ≡ {} (mod {})".format(arg[0], res, arg[1], arg[2]))
        print("Verify result: ", end="")
        if verify(arg[0], arg[1], arg[2], res):
            print("verified")
        else:
            print("not verified")

```

## Пример алгоритма p-Полларда для задач дискретного логарифмирования

На рис. [-@fig:001] представлены результаты работы p-метода Полларда для задач дискретного логарифмирования: 

![Пример алгоритма p-Полларда](image/1.png){ #fig:001 width=70% height=70%}

# Выводы

В ходе выполнения работы был успешно изучен p-метод Полларда для задач дискретного логарифмирования, а также был реализован программно на языке Python.

# Список литературы{.unnumbered}

::: {#refs}
:::