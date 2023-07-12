# Node JS

Node.js - **a JavaScript Runtime**, makes JavaScript accessible on the server.
Node.js uses **V8 engine**, that runs JavaScript in the browser.
**[V8 engine](#v8)**, written C++, compiles JavaScript code to Machine code.
Node.js adds extensions to the V8 engine.

Backend applications on Node.js don't require
extra tools for serving, like Apache, Tomcat, etc.

**Instead, they are running the server by listening to the socket
and executing the application code**.

https://nodejs.org/en

Node.js offers an interactive, [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) mode:
```text
node
> <node commands>
```

## Tooling

1. `npm` vs. `yarn`, NPM registry: `https://www.npmjs.com`
2. Static analysis: `ESLint`, Code formatter: `Prettier`,
   TSLint was deprecated.
3. Testing: `jest`, `testcontainers`
4. Automation: `Grunt`
5. ..

## V8

Node.js uses V8 engine.

V8 provides **Memory Heap** and **Call Stack**.

**Heap requires only an array**:
```text
left = 2 * i + 1
right = 2 * i + 2
```
V8 engine **since version 5.9** uses Ignition(without optimization) and TurboFan (optimizations).

Some other engines:
1. Rhino - Mozilla, Firefox.
2. Chakra - Microsoft Edge.
3. JerryScript for IoT.

## NVM

Node Version Manager (NVM) is a tool used to install,
manage and upgrade Node.js versions.
It allows the user to maintain multiple versions of Node.js
on the system and change the active version depending on their current needs.

## NPM

Node Package Manager (NPM) is a software registry for JavaScript packages.
It has its own CLI: `npm`.

### Versioning

Version number stored in the `package.json` file:
```json
{
   "name": "package-name",
   "version": "MAJOR.MINOR.PATCH",
   ...
}
```

`~version` - **locked MINOR version**:
will update you to all future patch versions, without incrementing the MINOR version,
e.g., `0.1.2` > `0.1.3`.
<br>
`^version` (^ - caret) - **locked MAJOR version**:
will update you to all future minor/patch versions, without incrementing the MAJOR one.
<br>
`>version` - **must be greater than `version`**.
<br>
`>=version` - **must be greater or equal than `version`**.
<br>
`latest` - **latest version**.
<br>
`*` - **any version**.

[semver.org](https://semver.org/)
<br>
[0ver.org](https://0ver.org/)

### Modules

### Folder structure

```text
src/*
test/*
package.json
package-lock.json
```

`package-lock.json`is automatically generated for any operations where npm modifies
either the `node_modules tree`, or `package.json`.
This file is intended to be committed into source repositories to describe
a single representation of a dependency tree such that teammates, deployments,
and continuous integration are guaranteed to install exactly the same dependencies.

## JavaScript

Weakly-Typed, just-in-time compiled, Object-Oriented Language.
Language of the modern web.
Runs in browser and on a server.

In JavaScript, it's possible to add fields and functions in runtime:
```javascript
function Point(x, y) {
   this.x = x;
   this.y = y;
}

const p = new Point(1, 2);
let b = p.b; // will be added in runtime
```

For idempotent functions, JavaScript caches the result.

Summary:
1. Initialize object properties in the same order
2. Don't use Dynamic properties
3. Don't use Sparse Arrays

### Data types

#### Numbers

Integers, Float-point numbers

#### Strings

A collection of one or more characters between two single quotes, double quotes, or backticks:
```javascript
'a'
"a"
`a`
```

#### Booleans

A boolean value is either `True` of `False`.
```javascript
true
false
```

#### Undefined

If value is not assigned to a variable, the value of the variable is `undefined`.
**Also, if function is not returning anything, the result will be `undefined`**.
```javascript
let fistname;
console.log(fistname) // undefined
```

#### NULL

NULL in JavaScript means just an empty value.
```javascript
let user = null;
```

#### Type Checking

To check the data type of certain variable, we use the `typeof` operator:
```javascript
console.log(typeof 'a') // string
console.log(typeof 5) // number
console.log(typeof true) // boolean
console.log(typeof null) // object
console.log(typeof undefined) // undefined
```

### Declarations

`var` declares a variable, optionally initializing a value:
```javascript
var num;
```

`let` declares a block-scoped, local variable, optionally initializing a value:
```javascript
let num;
```

`const` declares a block-scoped, read-only named constant,
similar to `final` in Java:
```javascript
const num = 1;
const another; // SyntaxError: Missing initializer in const declaration
```

#### Variable Hoisting

Hoisting in JavaScript is a behavior in which a function or a variable can be used before declaration:
```javascript
console.log(test);
var test;
```

In terms of variables and constants, keyword `var` is hoisted and
**`let` and `const` do not allow hoisting**.
**While using let, the variable must be declared first**.

#### Function Hoisting

A function can be called before declaring it:
```javascript
greet();

function greet() {
    console.log('Hi, there.');
}

// the output will be: Hi, there.
```
**However, when a function is used as an expression,
an error occurs because only declarations are hoisted**:

```javascript
greet();

let greet = function() {
    console.log('Hi, there.');
}

// the output will be Uncaught ReferenceError: greet is not defined
```

In case of `var` usage it will give the following output:
```text
Uncaught TypeError: greet is not a function
```

## Express.js

## TypeScript
