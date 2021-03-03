# npm
The `npm` command is CLI tool that acts as a package manager for Node.js. By default it points to the npmjs.com registry, which is the default module registry.

## Initializing a Package
A package is a folder with a `package.json` file in it.

The `npm init` command can be used to quickly create a `package.json`, it will start a CLI wizard that will ask some questions. To accept the default value for every question is to use the `-y` flag.

## `package.json` some fields
- name – the name of the package
- version – the current version number of the package
- description – a package description, this is used for meta analysis in package registries
- main – the entry-point file to load when the package is loaded
- scripts – namespaced shell scripts, these will be discussed later in this section
- keywords – array of keywords, improves discoverability of a published package
- author – the package author
- contributors - is like author but accept an array
- license – the package license.
- repository -  repository of the code

## Installing Dependencies
```
npm install pino
```
Once the dependency is installed the package.json file will have the package in dependencies.
```
npm install
```
Will install all the dependencies in package.json.

The `npm ls` command can be used to describe the dependency tree of a package

### Development Dependencies
An important characteristic of development dependencies is that only top level development dependencies are installed. The development dependencies of sub-dependencies will not be installed.

To install a package as dev dependecie:
```
npm install --save-dev standard

```
Once the dependency is installed the package.json file will have the package in devDependencies.

A `--production` flag can be used with `npm install` so that development dependencies are ignored

## Scripts
The "scripts" field in `package.json` can be used to define aliases for shell commands that are relevant to a Node.js project.

```json
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "lint": "standard"
},
```
Packages can assign a "bin" field in their package.json, which will associate a namespace with a Node program script within that package.

To execute the script use **npm run**:
```
npm run lint
```
There are two package scripts namespaces that have dedicated npm commands: `npm test` and `npm start`.

