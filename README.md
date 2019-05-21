# *Лучшие практики JavaScript*

1. [Размещение скриптов внизу страницы](#point_1)
1. [Объявления вначале](#point_2)
1. [Инициализация переменных при их объявлении](#point_3)
1. [Уменьшение количества глобальных переменных](#point_4)
1. [Использование `===` вместо `==`](#point_5)
1. [Использование литеральной нотации при создании объекта](#point_6)
1. [Не использовать объекты оборачивающие типы](#point_7)
1. [Не использовать `eval()` без необходимости](#point_8)
1. [Не передавать строку в `SetInterval` или `SetTimeOut`](#point_9)
1. [Проведение проверки атрибутов при использовании `for in`](#point_10)

<a name='point_1'></a>
### Размещение скриптов внизу страницы

Основная цель этого совета — заставить страницу грузиться как можно быстрее. Когда браузер грузит скрипт он не продолжит рендеринг пока весь файл не будет загружен. Таким образом пользователю придется ждать дольше.
Если JS-скрипты служат для добавления функционала — например, обработки кликов кнопки то стоит перенести скрипты вниз поставив их перед закрывающимся тегом `body`.

```html
    <p>And now you know my favorite kinds of corn.</p>  
    <script type="text/javascript" src="path/to/file.js"></script>
    <script type="text/javascript" src="path/to/anotherFile.js"></script>
  </body>
</html>
```

<a name='point_2'></a>
### Объявления вначале

Хорошей парктикой является размещать все объявления вначале скрипта или функции.
Это делает код более понятным, предоставляет одно места для поиска локальных переменных, уменьшает вероятность нежелательных повторных объявлений.

```js
// объявление вначале
let firstName, lastName, price, discount, fullPrice;

// использование позднее
firstName = "John";
lastName = "Doe";

price = 19.90;
discount = 0.10;

fullPrice = price * 100 / discount;
This also goes for loop variables:

// объявление вначале
let i;

// использование позднее
for (i = 0; i < 5; i++) {
```

<a name='point_3'></a>
### Инициализация переменных при их объявлении

Хорошей практикой программирования является инициализация переменных при их объявлении.
Это делает код чище, предоставляет единое место для инициализации переменных, позволяет избегать неопределенного значения.

```js
// объявление и инициализация вначале
let firstName = "";
let lastName = "";
let discount = 0;
let fullPrice = 0;
let myArray = [];
let myObject = {};
```

<a name='point_4'></a>
### Уменьшение количества глобальных переменных

Глобальные переменные — переменные, которые доступны из любого участка кода, даже если они загружены в разные .js файлы. Любая переменная всегда доступна для изменения, что может привести к непреднамеренной перезаписи значений.

```js
//bad
let name = 'Jeffrey';
let lastName = 'Way';

function doSomething() {...}

console.log(name); // Jeffrey -- or window.name

//good
let DudeNameSpace = {
  name : 'Jeffrey',
  lastName : 'Way',
  doSomething : function() {...}
}
console.log(DudeNameSpace.name); // Jeffrey
```
<a name='point_5'></a>
### Использование `===` вместо `==`

_**JavaScript**_ использует два разных типа операторов равенства: `=== | ! ==` и `== | ! =` Рекомендуется всегда использовать первый набор при сравнении.
Оператор `==` сравнивает на равенство, а вот `===` — на идентичность. Плюс оператора `===` состоит в том, что он не приводит два значения к одному типу. Именно из-за этого он обычно и используется.

```js
abc  == undefined;  // true, если abc = undefined | null
abc === undefined;  // true - только если abc = undefined!

abc  == false;  // true, если abc = false | 0 | '' | []
abc === false; // true, только если abc = false!
```

<a name='point_6'></a>
### Использование литеральной нотации при создании объекта

Для создания объекта желательно использовать фигурные скобки, а не через конструктор `new Object`.
Литеральная нотация короче конструктора, также она подчеркивает, что объект — это всего лишь изменяемый хэш, а не нечто, создаваемое по шаблону, заданному классом.

```js
//good
let item = {};
//bad
let item = new Object();
```

Кроме того, использование конструктора `Object` вынуждает интерпретатор проверять, не переопределена ли эта функция в локальном контексте.
Также он принимает аргумент, и в зависимости от его типа может поручить создание объекта другому встроенному в язык конструктору - в итоге мы получим не тот объект, что ожидали. Это полезно знать в случаях, когда приходится использовать какой-то старый код.

```js
//пустой объект
let o = new Object();
o.constructor === Object; // true

let o = new Object(1);
o.constructor === Number; //true 

let o = new Object("string");
o.constructor === String; //true
//мы не предполагали, что созданный объект будет иметь этот метод:
typeof o.substring; // "function"
```
Такое поведение конструктора `Object` может привести к неожиданным результатам, если мы передаем в него значение, неизвестное на этапе выполнения.

<a name='point_7'></a>
### Не использовать объекты оборачивающие типы

Нежелательно также использовать конструкторы `Number`, `String` или `Boolean` для объявления числа, строки или логического значения.
Объявление этих типов как объектов замедляет скорость выполнения и вызывает неприятные побочные эффекты:

```js
let x = "John";
let y = new String("John");
(x === y) //false т.к. x - строка, а y - объект

var x = new String("John");
var y = new String("John");
(x == y) // false, не можем сравнить объекты

```

Поэтому для создания примитивов используются литералы:
```js
// good
let x2 = "";           // строка
let x3 = 0;            // число
let x4 = false;        // логическое значение
```

<a name='point_8'></a>
### Не использовать `eval()` без необходимости

Функция `eval` предоставляет доступ к компилятору _**JavaScript**_. Т.е. мы можем выполнить команду записанную в строковой переменной, которую передадим в качестве параметра в `eval`.
Это не только замедлит вашу программу, но еще и предполагает возниковение огромной дыры безопасности вашего приложения. Поэтому по возможности следует избегать использование.

```js
//bad
eval( "Console.log('Привет!');" );
```

<a name='point_9'></a>
### Не передавать строку в `SetInterval` или `SetTimeOut`

Передавать строку в `SetInterval` или `SetTimeOut` не рекомендуется по следующим причинам:
+ Такой способ затрудняет чтение (а также сопровождение и/или отладку).
+ Внутри используется eval(), что является небезопасным.
+ Этот способ медленнее остальных, т.к. вызывает интерпретатор JS.
Вместо этого лучше передавать функцию в качестве аргумента.

```js
//bad
setInterval(  
"document.getElementById('container').innerHTML += 'My new number: ' + i", 3000
);

//good
setInterval(someFunction, 3000);
```

<a name='point_10'></a>
### Проведение проверки атрибутов при использовании `for in`

При итерировании по объектам через `for in` используется конструкция `hasOwnProperty`, чтобы отфильтровать свойства, которые не являются объектами:

```js
  let prop, obj = { name: "Вася", job: "Тракторист", age: 55 };
  
for (let prop in obj) {
  console.log(prop + ": " + obj[prop]);
}
```
Если запустить этот код, будет следующий вывод:

```js
name: Вася
job: Тракторист
age: 55
```
Однако, браузер будет включать в себя свойства и методы дальше по цепочке. При использовании `hasOwnProperties` происходит фильтрация ненужной информации:

```js
function Dog (name) {
  this.name = name;
}

Dog.prototype.legs = 4;
Dog.prototype.speak = function() {
  return "Гав!";
};

let d = new Dog("Тузик");

for (let prop in d) {
  console.log( prop + ": " + d[prop] );
}

console.log("=====");

for (let prop in d) {
  if (d.hasOwnProperty(prop)) {
    console.log( prop + ": " + d[prop] );
  }
}

// Output  
  
// name: Тузик  
// legs: 4  
// speak: function () {  
//        return "Гав!";  
// }  
// =====  
// name: Тузик  
```