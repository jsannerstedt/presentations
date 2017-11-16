title: es6 overview
author:
  name: Joel Sannerstedt
  url: https://github.com/jsannerstedt/presentations
output: es6.html
controls: false
style: ../main.css

--

# Part 1 - es6

--

## TOC

- Background
- es6 highlights 
- transpiling and bundling

--

## Background

- es5 in 2009
- es6 === es2015
- superset of es5

--

## What's new?

- features
- syntactic sugar 

--

## Highlights

--

### Block scoped var

Variables without hoisting

```javascript
if(foo){
  const bar = 'bar';
}
console.log(bar); // ReferenceError
```

```javascript
for (let i = 0; i < b.length; i++) {
    let y = b[i]
    …
}
```

--

### Arrow functions

```javascript
const names = models.map(model => model.name);
```
```javascript
const names2 = models.map(model => {
  if(!model.name){
    return 'name missing';
  }
  return model.name;
});
```
```javascript
//es5
var _this = this;
element.onClick(function() {_this.doStuff() });

//es6
element.onClick(() => this.doStuff());

```

--

### Object short hand

```javascript
const a = 1;
const b = 2;
console.log({a, b}); // {a: 1, b: 2}

const obj = {
  foo() {
    return 'foo';
  }
};
```

--

### Computed properties

```javascript
const foo = 'foo';
console.log({[foo]: 'bar'});
// {foo: bar}
```

--

### String templates

```javascript
const getUrl = params => 
  `${location.href}?foo=${params.bar}}`);
```

--

### Default parameters

```javascript
const doStuff = (foo='bar') => console.log(foo);
doStuff(); // 'bar'
```

--

### Spread
```javascript
const myComponent = props => 
  <MyOtherComponent {...props} className='foobar' />;
```
--

### Rest

```javascript
const doStuff = (foo, ...bar) => console.log(bar);
doStuff('foo', 'bar', 'foobar'); 
// ['bar', 'foobar']

const doMore = ({foo, ...bar}) => console.log(foo, bar);
doStuff({foo:'foo', bar:'bar', foobar: 'foobar'}); 
// 'foo', {bar: 'bar', foobar: 'foobar'}
```
```javascript
const myComponent = ({id, ...props}) => 
  <MyOtherComponent stuff={getStuff(id)}  {...props} />;
```

--

### Destructuring
```javascript
const [a, b] = [1,2,3];
console.log(a,b); // 1,2
```
```javascript
const {href} = window.location;
```
```javascript
const {host='localhost', search} = window.location;
```
```javascript
const doStuff = ({foo: bar}) => console.log(bar);
doStuff({foo: 'foo'});
```
--

### Promises

```javascript
fetch('/myapi')
  .then(response => response.json())
  .then(json => console.log(json));
```

```javascript
const logAfterTimeout = msg =>
  new Promise((resolve, reject)=>
    setTimeout(()=> resolve(msg), 500));

logAfterTimeout('hello')
  .then(msg => console.log(msg));
```
--

### Classes 

```javascript
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

---

### Modules

```javascript
import _ from 'lodash';

export default () => _.uniqueId();
```

--

## How to use it?

- evergreen browsers
- or transpilers

--

## Babel

- Transpiler.
- Can provide polyfills
- es next
--

## Browser list

.babelrc

```json
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions", "safari >= 7"]
      }
    }]
  ] 
}
```

--

# Part 2 - modules and architecture

-- 

## Definition 

> - **Definition**: how to encapsulate a piece of code into a useful unit, and how to register its capability/export a value for the module.
> - **Dependency References**: how to refer to other units of code.

// http://requirejs.org/docs/whyamd.html

--

### Named exports

several per module

```javascript
export const foo = 1;
export function bar (){}
```

```javascript
const foo = 'foo';
const bar = 'bar';

export {foo, bar};
```

--

### Default exports 

one per module

```javascript
export default () => 
<div>
  <h1>default exports</h1>
</div>;
```

--

## Imports 

- Must be top level. No condiationls or dynamic imports.
- imports are live, read only views of exports
```javascript
// adder
let val = 0;
const increase = () => val++;
export {val, increase}
```
```javascript
import {val, increase} from './adder';
console.log(val); // 0
increase();
console.log(val); // 1
```

--

### Default imports

```javascript
import foo from './bar';
import * as _ from 'lodash';
_.uniqueId();
```

--

### Named imports 

```javascript
import {foo, bar} from './foobar';
import {foo as f00} from './foo';
```

--

## Bundling

- + fewer files === less requests
- + Gzip is more efficient on larger files
- + tree shaking
- - compile time
- - complex build configs

--

## HTTP2 and modules

- + Cost/request decreases, no gain in downloading one vs multiple files
- + smaller incremental builds
- + more efficient cache
- - Gzip is better for larger files
- - tree shaking not possible

--

## Bundlers

- cat
- Browserify
- Rollup
- Webpack

--
