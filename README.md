# Основы JS за 30 минут

Туториал рассчитан на программистов / сисадминов, не знающих или плохо знающих JS.
Основной акцент сделан на особенности JS, отличающие его от остальных языков
и неочевидные вещи. Также даются практические советы по обхождению / минимизации недостатков языка.

## Синтаксис JS похож на C, Java, PHP

```js
// Однострочный комментарий 

/*
  Многострочный
  комментарий
*/

1 // Число

"hello" // Строка

true // Булевское значение

[1, 2, 3] // Массив

{foo: "bar"} // Запись

new Date("1999-01-01") // Дата

(x + 2) * 3 // Арифметические операции, скобки

(x && y || z) // Логические операции

add(x, 2) // Вызовы функций

let x = "He-he" // Присвоения переменных

// и т.д. 
```

Если вы умеете программировать на другом языке, вам, скорее, понадобится
обратить внимание на особенности JS (чему и посвящён данный туториал).

## JS – это язык с динамической типизацией

С точки зрения статической типизации, в JS все выражения (expressions) имеют тип `any` и все операции разрешены. 
В рантайме, выражению присваивается определённый тип, который может меняться.  

JS использует автоприведение типов (коэрцию), в случае обнаружения операндов
различных типов:

```js
true * 7 // 7 
// 1 * 7
```

Операция умножения является арифметической, а не логической, поэтому `boolean` приводится к типу `number`
(`true -> 1`, `false -> 0`) и операция выполняется.

В интернете есть множество примеров и таблиц, описывающих правила коэрции ("js wtf stackoverflow").
Почти все они основаны на произвольной логике которая, в принципе, могла бы быть иной. 

Например:

```js
1 + "1" // "11"
1 - "1" // 0
```

Правила коэрции для сложения выбраны в пользу строк.
Правила коэрции для вычитания – в пользу чисел. 

С нашей точки зрения, запоминать эти правила не имеет никакого смысла.
Осознанное их использование свидетельствует об очень низком уровне программиста.

Если вас спрашивают подобные вещи на собеседовании – это повод задуматься *куда вы устраиваетесь*.
Зазубривание данных таблиц *на память* не сделает вас лучшим программистом. Скорее – это забивание памяти ненужным хламом. 
Незнание "правил" коэрции никоим образом не скажется на вашей продуктивности и качестве кода. Со временем, вы, так или иначе, 
запомните некоторые из них (что также не будет иметь никакого значения).

Сами создатели JS и все профессионалы считают автоприведение типов серьёзным просчётом (в дизайне языка).
В том же Python, `1 + "1"` порождает рантайм ошибку, что лучше чем коэрция (но хуже чем ошибка компиляции).
К сожалению, это решение уже невозможно пересмотреть. JS содержит оператор `===`, который может послужить паллиативным
решением в некоторых случаях (см. ниже). Однако, с нашей точки зрения, гораздо продуктивнее оказывается думать о типах как о статических.

Наш совет – не концентрироваться на этом аспекте и стремиться не создавать ситуации, 
полагающиеся на (авто-)коэрцию в своём коде. 

В хорошем коде, переменные не меняют свой тип, а выражения, в которых возможно использование разных типов, 
обрабатываются вручную: `1 + parseInt(x)`, `String(x) + "1"`.

Далее по тексту мы будем использовать фразу "операция определена" как для языков со статической типизацией.
Разница будет состоять в том, что во втором случае неопределённая операция будет означать ошибку этапа компиляции.
А в первом случае – получение "неведомой фигни" в рантайме. 

Хотя `true / true` синтаксически корректно, его семантика нас не интересует, т.к. она выбрана произвольно
и не должна использоваться. Подобные операции мы будем считать "неопределёнными".

## Числа

В JS есть только один тип числа (64-bit IEEE 754 double), называемый `number`.
Его достаточно для хранения целых чисел с точностью до 9×10¹⁵.

```js
-1
0
42
99.9
```

Для чисел определены [арифметические операции](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators):

```js
1 + 1  // 2   сложение
1 - 1  // 0   вычитание
10 * 2 // 20  умножение
5 / 2  // 2.5 деление
35 % 5 // 0   деление по модулю
```

Double ведут себя (не)предсказуемо (гугл в помощь): 

```js
0.1 + 0.2 // 0.30000000000000004
```

Порядком вычисления можно управлять с помощью скобок:

```js
(1 + 3) * 2 // 8
```

Есть три специальных значения, возникающие в результате арифметич. операций, но не являющиеся настоящими числами:

```js
Infinity  // бесконечность       (1 / 0)
-Infinity // минус бесконечность (-1 / 0)
NaN       // не число            (0 / 0)
```

Для чисел определены [операторы сравнения](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators):

```js
// Проверка на равенство
2 == 1 // false   
2 == 2 // true   

// Проверка на "больше"
1 > 2 // false
2 > 1 // true 

// Проверка на "больше или равно"
1 >= 2 // false
2 >= 1 // true

// Проверка на "меньше"
2 < 1 // false
1 < 2 // true

//  Проверка на "меньше или равно"
2 <= 1 // false  
1 <= 2 // true
```

Стоит помнить, что проверка на равенство не рекомендуется для Double в общем случае (неуправляемая точность).

Для чисел определены [битовые операции](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators):

```js
// Битовая инверсия
~0 // -1

// Сдвиги влево и вправо (деление и умножение на 2...)
1 << 2 // 4
2 << 2 // 8
8 >> 1 // 4
8 >> 2 // 2

// Битовый AND
5 & 1 // 1
5 & 3 // 1
5 & 0 // 0

// Битовый OR
5 | 1 // 5
5 | 3 // 7
5 | 0 // 5

// Битовый XOR
7 ^ 7 // 0
0 ^ 7 // 7
```

Битовые операции требуют понимания как числа представляются в двоичной системе (`5 -> ...000101`) 
и в классическом веб-программировании используются очень редко.

## Булеаны

Данный тип ничем не примечателен.

```js
true
false
```

Для булевских значений определены булевские операции и операция сравнения:

```js
// Логический AND (конъюнкция)
true && true  // true
true && false // false

// Логический OR (дизъюнкция)
true || true  // true
true || false // true

// Логический NOT (отрицание)
!true  // false
!false // true

// Сравнение
true == true  // true
true == false // false
```

`&&` приоритетнее `||`:

```js
x1 && x2 || y1 && y2 // (x1 && x2) || (y1 && y2)
```

## Строки

TODO

## Массивы

TODO

## Записи

TODO

## Даты

TODO

## Регулярные выражения

TODO


## Юниты

`null` и `undefined`

## Примитивные и объектные типы

TODO

## Строгое и нестрогое равенство

TODO 

