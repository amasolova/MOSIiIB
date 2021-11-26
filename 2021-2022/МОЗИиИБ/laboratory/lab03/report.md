---
# Front matter
title: "Математические основы защиты информации и информационной безопасности. Лабораторная работа №3"
subtitle: "Шифрование гаммированием"
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

Ознакомиться с шифрованием гаммированием на примере гаммирования конечной гаммой.

# Задание

Реализовать алгоритм шифрования гаммированием конечной гаммой.

# Теоретическое введение

Гаммирование, или Шифр XOR, — метод симметричного шифрования, заключающийся в «наложении» последовательности, состоящей из случайных чисел, на открытый текст. Последовательность случайных чисел называется гамма-последовательностью и используется для зашифровывания и расшифровывания данных. Суммирование обычно выполняется в каком-либо конечном поле.

## Шифрование гаммированием

При шифровании гаммированием формируется `m` - разрядная случайная последовательность. Пусть `k` -  передаваемое сообщение
$$ k=k_{1}k_{2}...k_{i}...k_{m}, $$  
а `p` - последовательность, которая является ключом:
$$ p=p_{1}p_{2}...p_{i}...p_{m}, $$  
тогда `i`-ый символ криптограммы будет равен:
$$ c_{i} = p_{i} \oplus k_{i}, $$  
где $\oplus$ - операция побитового сложения XOR [@xor]. В результате криптограмму можно записать следующим образом:
$$ c=c_{1}c_{2}...c_{i}...c_{m} $$  

Более подробно о шифровании гаммированием: [@gammirovanie]

# Выполнение лабораторной работы

В рамках данной лабораторной работы был описан алгоритм шифрования гаммированием с конечной гаммой.  

## Описание реализации метода шифрования

В данной работе применяется схема однократного использования (рис. [-@fig:01]). К элементам ключа и исходного сообщения применяется побитовое сложение XOR, в результате чего формируется зашифрованное сообщение:  

![Схема однократного использования](image/1.png){ #fig:01 width=70% }

Для того, чтобы применить операцию побитового сложения, необходимо, чтобы ключ и исходное сообщение были одной длины. Для достижения данной цели, ключ растягивается до тех пор, пока не сравняется длиной с исходным сообщением следующим образом: пусть сообщение будет 
$$ SECURITY, $$  
длина 'm' которого равна 12, тогда ключ растягивается следующим образом:
$$ KEY \rightarrow KEYKEYKE $$
Таким образом, к сообщению и ключу одинаковой длины можно применить операцию побитового сложения XOR.


## Листинг

Код приведенной ниже программы реализован на языке python.

```
alphabet = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя"
ignore_symbols = '!.,@#$%^&*()-+={}[]<>/ '

def format_gamma(gamma, message_length):
    new_gamma = ""
    for i in range(message_length):
        new_gamma += gamma[i % len(gamma)]
    return new_gamma

def get_indexes(text):
    indexes = []
    for letter in text:
        try:
            indexes.append(alphabet.index(letter) + 1)
        except ValueError:
            indexes.append(ignore_symbols.index(letter) + 10000)
    return indexes

def encrypt(message_indexes, gamma_indexes):
    indexes = []
    for ix, item in enumerate(message_indexes):
        if message_indexes[ix] > 10000 or gamma_indexes[ix] > 10000:
            result = item
            indexes.append(result)
            continue
        result = (message_indexes[ix] + gamma_indexes[ix]) % len(alphabet)
        indexes.append(result)
    return indexes
            
def to_text(indexes):
    text = ""
    for index in indexes:
        if index > 10000:
            text += ignore_symbols[index - 10000]
            continue
        text += alphabet[index - 1]
    return text

message = input("Введите сообщение: ")
gamma = input("Введите ключ (гамма): ")
new_gamma = format_gamma(gamma, len(message))
print("\nПреобразование {} -> {}".format(gamma.upper(), new_gamma.upper()))
message_indexes = get_indexes(message)
print("\nВаше сообщение:\n{} ({})".format(message.upper(), message_indexes))
gamma_indexes = get_indexes(new_gamma)
print("\nВаша гамма:\n{} ({})".format(gamma.upper(), gamma_indexes))
encrypted_message_indexes = encrypt(message_indexes, gamma_indexes)
encrypted_message = to_text(encrypted_message_indexes)
print("\nЗашифрованное сообщение:\n{} ({})"
	.format(encrypted_message.upper(), encrypted_message_indexes))
```

## Полученные результаты

При запуске программы пользователю предлагается ввести сообщение, которое необходимо зашифровать, и ключ. После этого, если ключ и сообщения разной длины, из ключа исключаются лишние символы или добавляются в конец новые. Знаки препинания и пробелы, в свою очередь, игнорируются и остаются в неизменном виде. Для удобства, они нумеруются начиная с индекса 10000. В результате выполнения пользователь получает зашифрованное сообщение (рис. [-@fig:02]).  

![Результаты шифрования гаммированием](image/2.png){ #fig:02 width=70% }

# Выводы

В ходе выполнения данной лабораторной работы было выполнено ознакомление с шифрованием гаммированием на шифрования гаммированием с конечной гаммой.  
В результате проделанной работы был программно реализован этот метод шифрования.  
В итоге поставленные цели и задачи были успешно достигнуты.

# Список литературы{.unnumbered}

::: {#refs}
:::
