# Table of contents

- [About JavaScript](#about-javascript)
- [ECMAScript](#ecmascript)
- [Event loop](#event-loop)
- [Comments](#comments)
  * [Single line](#single-line)
  * [Multi line](#multi-line)
  * [Inline](#inline)
- [Data Types](#data-types)
  * [Number](#number)
    + [Integer and floating point](#integer-and-floating-point)
    + [Basic operations](#basic-operations)
    + [Number methods](#number-methods)
  * [String](#string)
    + [Quotes](#quotes)
    + [Concatenation](#concatenation)
    + [String size](#string-size)
    + [String methods](#string-methods)
  * [Boolean](#boolean)
  * [Undefined](#undefined)
  * [Null](#null)
  * [Symbol](#symbol)
- [Weak typing](#weak-typing)
- [Variables](#variables)
  * [Variables and constants](#variables-and-constants)
  * [Creating, assigning and updating](#creating--assigning-and-updating)
  * [String templates](#string-templates)
  * [use strict](#use-strict)
- [Math](#math)
- [Pre-defined functions](#pre-defined-functions)
- [Comparisons](#comparisons)
- [Truthy and falsy values](#truthy-and-falsy-values)
  * [Short-circuit evaluation](#short-circuit-evaluation)
- [Ternary operator](#ternary-operator)
- [Logical operations](#logical-operations)
- [Conditionals](#conditionals)
  * [If else](#if-else)
  * [Switch case](#switch-case)
- [Functions](#functions)
  * [Functions without parameters](#functions-without-parameters)
  * [Functions with parameters](#functions-with-parameters)
  * [Default parameters](#default-parameters)
  * [Arrow functions](#arrow-functions)
- [Scope](#scope)
- [Arrays](#arrays)
  * [Array methods](#array-methods)
- [Loops](#loops)
  * [For](#for)
  * [While](#while)
  * [Do while](#do-while)
  * [Break](#break)
  * [Continue](#continue)
- [Higher-order functions](#higher-order-functions)
  * [Function reference](#function-reference)
  * [Callback as argument](#callback-as-argument)
  * [Function as return value](#function-as-return-value)
  * [Receiving and returning functions](#receiving-and-returning-functions)
- [Objects](#objects)
  * [Key-value pairs](#key-value-pairs)
  * [Updating objects](#updating-objects)
  * [Object methods](#object-methods)
  * [this keyword](#this-keyword)
  * [this keyword with callbacks](#this-keyword-with-callbacks)
  * [Getters and setters](#getters-and-setters)
  * [Factory functions](#factory-functions)
  * [Destructured assignment](#destructured-assignment)
  * [Built-in methods](#built-in-methods)
- [Classes](#classes)
- [Dates](#dates)
- [Regular expressions](#regular-expressions)
  * [Regex flags](#regex-flags)
  * [String methods that work with regexes](#string-methods-that-work-with-regexes)
- [JSON](#json)
- [Iterators](#iterators)
  * [Array iterators](#array-iterators)
  * [Symbol.iterator](#symboliterator)
  * [Custom iterators](#custom-iterators)
- [Generators](#generators)
- [Error handling](#error-handling)
  * [Try...catch](#trycatch)
  * [Error types](#error-types)
- [Promises](#promises)
  * [Asynchronous programming](#asynchronous-programming)
  * [Future result](#future-result)
  * [Resolve or reject](#resolve-or-reject)
  * [Concurrent execution](#concurrent-execution)
- [Async...await](#asyncawait)
- [AJAX](#ajax)
  * [XHR requests](#xhr-requests)
  * [Fetch API](#fetch-api)
- [JavaScript projects with npm](#javascript-projects-with-npm)
  * [Project creation](#project-creation)
  * [Module installation](#module-installation)
- [Modules](#modules)
  * [NodeJS](#nodejs)
  * [ES6](#es6)
    + [Default export](#default-export)
    + [Named exports](#named-exports)
- [Document Object Model (DOM)](#document-object-model--dom-)
  * [Example events](#example-events)
- [Dialogs](#dialogs)
  * [Alert dialog](#alert-dialog)
  * [Confiramtion dialog](#confiramtion-dialog)
  * [Prompt dialog](#prompt-dialog)
- [References](#references)
  * [Main reference](#main-reference)
  * [Secondary references](#secondary-references)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>



# About JavaScript

JavaScript is a lightweight, interpreted (or JIT compiled) language, most well-known as the scripting languages for webpages.

Browsers have an embedded console that interpret and run JavaScript commands:

```js
console.log('Hello, world!');

// >>> Hello, world!
```

It can also be used in non-browser environments, for example in backend development with [NodeJS](https://nodejs.org/en/).

# ECMAScript

JavaScript conforms to the stardards proposed by the *ECMAScript Language Specification*, more specifically the [ECMA-262](https://www.ecma-international.org/publications/standards/Ecma-262.htm) standard.

This standard is always evolving and adding new features to the language.

Symbols, for example, were introduced by ECMAScript 6 (or ES6). See the [Symbol](#symbol) section for more details.

However, the newest features may not run on all web browsers yet. The [Can I use](https://caniuse.com) website is a good reference of which features are already implemented by each browser.

# Event loop

JavaScript has an event loop responsible for the code execution.

A visual representation of the memory:

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                ┌────────┐     │
│               │     ┌────────┐ │ Object │     │
│  ┌─────────┐  │     │ Object │ └────────┘     │
│  │  Frame  │  │     └────────┘                │
│  └─────────┘  │   ┌────────┐                  │
│  ┌─────────┐  │   │ Object │     ┌────────┐   │
│  │  Frame  │  │   └────────┘     │ Object │   │
│  └─────────┘  │                  └────────┘   │
├───────────────┴───────────────────────────────┤
│  ┌─────────┐  ┌─────────┐  ┌─────────┐        │
│  │ Message │  │ Message │  │ Message │  QUEUE │
│  └─────────┘  └─────────┘  └─────────┘        │
└───────────────────────────────────────────────┘
```

<br>

Frames corresponding to function calls (see the [Functions](#functions) section) are pushed into the *stack*. An example execution:

```js
function double(x) {
    return x * 2;
}

function square(x) {
    return x * x;
}

let myNumber = {
    value: 2
}

console.log(
    square(
        double(myNumber.value)
    )
);
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │     ┌──────────┐              │
│               │     │ value: 2 │              │
│               │     └──────────┘              │
│               │                               │
│               │                               │
│               │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │     ┌──────────┐              │
│               │     │ value: 2 │              │
│               │     └──────────┘              │
│ ┌───────────┐ │                               │
│ │ square(?) │ │                               │
│ └───────────┘ │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│ ┌───────────┐ │     ┌──────────┐              │
│ │ square(?) │ │     │ value: 2 │              │
│ └───────────┘ │     └──────────┘              │
│ ┌───────────┐ │                               │
│ │ double(2) │ │                               │
│ └───────────┘ │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │     ┌──────────┐              │
│               │     │ value: 2 │              │
│               │     └──────────┘              │
│ ┌───────────┐ │                               │
│ │ square(4) │ │                               │
│ └───────────┘ │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │     ┌──────────┐              │
│               │     │ value: 2 │              │
│               │     └──────────┘              │
│               │                               │
│               │                               │
│               │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```js
// >>> 16
```

<br>

JavaScript runtimes use a message *queue* with a list of messages to be processed.

Each message has a corresponding function to handle it.

The event loop monitors the event queue and, when it removes the oldest message from it, the corresponding function is called.

The function is called with the message as an input parameter, resulting in a new stack frame.

After the message is processed completely (the stack is empty once again), the event loop moves to the processing of the next message (if there is one).

<br>

An example execution:

```js
function double(x) {
    return x * 2;
}

setTimeout( () => console.log(double(3)) , 0 );
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
├───────────────┴───────────────────────────────┤
│  ┌─────────┐                                  │
│  │ Message │                            QUEUE │
│  └─────────┘                                  │
└───────────────────────────────────────────────┘
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│ ┌───────────┐ │                               │
│ │ double(3) │ │                               │
│ └───────────┘ │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```
┌───────────────┬───────────────────────────────┐
│     STACK     │                          HEAP │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
│               │                               │
├───────────────┴───────────────────────────────┤
│                                               │
│                                         QUEUE │
│                                               │
└───────────────────────────────────────────────┘
```

```js
// >>> 6
```

<br>

A message is added to the queue every time an event (e.g. a mouse click) occurs in the browser and a corresponding listener is available.

`setTimeout(function, delay)` is function that also adds a message to the queue. Its first argument corresponds to the message to be added to the queue, and the second one is the (minimum) delay after which the message is actually pushed into the queue.

If the stack is empty, the message is processed right after the delay. Otherwise, it will have to wait:

```js
const start = new Date().getSeconds();

setTimeout(
  () => console.log(`Executed after ${(new Date().getSeconds() - start)}s`),
  2 * 1000
)

// >>> Executed after 2s
```

```js
const start = new Date().getSeconds();

setTimeout(
  () => console.log(`Executed after ${(new Date().getSeconds() - start)}s`),
  2 * 1000
)

while (new Date().getSeconds() - start < 5) { /* Loop */ }

// >>> Executed after 5s
```

# Comments

## Single line

```js
// Single-line comments are C-like
```

## Multi line

```js
/*
 * Multi-line comments
 * are also
 * C-like
 */
```

## Inline

```js
console.log('Hello, world!');  // Logging 'Hello, world!'
```

# Data Types

```js
console.log(typeof 123);
console.log(typeof 45.6);
console.log(typeof Infinity);
console.log(typeof NaN);

// >>> number
// >>> number
// >>> number
// >>> number


console.log(typeof 'text');
console.log(typeof 'a');
console.log(typeof "a");

// >>> string
// >>> string
// >>> string


console.log(typeof true);
console.log(typeof false);

// >>> boolean
// >>> boolean


console.log(typeof {name: 'John', age: 40});

// >>> object


console.log(typeof Symbol('abc'));

// >>> symbol


let value = 'Some text';
let empty;

console.log(typeof value);
console.log(typeof empty);

// >>> string
// >>> undefined
```

## Number

### Integer and floating point

```js
console.log(123.0);
console.log(1.0);
console.log(12.340);

// >>> 123
// >>> 1
// >>> 12.34
```

### Basic operations

```js
console.log(2 + 2);
console.log(5 - 3);
console.log(4 * 3);
console.log(6 / 2);

// >>> 4
// >>> 2
// >>> 12
// >>> 3


console.log(1.2 + 3.4);
console.log(2 - 1.5);
console.log(2 * 3.5);
console.log(10 / 3);

// >>> 4.6
// >>> 0.5
// >>> 7
// >>> 3.3333333333333335



console.log(5 / 2);
console.log(5 % 2);

// >>> 2.5
// >>> 1
```

###  Number methods

```js
Number.isInteger(10 / 2);
Number.isInteger(10 / 3);

// >>> true
// >>> false


Number.parseInt('123');

// >>> 123


Number.parseInt('1001');
Number.parseInt('1001', 10);
Number.parseInt('1001', 2);

// >>> 1001
// >>> 1001
// >>> 9


Number.parseInt('F', 16);

// >>> 15


Number.parseFloat('12.5');

// >>> 12.5
```

## String

### Quotes

```js
console.log("Double-quoted string");
console.log('Single-quoted string');

// >>> Double-quoted string
// >>> Single-quoted string
```

### Concatenation

```js
console.log('String' + ' ' + 'concatenation');

// >>> 'String concatenation'
```

### String size

```js
console.log('String'.length);

// >>> 6
```

### String methods

```js
console.log('String'.startsWith('S'));
console.log('String'.endsWith('z'));

// >>> true
// >>> false


console.log('String'.toUpperCase());
console.log('STRING'.toLowerCase());
console.log(' Abc  '.trim());

// >>> 'STRING'
// >>> 'string'
// >>> 'Abc'


console.log('Some text'.substring(1, 7))

// >>> ome te
```

## Boolean

```js
console.log(2 < 1);
console.log(10 > 9);

// >>> false
// >>> true
```

## Undefined

`undefined` typically means a variable that was declared but not defined:

```js
let a = 1;
let b;

let object = {
  name: 'object'
}


console.log(a);
console.log(b);

// >>> 1
// >>> undefined


console.log(object.name);
console.log(object.description);

// >>> My Object
// >>> undefined
```

## Null

`null` means an *empty* or *non-existent* value. It must be *assigned*:

```js
let value1 = null;
let value2;

console.log(value1);
console.log(value2);

// >>> null
// >>> undefined
```

## Symbol

Symbols are *unique and immutable identifiers* returned by the function `Symbol()`. They can be used as keys for object properties (see the [Objects](#objects) section) and were introduced by (*[ES6](https://www.ecma-international.org/ecma-262/6.0/index.html)*).

```js
let symbol1 = Symbol();
let symbol2 = Symbol('optional description');
let symbol3 = Symbol('optional description');


console.log(typeof symbol1);

// >>> symbol


console.log(symbol1);
console.log(symbol2);

// >>> Symbol()
// >>> Symbol(optional description)


console.log(symbol2.description);
console.log(symbol2.toString());

// >>> optional description
// >>> Symbol(optional description)


console.log(symbol2.description === symbol3.description);
console.log(symbol2 === symbol3);
console.log(Symbol() === Symbol());

// >>> true
// >>> false
// >>> false
```

# Weak typing

```js
let variable = 'Some text';


console.log(variable);
console.log(typeof variable);

// >>> Some text
// >>> string


variable = 123;


console.log(variable);
console.log(typeof variable);

// >>> 123
// >>> number
```

# Variables

## Variables and constants

```js
let   variable = 3;
const final    = 2;


console.log(variable);
console.log(final);

// >>> 3
// >>> 2


variable = 4;

console.log(variable);

// >>> 4


final = 10;

// >>> TypeError: Assignment to constant variable.
```

## Creating, assigning and updating

```js
const id     = 1234;
let   number = 12.5;
let   name   = 'John';
let   option = false;

let noValue;


console.log(id);
console.log(number);
console.log(name);
console.log(option);
console.log(noValue);

// >>> 1234
// >>> 12.5
// >>> John
// >>> false
// >>> undefined


option  = true;
noValue = 'SomeValue';

console.log(option);
console.log(noValue);

// >>> true
// >>> 'SomeValue'


number += 0.5;
name   += ' Doe';

console.log(number);
console.log(name);

// >>> 13
// >>> John Doe


number++;

console.log(number);

// >>> 14


console.log(++number);
console.log(number--);
console.log(number);

// >>> 15
// >>> 15
// >>> 14
```

## String templates

```js
let name    = 'John';
let surname = 'Doe';


console.log('My name is ' + name + ' ' + surname);

// >>> My name is John Doe


console.log(`My name is ${name} ${surname}`);

// >>> My name is John Doe
```

## use strict

```js
x = 1;


console.log(x);

// >>> 1
```

To write a more secure code:

```js
"use strict";

x = 1;

// >>> ReferenceError: x is not defined
```

# Math

```js
console.log(Math.random());
console.log(Math.random());
console.log(Math.random());

// >>> 0.7742751547056188
// >>> 0.9094660429847081
// >>> 0.1693942573499141


console.log(Math.floor(3.5));
console.log(Math.ceil(3.5));

// >>> 3
// >>> 4


console.log(Math.min(1, 2, 3));
console.log(Math.max(1, 2, 3));

// >>> 1
// >>> 3


console.log(Math.round(3.9));
console.log(Math.trunc(3.9));

// >>> 4
// >>> 3


console.log(Math.sin(90 * (Math.PI / 180)));
console.log(Math.cos(0));

// >>> 1
// >>> 1


console.log(Math.pow(2, 10));

// >>> 1024
```

# Pre-defined functions

```js
console.log(eval('2 + 2'));

// >>> 4


console.log(parseInt('1', 10));
console.log(parseInt('1001', 2));

// >>> 1
// >>> 9


console.log(1.0 + parseFloat('1.5'));

// >>> 2.5


console.log(       (123).toString());
console.log(typeof (123).toString());

// >>> 123
// >>> string
```

# Comparisons

```js
console.log(4 < 5);
console.log(3 > 2);
console.log(3 >= 3);
console.log(3 == 3);
console.log(4 != 5);

// >>> true
// >>> true
// >>> true
// >>> true
// >>> true


console.log(3 == '3');
console.log(3 === '3');
console.log(3 === 3);
console.log('3' === '3');

// >>> true
// >>> false
// >>> true
// >>> true


console.log(4 != '5');
console.log(4 !== '5');
console.log(4 != '4');
console.log(4 !== '4');

// >>> true
// >>> true
// >>> false
// >>> true


console.log(false == 0);
console.log(true == 1);
console.log(false === 0);
console.log(true === 1);

// >>> true
// >>> true
// >>> false
// >>> false


console.log(false == '0');
console.log(true == '1');
console.log(false === '0');
console.log(true === '1');

// >>> true
// >>> true
// >>> false
// >>> false


console.log('' == false);
console.log('not empty' == false);

// >>> true
// >>> false
```

# Truthy and falsy values

There are 6 falsy values in JavaScript:

 - `false`
 - `0`
 - `''` (empty string)
 - `null`
 - `undefined`
 - `NaN`

```js
if ('')  console.log('Truthy');
else     console.log('Falsy');

// >>> Falsy


if ('Not empty')  console.log('Truthy');
else              console.log('Falsy');

// >>> Truthy


if (0)  console.log('Truthy');
else    console.log('Falsy');

// >>> Falsy


if (1)  console.log('Truthy');
else    console.log('Falsy');

// >>> Truthy


if (null)  console.log('Truthy');
else       console.log('Falsy');

// >>> Falsy


if (undefined)  console.log('Truthy');
else            console.log('Falsy');

// >>> Falsy


if (NaN)  console.log('Truthy');
else      console.log('Falsy');

// >>> Falsy
```

```js
console.log('null and undefined are strictly equal? ' + (null === undefined))
console.log('null and undefined are falsy values?   ' + (null == undefined))

// >>> null and undefined are strictly equal? false
// >>> null and undefined are falsy values?   true


console.log('0 and false have the same types? ' + (0 === false));
console.log('0 and false are falsy values?    ' + (0 == false));

console.log(typeof false);
console.log(typeof 0);

// >>> 0 and false have the same types? false
// >>> 0 and false are falsy values?    true
// >>> boolean
// >>> number
```

## Short-circuit evaluation

```js
let username = '';

console.log('Hello, ' + (username || 'stranger'));

// >>> Hello, stranger


username = 'John'

console.log('Hello, ' + (username || 'stranger'));

// >>> Hello, John
```

# Ternary operator

```js
let systemOn = true;

console.log('The system is turned ' + (systemOn ? 'on' : 'off'))

// >>> The system is turned on


systemOn = false;

console.log('The system is turned ' + (systemOn ? 'on' : 'off'))

// >>> The system is turned off
```

# Logical operations

```js
console.log(true  && true);
console.log(true  && false);
console.log(false && false);

// >>> true
// >>> false
// >>> false


console.log(true  || true);
console.log(true  || false);
console.log(false || false);

// >>> true
// >>> true
// >>> false


console.log( !false);
console.log( !true);
console.log(!!true);

// >>> true
// >>> false
// >>> true
```

# Conditionals

## If else

```js
if (temperature >= 100) {
  console.log('The water is boiling');
}
else if (temperature < 0) {
  console.log('The water is frozen');
}
else {
  console.log('The water is liquid');
}
```

## Switch case

```js
switch (lightColor) {
  case 'green':
    console.log('Go ahead');
    break;

  case 'yellow':
    console.log('Be careful');
    break;

  case 'red':
    console.log('Stop');
    break;

  default:
    console.log('Unknown color');
    break;
}
```

# Functions

## Functions without parameters

```js
function sayHello() {
    console.log('Hello');
}

sayHello();

// >>> Hello
```

## Functions with parameters

```js
function duplicate(number) {
  return number * 2;
}

console.log(duplicate(3));

// >>> 6
```

## Default parameters

```js
function greeting(username = 'stranger') {
  return `Hello, ${username}`;
}

console.log(greeting('John'));

// >>> Hello, John


console.log(greeting());

// >>> Hello, stranger


console.log(greeting(null));

// >>> Hello, null


console.log(greeting(undefined));

// >>> Hello, stranger
```

## Arrow functions

```js
const area = function(width, height) {
  return width * height;
}

console.log(area(2, 3));

// >>> 6
```
```js
const area = (width, height) => {
  return width * height;
}

console.log(area(2, 3));

// >>> 6
```
```js
const area = (width, height) => width * height;

console.log(area(2, 3));

// >>> 6
```
```js
const duplicate = number => number * 2;

console.log(duplicate(5));

// >>> 10
```
```js
const greeting = () => 'Hello, user'

console.log(greeting());

// >>> Hello, user
```
# Scope

```js
let number = 10;

function writeNumber() {
  let number = 5;

  if (true) {
    // This would not be allowed in Java
    let number = 1;

    console.log(number);
  }

  console.log(number);
}

writeNumber();

// >>> 1
// >>> 5


console.log(number);

// >>> 10
```

# Arrays

```js
let colors = ['red', 'green', 'blue'];
let misc   = ['John', 1, false];


console.log(colors);
console.log(colors.length);

// >>> (3) ["red", "green", "blue"]
// >>> 3


console.log(colors[0]);
console.log(colors[1]);
console.log(colors[2]);

// >>> red
// >>> green
// >>> blue


console.log(colors[colors.length - 1]);

// >>> blue


console.log(colors[3]);

// >>> undefined


console.log(colors.indexOf('green'));
console.log(colors.lastIndexOf('green'));
console.log(colors.indexOf('white'));

// >>> 1
// >>> 1
// >>> -1

console.log(misc);

// (3) ["John", 1, false]


console.log(typeof misc[0]);
console.log(typeof misc[1]);
console.log(typeof misc[2]);

// >>> string
// >>> number
// >>> boolean
```

```js
let colors = ['red', 'green', 'blue'];


console.log(colors);

// >>> (3) ["red", "green", "blue"]


colors[3] = 'white';

console.log(colors);

// >>> (4) ["red", "green", "blue", "white"]


colors[3] = 'yellow';

console.log(colors);

// >>> (4) ["red", "green", "blue", "yellow"]
```

```js
let matrix       = [[2, 3], [4, 5]];
let namesAndAges = [['John', 30], ['Anna', 35]]


console.log(matrix[0]);
console.log(matrix[1]);

// >>> (2) [2, 3]
// >>> (2) [4, 5]


console.log(namesAndAges[0][0]);
console.log(namesAndAges[0][1]);
console.log(namesAndAges[1][0]);
console.log(namesAndAges[1][1]);

// >>> John
// >>> 30
// >>> Anna
// >>> 35
```

## Array methods

```js
let numbers = [1, 2, 3];         // [1, 2, 3]

numbers.push(4, 5, 6);           // [1, 2, 3, 4, 5, 6]

let value = numbers.pop();       // [1, 2, 3, 4, 5]

numbers.push(value);             // [1, 2, 3, 4, 5, 6]

value = numbers.shift();         //    [2, 3, 4, 5, 6]

numbers.unshift(value);          // [1, 2, 3, 4, 5, 6]

numbers.reverse();               // [6, 5, 4, 3, 2, 1]

numbers.sort();                  // [1, 2, 3, 4, 5, 6]

numbers.splice(1, 4, 9, 8);      // [1, 9, 8, 6]

numbers.splice(2);               // [1, 9]

numbers.fill(0);                 // [0, 0]
```
```js
let numbers = [1, 2, 3, 4]                          // [1, 2, 3, 4]

numbers.map(value => 2 * value);                    // [1, 2, 3, 4] --> [2, 4, 6, 8]

numbers.reduce(                                     // [1, 2, 3, 4] --> -8
  (accumulator, current) => accumulator - current
);

numbers.reduceRight(                                // [1, 2, 3, 4] --> -2
  (accumulator, current) => accumulator - current
);

numbers.slice(1, 3);                                // [1, 2, 3, 4] --> [2, 3]

numbers.filter(value => value >= 2 && value <= 4);  // [1, 2, 3, 4] --> [1, 2, 3]

numbers.concat([5, 6]);                             // [1, 2, 3, 4] --> [1, 2, 3, 4, 5, 6]

numbers.some(value => value >= 3);                  // [1, 2, 3, 4] --> true

numbers.some(value => value >= 9);                  // [1, 2, 3, 4] --> false

numbers.every(value => value >= 1);                 // [1, 2, 3, 4] --> true

numbers.every(value => value > 1);                  // [1, 2, 3, 4] --> false
```
```js
let numbers = [1, 2, 3, 4];
let letters = ['A', 'B', 'C', 'D'];


numbers.forEach(value => console.log(value));

// >>> 1
// >>> 2
// >>> 3
// >>> 4


console.log(letters.join(' | '));

// >>> A | B | C | D


let index = numbers.findIndex(value => value >= 3);


console.log(index);
console.log(numbers[index]);

// >>> 2
// >>> 3
```

# Loops

## For

```js
for (let i = 0; i < 3; i++) {
  console.log(`Iteration ${i + 1}`)
}

// >>> Iteration 1
// >>> Iteration 2
// >>> Iteration 3
```

```js
let numbers = [1, 2, 3, 4];


for (let index in numbers)
  console.log(index + ` (${typeof index})`);

// >>> 0 (string)
// >>> 1 (string)
// >>> 2 (string)
// >>> 3 (string)


for (let value of numbers)
  console.log(value + ` (${typeof value})`);

// >>> 1 (number)
// >>> 2 (number)
// >>> 3 (number)
// >>> 4 (number)


for (let key of numbers.keys())
  console.log(key + ` (${typeof key})`);

// >>> 0 (number)
// >>> 1 (number)
// >>> 2 (number)
// >>> 3 (number)
```

```js
let string  = 'abcd';


for (let index in string)
  console.log(index + ` (${typeof index})`);

// >>> 0 (string)
// >>> 1 (string)
// >>> 2 (string)
// >>> 3 (string)


for (let char of string)  // Value iterator
  console.log(char + ` (${typeof char})`);

// >>> a (string)
// >>> b (string)
// >>> c (string)
// >>> d (string)


for (let key of string.keys())
  console.log(key + ` (${typeof key})`);

// >>> TypeError: string.keys is not a function or its return value is not iterable
```

See more about iterators in the [Iterators](#iterators) section.

## While

```js
let count = 0;

while (count < 3) {
  console.log(`Iteration ${++count}`)
}

// >>> Iteration 1
// >>> Iteration 2
// >>> Iteration 3
```

## Do while

```js
let count = 0;

do {
  console.log(`Iteration ${++count}`)
}
while (count < 3)

// >>> Iteration 1
// >>> Iteration 2
// >>> Iteration 3
```

## Break

```js
let count = 0;

while (true) {
  console.log(`Iteration ${++count}`)

  if (count >= 3)
    break;
}

// >>> Iteration 1
// >>> Iteration 2
// >>> Iteration 3
```

## Continue

```js
for (let i = 1; i <= 8; i++) {
  if (i % 2 === 0)
    continue;
    
  console.log(`Iteration ${i + 1}`)
}

// >>> Iteration 2
// >>> Iteration 4
// >>> Iteration 6
// >>> Iteration 8
```

# Higher-order functions

## Function reference

```js
const writeGreeting = function(username = 'John') {
  console.log(`Hello, ${username}`);
}

const greet = writeGreeting;


greet();
greet('Anna');

// >>> Hello, John
// >>> Hello, Anna


console.log(greet.name);

// >>> writeGreeting
```

## Callback as argument

```js
const log = (level, message) => {
  console.log(`[${level}] ${message}`);
}

const generateRandomNumbers = (numberOfIterations, loggingCallback) => {
  for (let i = 0; i < numberOfIterations; i++) {
    let randomNumber = Math.random();
    let message      = 'Generated number: ' + randomNumber;

    loggingCallback('DEBUG', message);
  }
}


generateRandomNumbers(3, log);

// >>> [DEBUG] Generated number: 0.4576630038607681
// >>> [DEBUG] Generated number: 0.6170319962766659
// >>> [DEBUG] Generated number: 0.3937085327175489


generateRandomNumbers(3, (ignored, message) => console.log(message));

// >>> Generated number: 0.6406502767264568
// >>> Generated number: 0.30985723002976195
// >>> Generated number: 0.8592765557889122
```

## Function as return value

```js
const generateLogger = function(loggerLevel) {
  return (message) => console.log(`[${loggerLevel}] ` + message);
}

let infoLogger = generateLogger('INFO');


infoLogger('Some text');

// >>> [INFO] Some text


generateLogger('DEBUG')('Other message');

// >>> [DEBUG] Other message
```

## Receiving and returning functions

```js
const repeater = function(callback, numberOfCalls) {
  return function() {
    for (let i = 0; i < numberOfCalls; i++)
      callback();
  }
}

let callbackRepeater = repeater(() => console.log('Executed'), 3);


callbackRepeater();

// >>> Executed
// >>> Executed
// >>> Executed


const arrowRepeater = (callback, numberOfCalls) => () => {
  for (let i = 0; i < numberOfCalls; i++)
    callback();
}

arrowRepeater(() => console.log('Executed'), 3);

// >>> Executed
// >>> Executed
// >>> Executed
```

# Objects

## Key-value pairs

```js
let car = {
  make            : 'Volkswagen',
  model           : 'Polo',
  'engine'        : 1.6,
  turbo           : false,
  'Main features' : ['Traction Control', 'Airbags', 'Alloy wheels']
}


console.log(`Car           : ${car.make} ${car['model']}`);
console.log('Turbo         : ' + (car['turbo'] ? 'Yes' : 'No'));
console.log('Small engine  : ' + (car.engine > 1.0 ? 'No' : 'Yes'));
console.log('First feature : ' + car['Main features'][0]);

// >>> Car           : Volkswagen Polo
// >>> Turbo         : No
// >>> Small engine  : No
// >>> First feature : Traction Control


let modelKey = 'model';


console.log(car.model);
console.log(car['model']);
console.log(car[modelKey]);

// >>> Polo
// >>> Polo
// >>> Polo
```

```js
let model = Symbol();

let car = {
  make: 'BMW',
  [model]: 'M2'
}


console.log(car);

// >>> {
// >>>   make: "BMW",
// >>>   Symbol(): "M2"
// >>> }


console.log('make' in car);
console.log(model in car);

// >>> true
// >>> true


console.log(car['make']);
console.log(car[model]);

// >>> BMW
// >>> M2


console.log(car.make);
console.log(car.model);

// >>> BMW
// >>> undefined


for (let key in car) {
  console.log(key);
}

// >>> make
```

```js
let garage = {
  car: {
    'make': 'Volkswagen',
    'model': 'Polo'
  },
  sportsCar: {
    'make': 'BMW',
    'model': 'M2'
  },
  superCar: {
    'make': 'Ferrari',
    'model': 'SF90 Stradale'
  },
}


for (let vehicle in garage) {
  console.log(`${vehicle}: ${garage[vehicle]['make']} ${garage[vehicle]['model']}`);
}

// >>> car: Volkswagen Polo
// >>> sportsCar: BMW M2
// >>> superCar: Ferrari SF90 Stradale
```

## Updating objects

```js
let sportsCar = {
  make: 'Mercedes-Benz'
};


console.log(sportsCar);

// >>> {
// >>>   make: "Mercedes-Benz"
// >>> }


sportsCar.make = 'BMW';


console.log(sportsCar);

// >>> {
// >>>   make: "BMW"
// >>> }


sportsCar.model = 'M2';
sportsCar['cylinders'] = 6;


console.log(sportsCar);

// >>> {
// >>>   make: "BMW",
// >>>   model: "M2",
// >>>   cylinders: 6
// >>> }


delete sportsCar.cylinders;


console.log(sportsCar);

// >>> {
// >>>   make: "BMW",
// >>>   model: "M2"
// >>> }
```

```js
const car = {
  'make'  : 'Mercedes-Benz',
  'model' : 'A class'
}

car = {
  'make'  : 'BMW',
  'model' : '1 series'
}

// >>> error: Uncaught TypeError: Assignment to constant variable.
```

```js
let car = {
  'make'  : 'Mercedes-Benz',
  'model' : 'A class'
}


console.log(car);

// >>> {
// >>>   make: "Mercedes-Benz",
// >>>   model: "A class"
// >>> }


car = {
  'make'  : 'BMW',
  'model' : '1 series'
}


console.log(car);

// >>> {
// >>>   make: "BMW",
// >>>   model: "1 series"
// >>> }
```

## Object methods

```js
let voiceAssistant = {
  activate : function() { console.log('Hello, what can I do for you?'); },
  executeCommand : (command) => console.log(`I don't know the command "${command}" yet`)
}


voiceAssistant.activate();
voiceAssistant.executeCommand("How is the weather today?");

// >>> Hello, what can I do for you?
// >>> I don't know the command "How is the weather today?" yet
```

```js
let superCar = {
  'make'   : 'Ferrari',
  'model'  : 'SF90 Stradale',
  'engine' : {
    'liters'     : 4.0,
    'cylinders'  : 8,
    'horsepower' : 1000,
    'hybrid'     : true,
    'start'      : () => console.log('Ready!')
  },
  'tires'  : [
    {
      'diameter' : 20,
      'width'    : 255
    },
    {
      'diameter' : 20,
      'width'    : 255
    },
    {
      'diameter' : 20,
      'width'    : 315
    },
    {
      'diameter' : 20,
      'width'    : 315
    }
  ]
};


console.log([superCar['make'], superCar.model].join(' '));
console.log(superCar['engine']['horsepower'] + ' HP')

// >>> Ferrari SF90 Stradale
// >>> 1000 HP


superCar.tires.filter(
                 tire => tire.width > 300
              )
              .forEach(
                tire => console.log(
                  tire['width'] + '/30 ZR' + tire.diameter
                )
              )

// >>> 315/30 ZR20
// >>> 315/30 ZR20


superCar.engine.start();

// >>> Ready!
```

```js
const car = {
  'engine' : {
    'liters'            : 2.0,
    'oilLifePercentage' : 5
  },
  'tires'  : [
    {
      'pressure' : 25
    },
    {
      'pressure' : 24
    },
    {
      'pressure' : 25
    },
    {
      'pressure' : 26
    }
  ]
}

const inflateTires = (tires) => tires.forEach(tire => tire['pressure'] = 30);

const changeOil = (car) => car['engine']['oilLifePercentage'] = 100;


console.log(car);

// >>> {
// >>>   engine: {
// >>>     liters: 5,
// >>>     oilLifePercentage: 12
// >>>   },
// >>>   tires:(4) [
// >>>     {
// >>>       pressure: 25
// >>>     },
// >>>     {
// >>>       pressure: 24
// >>>     },
// >>>     {
// >>>       pressure: 25
// >>>     },
// >>>     {
// >>>       pressure: 26
// >>>     }
// >>>   ]
// >>> }


inflateTires(car['tires']);
changeOil(car);


console.log(car);

// >>> {
// >>>   engine: {
// >>>     liters: 5,
// >>>     oilLifePercentage: 100
// >>>   },
// >>>   tires:(4) [
// >>>     {
// >>>       pressure: 30
// >>>     },
// >>>     {
// >>>       pressure: 30
// >>>     },
// >>>     {
// >>>       pressure: 30
// >>>     },
// >>>     {
// >>>       pressure: 30
// >>>     }
// >>>   ]
// >>> }
```

Functions can change received objects' attributes:

```js
let car = {
  'make'  : 'BMW',
  'model' : '1 series'
}

const changeCarModel = (carToChange) => {
  carToChange['model'] = 'X7';

  console.log('Car inside changeCarModel function:');
  console.log(carToChange);
};


console.log('Car before calling changeCarModel function:');
console.log(car);

// >>> Car before calling changeCarModel function:
// >>> {
// >>>   make: "BMW",
// >>>   model: "1 series"
// >>> }


changeCarModel(car);

// >>> Car inside changeCarModel function:
// >>> {
// >>>   make: "BMW",
// >>>   model: "X7"
// >>> }


console.log('Car after changeCarModel function:');
console.log(car);

// >>> Car after changeCarModel function:
// >>> {
// >>>   make: "BMW",
// >>>   model: "X7"
// >>> }
// >>>
```

But functions cannot reassign received objects:
```js
let car = {
  'make'  : 'BMW',
  'model' : '1 series'
}

const changeCar = (carToChange) => {
  carToChange = {
    'make'  : 'Volkswagen',
    'model' : 'Golf'
  }

  console.log('Car inside changeCar function:');
  console.log(carToChange);
};


console.log('Car before calling changeCar function:');
console.log(car);

// >>> Car before calling changeCar function:
// >>> {
// >>>   make: "BMW",
// >>>   model: "1 series"
// >>> }


changeCar(car);

// >>> Car inside changeCar function:
// >>> {
// >>>   make: "Volkswagen",
// >>>   model: "Golf"
// >>> }


console.log('Car after changeCar function:');
console.log(car);

// >>> Car after changeCar function:
// >>> {
// >>>   make: "BMW",
// >>>   model: "1 series"
// >>> }
```

That's because a function receives an object reference *by value*.

This reference points to the object in memory, so it can be used to access and modify the object's attributes:

```js
//  Before function
//
//             ┌─────┐        ┌────────────────────┐
//             │ car │  ───>  │ Make  : 'BMW'      │
//             └─────┘        │ Model : '1 series' │
//                            └────────────────────┘

const changeCarModel = (carToChange) => {
  //  Inside function
  //
  //           ┌─────┐        ┌────────────────────┐
  //           │ car │  ───>  │ Make  : 'BMW'      │
  //           └─────┘   ┌─>  │ Model : '1 series' │
  //                     │    └────────────────────┘
  //   ┌─────────────┐   │
  //   │ carToChange │  ─┘
  //   └─────────────┘
  
  carToChange['model'] = 'X7';

  //  Inside function after attribute change
  //
  //           ┌─────┐        ┌────────────────────┐
  //           │ car │  ───>  │ Make  : 'BMW'      │
  //           └─────┘   ┌─>  │ Model : 'X7'       │
  //                     │    └────────────────────┘
  //   ┌─────────────┐   │
  //   │ carToChange │  ─┘
  //   └─────────────┘

  console.log('Car inside changeCarModel function:');
  console.log(carToChange);
};

//  After function
//
//             ┌─────┐        ┌────────────────────┐
//             │ car │  ───>  │ Make  : 'BMW'      │
//             └─────┘        │ Model : 'X7'       │
//                            └────────────────────┘
```

However, as the reference is received *by value*, this reference is only valid within the function block (see the [Scope](#scope) section).

If we reaasign this reference, it will then point to something else other than the object:


```js
//  Before function
//
//             ┌─────┐        ┌────────────────────┐
//             │ car │  ───>  │ Make  : 'BMW'      │
//             └─────┘        │ Model : '1 series' │
//                            └────────────────────┘

const changeCar = (carToChange) => {
  //  Inside function
  //
  //           ┌─────┐        ┌────────────────────┐
  //           │ car │  ───>  │ Make  : 'BMW'      │
  //           └─────┘   ┌─>  │ Model : '1 series' │
  //                     │    └────────────────────┘
  //   ┌─────────────┐   │
  //   │ carToChange │  ─┘
  //   └─────────────┘

  carToChange = {
    'make'  : 'Volkswagen',
    'model' : 'Golf'
  }

  //  Inside function after reassigning
  //
  //           ┌─────┐        ┌────────────────────┐
  //           │ car │  ───>  │ Make  : 'BMW'      │
  //           └─────┘        │ Model : '1 series' │
  //                          └────────────────────┘
  //   ┌─────────────┐        ┌──────────────────────┐
  //   │ carToChange │  ───>  │ Make  : 'Volkswagen' │
  //   └─────────────┘        │ Model : 'Golf'       │
  //                          └──────────────────────┘

  console.log('Car inside changeCar function:');
  console.log(carToChange);
};

//  After function
//
//             ┌─────┐        ┌────────────────────┐
//             │ car │  ───>  │ Make  : 'BMW'      │
//             └─────┘        │ Model : '1 series' │
//                            └────────────────────┘
```
## this keyword

The following code produces an error:

```js
let person = {
  name   : 'John',
  skills : ['Java', 'C++', 'Python'],

  listSkills : function () {
    console.log('I am ' + name + ' and I know ' + skills.join(', '))
  }
}


person.listSkills();

// >>> Uncaught ReferenceError: skills is not defined
```

Inside the `listSkills` function we need to use the `this` keyword to access the attributes of the `person` object:

```js
let person = {
  name   : 'John',
  skills : ['Java', 'C++', 'Python'],

  listSkills : function () {
    console.log('I am ' + this.name + ' and I know ' + this.skills.join(', '))
  }
}


person.listSkills();

// >>> I am John and I know Java, C++, Python
```

## this keyword with callbacks

The following example does not work:
```js
let person = {
  name   : 'John',
  skills : ['Java', 'C++', 'Python'],

  listSkills : function () {
    this.skills.forEach(
      function (skill) {
        console.log('I am ' + this.name + ' and I know ' + skill)
      }
    )
  }
}


person.listSkills();

// >>> I am  and I know Java
// >>> I am  and I know C++
// >>> I am  and I know Python
```

A possible solution: assign `this` to an *outer variable*:
```js
let person = {
  name   : 'John',
  skills : ['Java', 'C++', 'Python'],

  listSkills : function () {
    let self = this;

    this.skills.forEach(
      function (skill) {
        console.log('I am ' + self.name + ' and I know ' + skill)
      }
    )
  }
}


person.listSkills();

// >>> I am John and I know Java
// >>> I am John and I know C++
// >>> I am John and I know Python
```

A second solution with *binding*:
```js
let person = {
  name   : 'John',
  skills : ['Java', 'C++', 'Python'],

  listSkills : function () {
    this.skills.forEach(
      function (skill) {
        console.log('I am ' + this.name + ' and I know ' + skill)
      }.bind(this)
    )
  }
}


person.listSkills();

// >>> I am John and I know Java
// >>> I am John and I know C++
// >>> I am John and I know Python
```

With a newer syntax (*[ES6](https://www.ecma-international.org/ecma-262/6.0/index.html)*) the binding is not necessary:
```js
let person = {
  name   : 'John',
  skills : ['Java', 'C++', 'Python'],

  listSkills() {
    this.skills.forEach(
       (skill) => console.log('I am ' + this.name + ' and I know ' + skill)
    )
  }
}


person.listSkills();

// >>> I am John and I know Java
// >>> I am John and I know C++
// >>> I am John and I know Python
```

## Getters and setters

```js
let cpu = {
  _clock : 3,

  get clock() {
    return this._clock + ' GHz'
  },

  set clock(newClock) {
    if (typeof newClock === 'number' && newClock > 0)
      this._clock = newClock;
    else
      console.log('The new clock must a number >= 0')
  }
}


console.log(cpu.clock)

// >>> 3 GHz


cpu.clock = 2;


console.log(cpu.clock)

// >>> 2 GHz


cpu.clock = -1;

// >>> The new clock must a number >= 0
```

The leading underscores are used to indicate these properties should not be accessed or changed directly.

They are just a convention, though, as the attributes are not really *private*:

```js
let cpu = {
  _clock : 3,

  get clock() {
    return this._clock + ' GHz'
  }
}


console.log(cpu.clock)
console.log(cpu._clock)

// >>> 3 GHz
// >>> 3
```

## Factory functions

```js
const carFactory = (make, model, color) => {
  return {
    make  : make, 
    model : model, 
    color : color,

    start() {
      console.log('v-v-v-vroom!')
    } 
  }
}

const car = carFactory('BMW', 'M2', 'Blue');


console.log(car.color);
console.log(car.make);
console.log(car.model);

// >>> Blue
// >>> BMW
// >>> M2


car.start()

// >>> v-v-v-vroom!
```

Using property value shorthands, the following code...

```js
const carFactory = (make, model, color) => {
  return {
    make  : make, 
    model : model, 
    color : color
  }
}
```

... can be written like this:

```js
const carFactory = (make, model, color) => {
  return {
    make, 
    model, 
    color
  }
}
```

## Destructured assignment

```js
const car = {
    make   : 'BMW', 
    model  : 'M2', 
    color  : 'blue',
    hybrid : false
}

const make              = car.make;
const model             = car.model;
const { color, hybrid } = car;


console.log(make);
console.log(model);
console.log(color);
console.log(hybrid);

// >>> BMW
// >>> M2
// >>> blue
// >>> false
```

## Built-in methods

```js
const car = {
    make  : 'Volkswagen',
    model : 'Golf'
}


console.log(Object.keys(car));
console.log(Object.values(car));

// >>> (2) ["make", "model"]
// >>> (2) ["Volkswagen", "Golf"]


console.log(Object.entries(car));

// >>> (2) [
// >>>   (2) ["make", "Volkswagen"],
// >>>   (2) ["model", "Golf"]
// >>> ]


console.log(car.valueOf());

// >>> {
// >>>   make: "Volkswagen",
// >>>   model: "Golf"
// >>> }
```

# Classes

```js
class Dog {
  constructor(breed, name, weight) {
    this._breed  = breed;
    this._name   = name;
    this._weight = weight;
  }

  get name() {
    return this._name;
  }

  get breed() {
    return this._breed;
  }

  get weight() {
    return this._weight;
  }

  feed() {
    this._weight++;
  }
}

const max = new Dog('Golden Retriever', 'Max', 30);


console.log(max.breed);
console.log(max.name);
console.log(max.weight);

// >>> Golden Retriever
// >>> Max
// >>> 30


max.feed();


console.log(max.weight);

// >>> 31
```

```js
class Vehicle {
  constructor(make, color, wheelCount) {
    this._make  = make;
    this._color = color;
    this._wheelCount = wheelCount;
  }

  get make() { return this._make; }
  get color() { return this._color; }
  get wheelCount() { return this._wheelCount; }

  static generateRandomColor() {
    let randomNumber = Math.random();
    return randomNumber <= 0.33 ? 'Red' : randomNumber <= 0.66 ? 'Green' : 'Blue';
  }
}

class Car extends Vehicle {
  constructor(make, color) {
    super(make, color, 4);
    this._doorCount = 5;
  }

  get doorCount() {
    return this._doorCount;
  }
}

class Bike extends Vehicle {
  constructor(make, color) {
    super(make, color, 2);
    this._pedalCount = 2;
  }

  get pedalCount() {
    return this._pedalCount;
  }
}


const car = new Car('BMW', Vehicle.generateRandomColor());


console.log(car.make)
console.log(car.color)
console.log(car.doorCount)

// >>> BMW
// >>> Blue
// >>> 5


const bike = new Bike('BMC', Vehicle.generateRandomColor());


console.log(bike.make);
console.log(bike.color);
console.log(bike.pedalCount);

// >>> BMC
// >>> Red
// >>> 2


console.log(bike.doorCount);

// >>> undefined
```

# Dates

```js
let now           = new Date();
let currentMillis = Date.now();


console.log(now);
console.log(currentMillis);

// >>> Thu Dec 31 2020 14:03:04 GMT+0000 (Greenwich Mean Time)
// >>> 1609423384201


console.log(new Date(0));
console.log(new Date(1 * 24 * 60 * 60 * 1000));
console.log(new Date(currentMillis));

// >>> Thu Jan 01 1970 01:00:00 GMT+0100 (Greenwich Mean Time)
// >>> Fri Jan 02 1970 01:00:00 GMT+0100 (Greenwich Mean Time)
// >>> Thu Dec 31 2020 14:03:04 GMT+0000 (Greenwich Mean Time)


console.log(new Date(2020, 12, 31, 15, 30, 0, 0));
console.log(new Date(2020, 12, 31));

// >>> Sun Jan 31 2021 15:30:00 GMT+0000 (Greenwich Mean Time)
// >>> Sun Jan 31 2021 00:00:00 GMT+0000 (Greenwich Mean Time)
```

```js
let millis = Date.parse('2021-01-01T13:00:00.000-03:00');
let date   = new Date(millis - 24 * 60 * 60 * 1000);


console.log(millis);
console.log(date);

// >>> 1609516800000
// >>> Thu Dec 31 2020 16:00:00 GMT+0000 (Greenwich Mean Time)


console.log(date.getDate());
console.log(date.getMonth() + 1);
console.log(date.getFullYear());
console.log(date.getHours());
console.log(date.getMinutes());
console.log(date.getSeconds());
console.log(date.getMilliseconds());

// >>> 31
// >>> 12
// >>> 2020
// >>> 16
// >>> 0
// >>> 0
// >>> 0
```

# Regular expressions

```js
let text1   = 'abc 1234 def';
let text2   = 'abc      def';
let numbers = /[0-9]+/;


console.log(text1.constructor);
console.log(text2.constructor);
console.log(numbers.constructor);

// >>> function String() { [native code] }
// >>> function String() { [native code] }
// >>> function RegExp() { [native code] }


console.log(numbers.test(text1));
console.log(numbers.test(text2));

// >>> true
// >>> false


console.log(numbers.exec(text1));
console.log(numbers.exec(text2));
// >>> (1) ["1234"]
// >>> null
```

```js
let text  = 'ab 1234 cd';
let regex = /[a-z]/;


console.log('Has any match? ' + regex.test(text));

// >>> Has any match? true


console.log('First match: ')
console.log(regex.exec(text));
console.log(regex.exec(text));
console.log(regex.exec(text));

// >>> First match: 
// >>> (1) ["a"]
// >>> (1) ["a"]
// >>> (1) ["a"]


console.log('All matches with global flag: ')

regex = /[a-z]/g;

for (let i = 0; i < 5; i++)
  console.log(regex.exec(text));

// >>> All matches with global flag: 
// >>> (1) ["a"]
// >>> (1) ["b"]
// >>> (1) ["c"]
// >>> (1) ["d"]
// >>> null


console.log('Case-insensitive flag: ')

regex = /[A-Z]/gi;

for (let i = 0; i < 5; i++)
  console.log(regex.exec(text));

// >>> Case-insensitive flag: 
// >>> (1) ["a"]
// >>> (1) ["b"]
// >>> (1) ["c"]
// >>> (1) ["d"]
// >>> null
```

## Regex flags

<table>
  <tbody>
    <tr>
      <th>Flag</th>
      <th>Description</th>
    </tr>
    <tr>
      <td>g</td><td>Global search</td>
    </tr>
    <tr>
      <td>i</td><td>Case-insensitive search</td>
    </tr>
    <tr>
      <td>m</td><td>Multi-line search</td>
    </tr>
    <tr>
      <td>s</td><td>Allows <tt>.</tt> to match newline characters</td>
    </tr>
  </tbody>
</table>

## String methods that work with regexes

```js
const text = 'abc 123 abbcc';


console.log(text.match( /ab+c+/   ));
console.log(text.match( /a(b+)c+/ ));

// >>> (1) ["abc"]
// >>> (2) ["abc", "b"]


let matchIterator = text.matchAll( /a(b+)c+/g );


console.log(matchIterator.next().value);
console.log(matchIterator.next().value);
console.log(matchIterator.next().value);

// >>> (2) ["abc", "b"]
// >>> (2) ["abbcc", "bb"]
// >>> undefined


console.log(     text.search( /[0-9]/ ));
console.log(text[text.search( /[0-9]/ )]);
console.log(text[text.search( /[0-9]/ ) + 1]);
console.log(text[text.search( /[0-9]/ ) + 2]);

// >>> 4
// >>> 1
// >>> 2
// >>> 3
```

```js
console.log('abc def  ghi'.split( /\s+/ ));

// >>> (3) ["abc", "def", "ghi"]


console.log('abc def  ghi'.replace( /\s+/ , '_' ));
console.log('abc def  ghi'.replace( /\s+/g , '_' ));

// >>> abc_def  ghi
// >>> abc_def_ghi
```

# JSON

```js
const sportsCar = {
  make: 'BMW',
  model: 'M2'
}

let objectString = JSON.stringify(sportsCar);


console.log(       objectString);
console.log(typeof objectString);

// >>> {"make":"BMW","model":"M2"}
// >>> string


let superCar = JSON.parse('{ "make": "Ferrari", "model": "SF90 Stradale" }');


console.log(superCar.make);
console.log(superCar.model);

// >>> Ferrari
// >>> SF90 Stradale


let array = JSON.parse('[1, 2, 3, 4]');


console.log(array);

// >>> (4) [1, 2, 3, 4]
```

# Iterators

## Array iterators

```js
let numbers = [1, 2, 3, 4];


for (let key in numbers)
  console.log(key + ` (${typeof key})`);

// >>> 0 (string)
// >>> 1 (string)
// >>> 2 (string)
// >>> 3 (string)


for (let value of numbers)
  console.log(value + ` (${typeof value})`);

// >>> 1 (number)
// >>> 2 (number)
// >>> 3 (number)
// >>> 4 (number)


for (let value of numbers.values())
  console.log(value + ` (${typeof value})`);

// >>> 1 (number)
// >>> 2 (number)
// >>> 3 (number)
// >>> 4 (number)


console.log(numbers);
console.log(numbers.values());

// >>> (4) [1, 2, 3, 4]
// >>> Array Iterator {}
```

## Symbol.iterator

```js
let numbers        = [1, 2, 3, 4];
let iteratorSymbol = Symbol.iterator;


for (let key of numbers.keys())
  console.log(`numbers[${key}] = ${numbers[key]}`);

// >>> numbers[0] = 1
// >>> numbers[1] = 2
// >>> numbers[2] = 3
// >>> numbers[3] = 4


console.log(iteratorSymbol in numbers.keys());

// >>> true


console.log(numbers[iteratorSymbol]);
console.log(numbers[iteratorSymbol]());

// >>> function values() { [native code] }
// >>> Array Iterator {}


let numbersIterator = numbers[iteratorSymbol]();


for (let value of numbersIterator)
  console.log(value);

// >>> 1
// >>> 2
// >>> 3
// >>> 4


for (let value of numbersIterator)
  console.log(value);

// >>>


console.log(numbersIterator.next());

// >>> {
// >>>   value: undefined,
// >>>   done: true
// >>> }


console.log('Iterator already consumed: ' + numbersIterator.next().done);

// >>> Iterator already consumed: true


for (let value of numbers[Symbol.iterator]())
  console.log(value);

// >>> 1
// >>> 2
// >>> 3
// >>> 4
```

```js
let numbers         = [1, 2, 3, 4];
let numbersIterator = numbers[Symbol.iterator]();
let done            = false;


while (!done) {
  let entry = numbersIterator.next();
  done      = entry.done;

  console.log(entry);
}

// >>> {
// >>>   value:  1,
// >>>   done:  false
// >>> }
// >>> {
// >>>   value: 2,
// >>>   done: false
// >>> }
// >>> {
// >>>   value: 3,
// >>>   done: false
// >>> }
// >>> {
// >>>   value: 4,
// >>>   done: false
// >>> }
// >>> {
// >>>   value: undefined,
// >>>   done: true
// >>> }


numbersIterator = numbers[Symbol.iterator]();


while(true) {
  let entry = numbersIterator.next();

  if (entry.done)  break;
  else             console.log(entry.value);
}

// >>> 1
// >>> 2
// >>> 3
// >>> 4
```

## Custom iterators

```js
let garage = {
    car       : { 'make': 'Volkswagen' , 'model': 'Polo'          },
    sportsCar : { 'make': 'BMW'        , 'model': 'M2'            },
    superCar  : { 'make': 'Ferrari'    , 'model': 'SF90 Stradale' }
}


for (let car in garage)
    console.log(car);

// >>> car
// >>> sportsCar
// >>> superCar


for (let car of garage)
    console.log(car);

// >>> TypeError: garage is not iterable


garage[Symbol.iterator] = function() {
    let keys           = Object.keys(garage);
    let carCount       = keys.length;
    let iterationCount = -1;

    return {
        next: () => {
            if (++iterationCount >= carCount) {
                return {
                    done: true,
                    value: undefined
                }
            }
            else {
                let currentCar = garage[keys[iterationCount]];

                return {
                    done: false,
                    value: currentCar.make + ' ' + currentCar.model
                }
            }
        }
    };
};


for (let car in garage)
    console.log(car);

// >>> car
// >>> sportsCar
// >>> superCar


for (let car of garage)
    console.log(car);

// >>> Volkswagen Polo
// >>> BMW M2
// >>> Ferrari SF90 Stradale
```

# Generators

```js
function* range() {
  yield 1;
  yield 2;
  yield 3;
}

let generator = range();


for (let number of generator)
  console.log(number);

// >>> 1
// >>> 2
// >>> 3
```

```js
function* range() {
  yield* [1, 2, 3];
}


for (let number of range())
  console.log(number);

// >>> 1
// >>> 2
// >>> 3
```

```js
function* range(maxIterations = 100) {
  let iterationCount = 0;

  for (let i = 0; i < maxIterations; i++)
    yield iterationCount++;
}


for (let number of range(10))
  console.log(number);

// >>> 0
// >>> 1
// >>> 2
// >>> 3
// >>> 4
// >>> 5
// >>> 6
// >>> 7
// >>> 8
// >>> 9
```

```js
class Garage {
  constructor() {
    this._cars = []
  }

  addCar(make, model) {
    this._cars.push({make, model});
  }

  get cars() {
    return this._cars;
  }

  *[Symbol.iterator]() {
    yield* this._cars.map(car => car.make + ' ' + car.model)
  }
}


let myGarage = new Garage();

myGarage.addCar('Volkswagen', 'Polo');
myGarage.addCar('BMW', 'M2');
myGarage.addCar('Ferrari', 'SF90 Stradale');


for (let car of myGarage.cars)
  console.log(car);

// >>> {
// >>>   make: "Volkswagen",
// >>>   model: "Polo"
// >>> }
// >>> {
// >>>   make: "BMW",
// >>>   model: "M2"
// >>> }
// >>> {
// >>>   make: "Ferrari",
// >>>   model: "SF90 Stradale"
// >>> }


for (let car of myGarage)
  console.log(car);

// >>> Volkswagen Polo
// >>> BMW M2
// >>> Ferrari SF90 Stradale
```

```js
```

# Error handling

## Try...catch

```js
const divide = (dividend, divisor) => {
  let result;

  try {
    result = dividend / divisor;

    if (result === Infinity)
      throw new Error('Infinite result');
  }
  catch (error) {
    console.log('Error: ' + error.message);
  }
  finally {
    console.log('Execution complete.')
  }

  return result;
}


console.log(divide(5, 2))
console.log(divide(1, 0))

// >>> Execution complete.
// >>> 2.5
// >>> Error: Infinite result
// >>> Execution complete.
// >>> Infinity
```

## Error types

```js
try {
  console.log(1.00.toPrecision(500));
}
catch (error) {
  console.log([error.name, error.message].join(': '));
}

// >>> RangeError: toPrecision() argument must be between 1 and 100
```

```js
try {
  console.log('The result is ' + result);
}
catch (error) {
  console.log([error.name, error.message].join(': '));
}

// >>> ReferenceError: result is not defined
```

```js
try {
  console.log(eval("2 +' 2"));
}
catch (error) {
  console.log([error.name, error.message].join(': '));
}

/// >>> SyntaxError: Invalid or unexpected token
```

```js
try {
  console.log('abc'.myFunction());
}
catch (error) {
  console.log([error.name, error.message].join(': '));
}

// >>> TypeError: "abc".myFunction is not a function
```

```js
try {
  decodeURI('http://%//www.google.com')
}
catch (error) {
  console.log([error.name, error.message].join(': '));
}

// >>> URIError: URI malformed
```

# Promises

## Asynchronous programming

Asynchronous programming involves executing other tasks while we wait for a parallel task to complete:

```
               ┌────────────────────┐
               │ Turn dishwasher on │
               └────────────────────┘
                         └──────────────────────┐
            Washing ──>  .                      V
                         .             ┌─────────────────┐
                         .             │ Clean the house │
                         .             └─────────────────┘
               DONE ──>  .                      │
                         ┌──────────────────────┘
                         V                      .
               ┌───────────────────┐            .
               │   Remove dishes   │            .
               │  from dishwasher  │            .
               └───────────────────┘            .
                         └──────────────────────┐
                                                V
                                       ┌─────────────────┐
                                       │ Clean the house │
                                       └─────────────────┘
```

An example of asynchoronous operation:

```js
console.log('Begin');

setTimeout(
  () => console.log('After 2 seconds'),
  2 * 1000
);

console.log('End');

// >>> Begin
// >>> End
// >>> After 2 seconds
```
In the example above, the lines `Begin` and `End` were printed first.

Then, due to the `setTimeout(callback, wait_time)` function, the phrase `After 2 seconds` was only printed after the  wait operation.

## Future result

*[Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)* represent the future result (a result to be obtained) of an asynchronous operation.

They begin in a *pending* state and *settle* to the *fullfilled* state on operation success.

If the operation fails they *settle* to a *rejected* state:

```
                          ┌─────────┐
                          │ PENDING │
                          └─────────┘
                               │
               ┌ ─ ─ error ─ ─ └──── success ────┐
               |                                 │
               │                                 │
            reject()                         resolve()
               |                                 │
               V                                 V
         ┌──────────┐                     ┌────────────┐
         │ REJECTED │                     │ FULLFILLED │
         └──────────┘                     └────────────┘
```

In the flow above, we can see that the Promise object has its state changed by the `resolve()`  and `reject()` functions:

<html>
  <table style="width: 30%">
    <tr>
      <td><tt>resolve()</tt></td>
      <td><i>pending &nbsp;&rarr;&nbsp; fullfilled</i></td>
    </tr>
    <tr>
      <td><tt>reject()</tt></td>
      <td><i>pending &nbsp;&rarr;&nbsp; rejected</i></td>
    </tr>
  </table>
</html>

<br>

## Resolve or reject

The Promise [constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/Promise) receives an *executor function* as argument.

This function runs automatically when the Promise object is instantiated, and dictates if the *pending* state should settle to the *fulfilled* or the the *rejected* state. It receives the `resolve` and `reject` functions as parameters:

```js
const executorFunction = (resolve, reject) => {
    console.log('Processing...');
  
    // Simulate some asynchronous processing with setTimeout
    setTimeout(
        () => {
            console.log('Processing complete!');

            if (Math.random() > 0.5)
                resolve('OK');
            else
                reject('NOK');
        },
        2 * 1000
    );
}

const remoteProcessing = () => new Promise(executorFunction);


remoteProcessing();

// >>> Processing...
// >>> Processing complete!
```

We can then take actions after the Promise is settled to the *fullfilled* or *rejected* states:

```js
const successCallback = (resolvedValue)   => console.log('Promise fullfilled: ' + resolvedValue);
const errorCallback   = (rejectionReason) => console.log('Promise rejected: ' + rejectionReason);


remoteProcessing().then(successCallback)
                  .catch(errorCallback);

// >>> Processing...
// >>> Processing complete!
// >>> Promise fullfilled: OK
```

`.then(callback)` also returns a Promise (whose result is the result of the `callback`), so that it can be chained to another `.then()`:

```js
function process(value) {
  return new Promise(
    function(resolve, reject) {
      setTimeout(
        () => resolve(value + 1),
        1000
      );
    }
  );
}

function successHandler(result) {
  console.log(`Received ${result}, returning ${result * 2}`);
  return result * 2;
}


process(1).then(successHandler)
          .then(successHandler)
          .then(successHandler)
          .then((result) => console.log('Received ' + result))
          .catch((rejectReason) => console.log('Error: ' + rejectReason));

// >>> Received 2, returning 4
// >>> Received 4, returning 8
// >>> Received 8, returning 16
// >>> Received 16
```

## Concurrent execution

The approach shown above can be very useful in situations where several asynchoronous operations depend on each other and therefore need to be executed in a certain order.

If the asynchronous operations do not depend on each other and the order does not matter, we can execute all of them *concurrently* with the `Promise.all()` method:

```js
const process = (value) => new Promise(
  (resolve, reject) => resolve(value + 1)
);

Promise.all(
         [process(1), process(1), process(1)]
       )
       .then(
         (results) => console.log(results)
       )
       .catch(
         (failure) => console.log('Error: ' + failure)
       );

// >>> (3) [2, 2, 2]
```

The `Promise.all()` method returns another promise that can:

 - *resolve* with an array of all resolve values, if all the promises resolve;
 - *reject* with the first reject value that occurs (*fail fast*);

# Async...await

`async...await` is a new syntax introduced by (*[ES8](https://www.ecma-international.org/ecma-262/8.0/)*) that allows us to handle asynchronous actions in a way that is more readable and similar to synchronous programs.

It also allows us to better keep track of the results the promises resolve to:

```js
const multiplyByTwo = (value) => new Promise(
  (resolve, reject) => {
    console.log('Multiplying by 2...');
    resolve(value * 2);
  }
);

async function generateNumbers() {
  let value   = 1;
  let result1 = await multiplyByTwo(value);    // Pause until promise resolves
  let result2 = await multiplyByTwo(result1);  // Pause until promise resolves
  let result3 = await multiplyByTwo(result2);  // Pause until promise resolves

  console.log(result1);
  console.log(result2);
  console.log(result3);
}


generateNumbers();

// >>> Multiplying by 2...
// >>> Multiplying by 2...
// >>> Multiplying by 2...
// >>> 2
// >>> 4
// >>> 8
```

The results of `async` functions are also Promises:

```js
async function generateNumber() {
  let result = await multiplyByTwo(3);  // Pause until promise resolves

  console.log(result);

  return result;
}


generateNumber().then(result => console.log('The result is ' + result));

// >>> Multiplying by 2...
// >>> 6
// >>> The result is 6
```

We can use `try...catch` with `async...wait` for error handling:

```js
const multiplyByTwo = (value) => new Promise((resolve, reject) => {
    console.log('Multiplying by 2...');

    if (Math.random() > 0.5)
      resolve(value * 2);
    else
      reject('Cannot multiply by 2');
});

const generateNumbers = async () => {
    try {
      let value = 1;

      let result1 = await multiplyByTwo(value);    // Pause until promise resolves
      console.log('1st result: ' + result1);

      let result2 = await multiplyByTwo(result1);  // Pause until promise resolves
      console.log('2nd result: ' + result2);

      let result3 = await multiplyByTwo(result2);  // Pause until promise resolves
      console.log('3rd result : ' + result3);
    }
    catch (error) {
        console.log('ERROR: ' + error);
    }
}


generateNumbers();

// >>> Multiplying by 2...
// >>> 1st result: 2
// >>> Multiplying by 2...
// >>> 2nd result: 4
// >>> ERROR: Cannot multiply by 2
```

If the order of the asynchronous actions does not matter, they can be executed concurrently:

```js
const generateNumbers = async () => {
    try {
      let promise1 = await multiplyByTwo(1);
      let promise2 = await multiplyByTwo(2);
      let promise3 = await multiplyByTwo(3);

      results = await Promise.all([promise1, promise2, promise3]);

      console.log(`Results: ${results[0]}, ${results[1]}, ${results[2]}`);
    }
    catch (error) {
        console.log('ERROR: ' + error);
    }
}

generateNumbers();
```

If all promises resolve, we have the following output:

```js
// >>> Multiplying by 2...
// >>> Multiplying by 2...
// >>> Multiplying by 2...
// >>> Results: 2, 4, 6
```

However, if any promises reject, `Promise.all` rejects as soon as the first rejection occurs:

```js
// >>> Multiplying by 2...
// >>> ERROR: Cannot multiply by 2
```

# AJAX

AJAX (Asynchronous JavaScript and XML) is a web development technique that enables us to develop more interative applications.

With AJAX we can reload just a part of the page without the need to reload everyting all over again.

Also, AJAX allows us to perform HTTP requests in the background without interfering with the page behavior and display. The next section is related to them.

## XHR requests

We can perform HTTP requests with the XHR ([XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)) API:

```js
let xhr = new XMLHttpRequest();

xhr.responseType = 'json';

xhr.onreadystatechange = () => {
  if (xhr.readyState === XMLHttpRequest.DONE) {
    console.log('Request returned code ' + xhr.status);
    console.log('Response:');
    console.log(xhr.response);
  }
};

xhr.open('GET', 'https://official-joke-api.appspot.com/random_joke');
xhr.send();

// >>> Request returned code 200
// >>> Response:
// >>> {
// >>>   id: 245,
// >>>   type: "general",
// >>>   setup: "What do you give a sick lemon?",
// >>>   punchline: "Lemonaid."
// >>> }
```

```js
let xhr = new XMLHttpRequest();

xhr.responseType = 'json';

xhr.onreadystatechange = () => {
  if (xhr.readyState === XMLHttpRequest.DONE) {
    console.log('Request returned code ' + xhr.status);
    console.log('Response:');
    console.log(xhr.response);
  }
};

xhr.open('POST', 'https://reqres.in/api/users');
xhr.setRequestHeader('Content-type', 'application/json');
xhr.send(JSON.stringify({ name: 'john', job: 'admin' }));

// >>> Request returned code 201
// >>> Response:
// >>> {
// >>>   name: "john",
// >>>   job: "admin",
// >>>   id: "592",
// >>>   createdAt: "2020-12-30T12:59:06.822Z"
// >>> }
```

## Fetch API

Besides XHR, the [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) API is a newer API to handle requests. It is more flexible and powerful, and its work is based on Promises:

```js
fetch(
  'https://official-joke-api.appspot.com/random_joke'
)
.then(
  (response) => {
    if (response.ok) {
      let jsonPromise = response.json();
      return jsonPromise;
    }
    throw new Error('GET request failed');
  }
)
.then(
  (json) => console.log(json)
)
.catch(
  (error) => console.log('Error: ' + error.message)
)

// >>> {
// >>>   id: 218,
// >>>   type: "general",
// >>>   setup: "What do you call a fly without wings?",
// >>>   punchline: "A walk."
// >>> }
```

```js
fetch(
  'https://reqres.in/api/users',
  {
    method  : 'POST',
    headers : { 'Content-type': 'application/json' },
    body    : JSON.stringify( { name: 'john', job: 'admin' } )
  }
)
.then(
  (response) => {
    if (response.ok) {
      let jsonPromise = response.json();
      return jsonPromise;
    }
    throw new Error('POST request failed');
  }
)
.then(
  (json) => console.log(json)
)
.catch(
  (error) => console.log('Error: ' + error.message)
)

// >>> {
// >>>   name: "john",
// >>>   job: "admin",
// >>>   id: "619",
// >>>   createdAt: "2020-12-30T14:30:29.966Z"
// >>> }
```

```js
const fetchJoke = async () => {
  try {
    const response = await fetch('https://official-joke-api.appspot.com/random_joke');
    
    if (response.ok)  console.log(await response.json());
    else              throw new Error('GET request failed');
  }
  catch (error) {
    console.log('Error: ' + error.message);
  }
}


fetchJoke();

// >>> {
// >>>   id:16 ,
// >>>   type:" programming",
// >>>   setup:" What's the object-oriented way to become wealthy?",
// >>>   punchline:" Inheritance"
// >>> }
```

```js
const createNewUser = async () => {
  try {
    const response = await fetch(
      'https://reqres.in/api/users',
      {
        method  : 'POST',
        headers : { 'Content-type': 'application/json' },
        body    : JSON.stringify( { name: 'john', job: 'admin' } )
      }  
    );
    
    if (response.ok)  console.log(await response.json());
    else              throw new Error('POST request failed');
  }
  catch (error) {
    console.log('Error: ' + error.message);
  }
}


createNewUser();

// >>> {
// >>>   name: "john",
// >>>   job: "admin",
// >>>   id: "298",
// >>>   createdAt: "2020-12-30T15:08:18.914Z"
// >>> }
```

# JavaScript projects with npm

The [Node Package Manager](https://www.npmjs.com/get-npm) (npm) enables JavaScript code sharing and reuse.

## Project creation

A new project can be created with the `npm init` command.

This commands asks for some inputs such as *project name* and *description*, and generates the `package.json` manifest:

```json
{
  "name": "example_project",
  "version": "1.0.0",
  "description": "An example project with NPM.",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "John Doe",
  "license": "ISC"
}
```

## Module installation

To install a module (e.g. the [upper-case](https://www.npmjs.com/package/upper-case) module), the `npm install` command is used:

```bash
npm install upper-case --save
```

The `--save` flag adds the module as a project dependency in the `package.json` file:

```json
{
  "name": "example_project",
  "version": "1.0.0",
  [...]

  "dependencies": {
    "upper-case": "^2.0.2"
  }
}
```

The module can then be used in our project. Below is an example `index.js` file:

```js
let lib = require("upper-case");

console.log(lib.upperCase("test"));  // test  -->  TEST
```

The code can then be executed with the *[NodeJS](https://nodejs.org/en/) JavaScript Runtime*:

```js
user@PC:~$ node index.js 

>>> TEST
```

# Modules

## NodeJS

With the *[NodeJS](https://nodejs.org/en/) JavaScript Runtime* we can use the `exports` syntax to create reusable modules.

For example, we can write the following to a file called `number_utils.js`:

```js
module.exports = {
  pi: 3.14,

  randomInt() {
    return Math.trunc(Math.random() * 100);  // Return random integer
  }
}
```

The exported object can then be used by another file, `main.js`:

```js
const Utils = require('./number_utils.js');


console.log('The value of PI is ' + Utils.pi);
console.log('A random int: ' + Utils.randomInt());

// >>> The value of PI is 3.14
// >>> A random int: 47
```

## ES6

ES6 brings a more readable export syntax.

### Default export

To export one module per file (just as we did with `module.exports`), we can use the `export default` syntax shown in the `garage.js` file below:

```js
let Garage = {
  cars: [
    {
      make: "Mercedes-Benz",
      model: "S class"
    },
    {
      make: "BMW",
      model: "7 series"
    }
  ]
};

export default Garage;
```

We can then import the exported `Garage` object inside another file:

```js
import MyGarage from './garage';


Garage.cars.forEach(car => console.log(`${car.make} ${car.model}`));

// >>> Mercedes-Benz S class
// >>> BMW 7 series
```

### Named exports

*Named exports* can also be used to export several objects, as shown in this modified version of `garage.js`:

```js
let Garage = {
  cars: [
    {
      make: "Mercedes-Benz",
      model: "S class"
    },
    {
      make: "BMW",
      model: "7 series"
    }
  ]
}

const getAllCars  = () => Garage.cars.map(car => `${car.make} ${car.model}`);

const addCar      = (make, model) => Garage.cars.push({make, model});

const hasMercedes = () => Garage.cars
                                .filter(car => car.make === 'Mercedes-Benz')
                                .length > 0;

const hasFerrari  = () => Garage.cars
                                .filter(car => car.make === 'Ferrari')
                                .length > 0;

export { getAllCars, addCar, hasMercedes, hasFerrari };
```

The named objects can then be imported in another file:

```js
import { getAllCars, addCar } from './garage';


addCar('Mini', 'Cooper S');


console.log(getAllCars());

// >>>  ["Mercedes-Benz S class", "BMW 7 series", "Mini Cooper S"]
```

*Default* and *named* exports can also be mixed, and the `as` keyword can be used to create *aliases*.

These are demonstrated in another version of the `garage.js` file:

```js
let Garage = {
  cars: [
    {
      make: "Mercedes-Benz",
      model: "S class"
    },
    {
      make: "BMW",
      model: "7 series"
    }
  ]
}

const getAllCars  = () => Garage.cars.map(car => `${car.make} ${car.model}`);

const addCar      = (make, model) => Garage.cars.push({make, model});

const hasMercedes = () => Garage.cars
                                .filter(car => car.make === 'Mercedes-Benz')
                                .length > 0;


export { addCar, hasMercedes as hasBenz };

export const hasFerrari = () => Garage.cars
                                      .filter(car => car.make === 'Ferrari')
                                      .length > 0;

export default getAllCars;
```

The exported objects are then imported below:

```js
import { addCar as newCar, hasBenz, hasFerrari } from './garage';
import allCars from './garage';


newCar('Ferrari', '812 Superfast');


console.log(hasBenz());
console.log(hasFerrari());
console.log(allCars());

// >>> true
// >>> true
// >>> ['Mercedes-Benz S class', 'BMW 7 series', 'Ferrari 812 Superfast']
```

# Document Object Model (DOM)

We can change webpage components using JavaScript, by using the Document Object Model (DOM) programming interface.

The DOM makes the page contents available to JavsScript as a tree o objects in memory.

The example simple page below...

```html
<html>

    <head>
        <title>My page</title>
    </head>

    <body>
        <p id="p1">Text</p>
    </body>

</html>
```

... has an Object Model as this one:

```
         ┌───────────┐
         │ Document  │
         └─────┬─────┘
         ┌─────┴─────┐
         │  <head>   │
         └─────┬─────┘
      ┌────────┴────────┐
┌─────┴─────┐     ┌─────┴─────┐
│  <head>   │     │  <body>   │
└─────┬─────┘     └─────┬─────┘
┌─────┴─────┐     ┌─────┴─────┐     ┌───────────┐
│  <title>  │     │    <p>    ├─────┤ id : "p1" │
└─────┬─────┘     └─────┬─────┘     └───────────┘
┌─────┴─────┐     ┌─────┴─────┐
│ "My page" │     │  "Text"   │
└───────────┘     └───────────┘
```

We can use the `<script>` tag to write some JavaScript code and define a function to interact with the model above:

```html
<html>

    <head>
        <title>My webpage</title>
    </head>

    <body>
        <p id="p1">Some text here</p>
        
        <button type="button" onclick="changeText()">Change</button>
        
        <script>
            const changeText = () => {
                document.getElementById('p1').innerHTML = 'Updated text';
            }
        </script>
    </body>

</html>
```

The function defined inside the `<script>` tag searches the DOM, and finds the `<p>` element by its id and changes its text.

The `onClick` attribute of the `<button>` element adds a message to the queue (with the function defined) once the *mouse clicking event* happens.

When the event loop removes the message from the queue, a new stack grame will be created for `changeText()` and the text displayed in the page will change from *"Text"* to *"Updated text"*.

We could also write the function to an JavaScript file, such as the `changeDom.js` example below...

```js
const changeText = () => {
    document.getElementById('p1').innerHTML = 'Updated text';
}
```
... and then reference the file in the HTML file:

```html
<html>
    <head>
        <title>My webpage</title>
    </head>
    <body>
        <p id="p1">Some text here</p>
        
        <button type="button" onclick="changeText()">Change</button>
        
        <script src="domChange.js"></script>
    </body>
</html>
```

The advantage of adding the `<script>` tag at the end on the `<body>` tag is that all page contents are loaded and displayed to the user soon, before the script loads (a  proccess that can be slow for big scripts).

## Example events

 - Mouse
   * onclick
   * onmouseenter
   * onmouseleave
- Keyboard
  * onkeydown
  * onkeypress
- Form
  * onsubmit
- Drag
  * ondrag
  * ondrop
- Clipboard
  * oncopy
  * onpaste


# Dialogs

## Alert dialog

```js
alert('This is an alert message');
```

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   This page says                                        │
│   This is an alert message                              │
│                                                         │
│                                                         │
│                                                         │
│                                              ┌────────┐ │
│                                              │   OK   │ │
│                                              └────────┘ │
└─────────────────────────────────────────────────────────┘
```

## Confiramtion dialog

```js
confirm('Are you sure?');
```

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   This page says                                        │
│   Are you sure?                                         │
│                                                         │
│                                                         │
│                                                         │
│                                    ┌────────┐┌────────┐ │
│                                    │   OK   ││ Cancel │ │
│                                    └────────┘└────────┘ │
└─────────────────────────────────────────────────────────┘
```

The `confirm()` method returns `true` or `false`, depending on the user choice.

## Prompt dialog

```js
prompt('What is your name?', 'Joe');
```

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   This page says                                        │
│   What is your name?                                    │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Joe                                                 │ │
│ └─────────────────────────────────────────────────────┘ │
│                                    ┌────────┐┌────────┐ │
│                                    │   OK   ││ Cancel │ │
│                                    └────────┘└────────┘ │
└─────────────────────────────────────────────────────────┘
```

The `confirm()` method returns the string entered by the user, or `false` if the user clicks on *Cancel*.


# References

## Main reference

 - [CodeCademy: Learn JavaScript](https://www.codecademy.com/courses/introduction-to-javascript)

## Secondary references

 - [Codeburst.io: JavaScript — Null vs. Undefined](https://codeburst.io/javascript-null-vs-undefined-20f955215a2)
 - [FreeCodeCamp: Learn ES6 The Dope Way Part II: Arrow functions and the ‘this’ keyword](https://www.freecodecamp.org/news/learn-es6-the-dope-way-part-ii-arrow-functions-and-the-this-keyword-381ac7a32881/)
 - [JavaScript Tutorial](https://www.javascripttutorial.net)
 - [JavaScript.info: Date and time](https://javascript.info/date)
 - [JavaScript.info: Promises chaining](https://javascript.info/promise-chaining)
 - [JS CheatSheet](https://htmlcheatsheet.com/js/)
 - [MDN Web Docs:  Concurrency model and the event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Event_loop)
 - [MDN Web Docs: Iterators and Generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/c)
 - [MDN Web Docs: JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
 - [MDN Web Docs: Regular expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)
 - [MDN Web Docs: Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
 - [W3Schools: JavaScript Errors - Throw and Try to Catch](https://www.w3schools.com/js/js_errors.asp)
 - [W3Schools: Node.js NPM](https://www.w3schools.com/nodejs/nodejs_npm.asp)
 - [Wikipedia: Ajax (programming)](https://en.wikipedia.org/wiki/Ajax_(programming))
