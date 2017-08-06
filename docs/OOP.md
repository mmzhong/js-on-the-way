# JavaScript OOP

## OOP 简介

**面向对象编程**（ Object Oriented Programming, OOP ）是一种主流的编程范式。所谓编程范式，可以理解为针对编程的方法论，即以一种特定的思维方式来写代码。最常见的编程范式是**过程式编程**（ Procedural Programming），或者叫命令式编程。此外，还有一种比较学院派的编程范式，叫**函数式编程**（ Functional Programming ）。当然，在现实中，大部分开发者其实都是**面向工资编程** :upside_down_face:

OOP 的关键在于如何处理对象，包括对对象的抽象、封装和使用。对象是对单个实物的抽象，它是现实世界到电子世界的一种映射，比如现实世界中一张桌子，在电子世界就映射为一个桌子对象，该对象拥有的特征和功能都是参照实际桌子而得来的。这个映射过程就是抽象和封装的过程，对象的特征和功能在编程中表现为属性和方法。

## 如何生成对象

JS 的 OOP 与典型的 OOP 语言（如C++、Java）不太一样。典型的 OOP 语言是基于**类**（ `class` ）的，类就是对象的生成模板，生成的对象称为实例；JS的 OOP 则是基于构造函数（ Constructor ）和原型链（ Prototype ），它使用构造函数作为对象的生成模板。

## 构造函数

构造函数就是普通的函数，但是有一些独特之处。

```javascript
// Constructor
function Desk() {
  this.shape = 'square';
}
```
独特之处：

- 构造函数的命名通常是以大写字母开头的驼峰式命名；
- 函数体内使用 `this` 关键字，代表要生成的实例；
- 必须使用 `new` 命令才能生成实例；

## new 命令

### 基本用法

`new` 命令的作用就是执行构造函数并返回实例。

```javascript
function Desk() {
  this.shape = 'square';
}
var desk = new Desk();
desk.shape // 'square'
```

使用 `new` 执行构造函数时，构造函数可以带括号也可以不带括号，效果是一样的。但如果构造函数需要接受入参，那就必须带括号，否则入参为 `undefined`。推荐做法是一直带括号。

构造函数既然是普通函数，那么肯定也可以直接当做普通函数执行，此时，它并不会生成实例，且 `this` 所指的对象并不会是生成实例。

```javascript
function Desk() {
  this.shape = 'square';
}

var desk = Desk(); // desk is undefined
desk.shape // Uncaught TypeError: Cannot read property 'shape' of undefined

shape // 'square', this => window
```

如何避免构造函数被直接调用？

- 在构造函数内第一行加上 `'use strict'` ,使得构造函数进入严格模式，以确保 `this` 默认为 `undefined` ，这样，不适用 `new` 的话就是报错
- 判断 `this` 是否为当前实例，否则主动 `new` 
- 使用 `new.target` ，如果使用 `new` ，`new.target` 为当前函数，否则为 `undefinded`

```javascript
function Desk() {
  'use strict';
  this.shape = 'square';
}

var desk = Desk(); // Uncaught TypeError: Cannot read property 'shape' of undefined
```

```javascript
function Desk() {
  if (!(this instanceof Desk)) {
    return new Desk();
  }

  this.shape = 'square';
}
var desk = Desk(); // Desk {shape: "square"}
```

```javascript
function Desk() {
  if (!new.target) {
    throw new Error('请使用 new 命令');
  }
  this.shape = 'square';
}
```

**注意**：`new.target` 只能在函数内使用。

### new 原理

使用 `new` 时，构造函数执行过程如下：

1. 创建一个空对象，假设为 A;
2. 把 A 的原型指向构造函数的原型，即 A.prototype = Constructor.prototype;
3. 把 A 赋值给构造函数内的 `this`, 即 this = A;
4. 开始执行构造函数内部代码。

如果构造函数内有返回值，且该返回值为**对象** （ Object，不能是 String、Number 等），则 `new` 返回该对象，否则忽略该 `return`， 返回 `this` 。

伪代码：

```javascript
function _new(constructor, param) {
  var args = [].slice.call(arguments);
  var constructor = args.shift();
  // 创建空对象，并且继承构造函数的 prototype
  var context = Object.create(constructor.prototype);
  // 执行构造函数
  var instance = constructor.apply(context, args);
  return (typeof instance === 'object' && instance != null) ? instance : context;
}

var desk = _new(Desk)
```

`Object.create()` 可以直接以一个实例作为模板，生成另一个新的实例，两者具有相同的属性和方法。

## this 关键字

### 摇摆不定的 `this`

与典型的 OOP 语言不同，JS 里的 `this` 指向的对象并不是固定的，它是动态的、可变的。

**this** 总是指向函数执行时所在的环境。

### 使用场景

1. 全局环境
2. 构造函数
3. 对象方法

### 注意点

### 固定 `this`

* `Function.prototype.call`
* `Function.prototype.apply`
* `Function.prototype.bind`
