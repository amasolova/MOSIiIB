---
## Front matter
lang: ru-RU
title: "Математические основы защиты информации и информационной безопасности. Лабораторная работа №7. Дискретное логарифмирование в конечном поле"
author: |
 Масолова Анна Олеговна, НФИмд-02-21  
 Преподаватель: Кулябов Дмитрий Сергеевич
institute: Российский Университет Дружбы Народов
date: 11 декабря, 2021, Москва, Россия

## Formatting
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
toc: false
slide_level: 2
theme: metropolis
header-includes: 
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
 - '\makeatletter'
 - '\beamer@ignorenonframefalse'
 - '\makeatother'
aspectratio: 43
section-titles: true
---

# Цели и задачи

## Цель лабораторной работы

Изучение алгоритма p-Полларда для задач дискретного логарифмирования.

## Задачи

Реализовать программно алгоритм, реализующий p-метод Полларда для задач дискретного логарифмирования

# Выполнение лабораторной работы

## Дискретное логарифмирование

Задача обращения функции $g^{x}$ в некоторой конечной мультипликативной группе $G$.

## Дискретное логарифмирование

Наиболее часто задачу дискретного логарифмирования рассматривают в мультипликативной группе кольца вычетов или конечного поля, а также в группе точек эллиптической кривой над конечным полем. Эффективные алгоритмы для решения задачи дискретного логарифмирования в общем случае неизвестны.

## Дискретное логарифмирование

Для заданных $g$ и $a$ решение $x$ уравнения $g^{x}=a$ называется дискретным логарифмом элемента $a$ по основанию $g$.

## p-метод Полларда для задач дискретного логарифмирования

* Вход. Простое число $p$, число $a$ порядка $r$ по модулю $p$, целое число $b$, $1<b<p$; отображение $f$, обладающее сжимающими свойствами и сохраняющее вычислимость логарифма.
* Выход. Показатель $x$, для которого $a^{x} ≡ b (mod p)$, если такой показатель существует.

## p-метод Полларда для задач дискретного логарифмирования

1. Выбрать произвольные числа $u,v$ и положить $c = a^{u}b^{v}(mod p), d = c$
2. Выполнять $c = f(c)(mod p), d = f(f(d))(mod p)$, вычисляя при этом логарифмы для $c$ и $d$ как линейные функции от $x$ по модулю $r$, до получения равенства $c ≡ d (mod p)$
3. Приравняв логарифмы для $c$ и $d$, вычислить логарифм $x$ решением сравнения по модулю $r$. Результат: $x$ или "Решений нет".

## Пример работы p-алгоритма Полларда

![Пример работы p-алгоритма Полларда](image/1.png){ #fig:001 width=70% height=70%}

# Выводы

## Результаты выполнения лабораторной работы

В ходе выполнения работы был успешно изучен p-метод Полларда для задач дискретного логарифмирования, а также был реализован программно на языке Python.