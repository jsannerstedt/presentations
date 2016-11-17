title: Full frontal
author:
  name: Joel Sannerstedt
  url: http://jsannerstedt.github.io/
output: modules.html
controls: false
style: ../main.css

--

# Modules & Bundling

--

## TOC
- Definition
- History
- Amd, common js, es2015
- Bundling
- Recommended approach
- Future

--

## What are JavaScript modules?

> - **Definition**: how to encapsulate a piece of code into a useful unit, and how to register its capability/export a value for the module.
> - **Dependency References**: how to refer to other units of code.

// http://requirejs.org/docs/whyamd.html

--

## Todo MVC
http://todomvc.com/

--

## Before
Module pattern

pros:
- no tooling - get started right away
- wysiwyg - no external code included

cons: 
- dependency management - order is important
- ceremony to create new module
- you probably need to add bundling later anyway

--

## Asynchronous Module Definition (AMD)

require js

pros: 
- no tooling, get started right away
- async loading of dependencies

cons:
- complicated bundling
- verbose syntax

--

```javascript
// dep1.js
define([], function() {
  var x = 1;  
  return {x: x};
});

```
    
    
```javascript
// app.js
define(['./dep1', './dep2'], function(dep1, dep2) {
    console.log(dep1, dep2);
});

```

--

## Common JS
Node, npm

pros:
- npm
- shared syntax client/server

cons: 
- needs tooling
- large builds 'needs' code splitting

--

## Es2015 (import/export)
pros:
- common syntax
- native

cons: 
- not implemented yet
- needs tooling

--

## Bundling
- cat
- Browserify
- R.js
- Webpack

--

## Recommended approach
Npm scripts + es2015 modules + browserify + babelify
Babel turns es2015 into commonJS, so browserify can bundle

--

## Future?
Rollup

--

## NB Donations are welcome ;)
