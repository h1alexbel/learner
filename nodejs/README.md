# Node JS

Node.js - a JavaScript Runtime, makes JavaScript accessible on the server.
Node.js uses **V8 engine**, that runs JavaScript in the browser.
**V8 engine**, written C++, compiles JavaScript code to Machine code.
Node.js adds extensions to the V8 engine.

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

## Express.js

## TypeScript
