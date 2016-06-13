title: Code camp 2016-06-16
author:
  name: Joel Sannerstedt
  url: http://jsannerstedt.github.io/
output: code-camp.html
controls: false
style: ../main.css

--

# Code camp
## 2016-06-16

--

### Intro
Selected parts of ES6 cheat sheet from [https://github.com/DrkSephy/es6-cheatsheet](https://github.com/DrkSephy/es6-cheatsheet)

--

### var versus let / const

> Besides `var`, we now have access to two new identifiers for storing values
—`let` and `const`. Unlike `var`, `let` and `const` statements are not hoisted
to the top of their enclosing scope.

--

## Arrow Functions

Using **Arrow Functions**, the lexical value of `this` isn't shadowed.

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(character => this.name + character);
};
```

> **Best Practice**: Use **Arrow Functions** whenever you need to preserve the
lexical value of `this`.

--
### Arrow Functions - continued

Arrow Functions are also more concise when used in function expressions which
simply return a value:

```javascript
 // Function Expression
var squares = arr.map(function (x) { return x * x });
```

```javascript
const arr = [1, 2, 3, 4, 5];
// Arrow Function for terser implementation
const squares = arr.map(x => x * x);
```

> **Best Practice**: Use **Arrow Functions** in place of function expressions
when possible.

--

## Strings

With ES6, the standard library has grown immensely. Along with these changes
are new methods which can be used on strings, such as `.includes()` and
`.repeat()`.

--

### Template Literals

Using **Template Literals**, we can now construct strings that have special
characters in them without needing to escape them explicitly.

```javascript
var text = "This string contains \"double quotes\" which are escaped.";
```

```javascript
let text = `This string contains "double quotes" which don't need to be escaped anymore.`;
```

--

**Template Literals** also support interpolation, which makes the task of
concatenating strings and values:

```javascript
var name = 'Tiger';
var age = 13;

console.log('My cat is named ' + name + ' and is ' + age + ' years old.');
```

Much simpler:

```javascript
const name = 'Tiger';
const age = 13;

console.log(`My cat is named ${name} and is ${age} years old.`);
```

--

In ES5, we handled new lines as follows:

```javascript
var text = (
    'cat\n' +
    'dog\n' +
    'nickelodeon'
);
```

Or:

```javascript
var text = [
    'cat',
    'dog',
    'nickelodeon'
].join('\n');
```

--

**Template Literals** will preserve new lines for us without having to
explicitly place them in:

```javascript
let text = ( `cat
dog
nickelodeon`
);
```

**Template Literals** can accept expressions, as well:

```javascript
let today = new Date();
let text = `The time and date is ${today.toLocaleString()}`;
```

--

## Destructuring

Destructuring allows us to extract values from arrays and objects (even deeply
nested) and store them in variables with a more convenient syntax.

--

### Destructuring Arrays

```javascript
var arr = [1, 2, 3, 4];
var a = arr[0];
var b = arr[1];
var c = arr[2];
var d = arr[3];
```

```javascript
let [a, b, c, d] = [1, 2, 3, 4];

console.log(a); // 1
console.log(b); // 2
```

--

### Destructuring Objects

```javascript
var luke = { occupation: 'jedi', father: 'anakin' };
var occupation = luke.occupation; // 'jedi'
var father = luke.father; // 'anakin'
```

```javascript
let luke = { occupation: 'jedi', father: 'anakin' };
let {occupation, father} = luke;

console.log(occupation); // 'jedi'
console.log(father); // 'anakin'
```

--

## Modules

Prior to ES6, we used libraries such as [Browserify](http://browserify.org/)
to create modules on the client-side, and [require](https://nodejs.org/api/modules.html#modules_module_require_id)
in **Node.js**. With ES6, we can now directly use modules of all types
(AMD and CommonJS).

--

### Exporting in CommonJS

```javascript
module.exports = 1;
module.exports = { foo: 'bar' };
module.exports = ['foo', 'bar'];
module.exports = function bar () {};
```

--

### Exporting in ES6

With ES6, we have various flavors of exporting. We can perform
**Named Exports**:

```javascript
export let name = 'David';
export let age  = 25;
```

--

As well as **exporting a list** of objects:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

export { sumTwo, sumThree };
```

--

