# Node JS

## Tooling

1. `npm` vs. `yarn`, NPM registry: `https://www.npmjs.com`
2. Static analysis: `ESLint`, Code formatter: `Prettier`,
   TSLint was deprecated.
3. Testing: `jest`, `testcontainers`
4. ..

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



## Folder structure