We can also export functions, objects and values (etc.) simply by using the `export` keyword:

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b, c) {
    return a + b + c;
}
```

--

And lastly, we can **export default bindings**:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

let api = {
    sumTwo,
    sumThree
};

export default api;

/* Which is the same as
 * export { api as default };
 */
```

--

## Importing in ES6

ES6 provides us with various flavors of importing. We can import an entire file:

```javascript
import 'underscore';
```

> It is important to note that simply **importing an entire file will execute
all code at the top level of that file**.

--

Similar to Python, we have named imports:

```javascript
import { sumTwo, sumThree } from 'math/addition';
```

We can also rename the named imports:

```javascript
import {
    sumTwo as addTwoNumbers,
    sumThree as sumThreeNumbers
} from 'math/addition';
```

--

In addition, we can **import all the things** (also called namespace import):

```javascript
import * as util from 'math/addition';
```

--

Lastly, we can import a list of values from a module:

```javascript
import * as additionUtil from 'math/addition';
const { sumTwo, sumThree } = additionUtil;
```
Importing from the default binding like this:

```javascript
import api from 'math/addition';
// Same as: import { default as api } from 'math/addition';
```

--

When importing a module exported using commonjs syntax (such as React) we can do:

```javascript
import React from 'react';
const { Component, PropTypes } = React;
```

This can also be simplified further, using:

```javascript
import React, { Component, PropTypes } from 'react';
```

--

## Parameters

In ES5, we had varying ways to handle functions which needed **default values**,
**indefinite arguments**, and **named parameters**. With ES6, we can accomplish
all of this and more using more concise syntax.

--

### Default Parameters

```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

In ES6, we can simply supply default values for parameters in a function:

```javascript
function addTwoNumbers(x=0, y=0) {
    return x + y;
}
```

```javascript
addTwoNumbers(2, 4); // 6
addTwoNumbers(2); // 2
addTwoNumbers(); // 0
```

--

### Rest Parameters

In ES5, we handled an indefinite number of arguments like so:

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

Using the **rest** operator, we can pass in an indefinite amount of arguments:

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```

--

### Named Parameters

One of the patterns in ES5 to handle named parameters was to use the **options
object** pattern, adopted from jQuery.

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

We can achieve the same functionality using destructuring as a formal parameter
to a function:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        // Use variables height, width, lineStroke here
    }
```

If we want to make the entire value optional, we can do so by destructuring an
empty object:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        // ...
    }
```

--

## Spread Operator

In ES5, we could find the max of values in an array by using the `apply` method on `Math.max` like this:
```javascript
Math.max.apply(null, [-1, 100, 9001, -32]); // 9001
```

In ES6, we can now use the spread operator to pass an array of values to be used as
parameters to a function:

```javascript
Math.max(...[-1, 100, 9001, -32]); // 9001
```

--

We can concat array literals easily with this intuitive syntax:

```javascript
let cities = ['San Francisco', 'Los Angeles'];
let places = ['Miami', ...cities, 'Chicago']; // ['Miami', 'San Francisco', 'Los Angeles', 'Chicago']
```

--

## Promises

```javascript
// ES5
func1(function (value1) {
    func2(value1, function (value2) {
        func3(value2, function (value3) {
            func4(value3, function (value4) {
                func5(value4, function (value5) {
                    // Do something with value 5
                });
            });
        });
    });
});
```

```javascript
// ES6
func1(value1)
    .then(func2)
    .then(func3)
    .then(func4)
    .then(func5, value5 => {
        // Do something with value 5
    });
```
--

We can also **parallelize** Promises to handle an array of asynchronous
operations by using `Promise.all()`:

```javascript
let urls = [
  '/api/commits',
  '/api/issues/opened',
  '/api/pullrequests'
];

let promises = urls.map((url) => {
  return new Promise((resolve, reject) => {
    $.ajax({ url: url })
      .done((data) => {
        resolve(data);
      });
  });
});

Promise.all(promises)
  .then((results) => {
    // Do something with results of all our promises
 });
```

--

## What else?

### Fetch
Replacement for XHR. It uses promises which enables a simpler and cleaner API. However not available in all browsers...

```javascript
fetch('./api/some.json')
  .then(response => {
    console.log(response);
  });
```

--

### Ideas for a simple component

```javascript
import listItem from './listItem.js';

export default data => `
    <ul>
        ${data.map(listItem).join('')}
    </ul>
`;
```

```javascript
// listItem.js
export default item => `
    <li>${item.name}</li>
`;
```
