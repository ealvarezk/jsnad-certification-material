# Loading a Module
The `require` function is passed a package's namespace, looks for a directory with that name in the node_modules folder and returns the exported value from the main file of that package.
```javascript
const pino = require('pino')
const logger = pino()
logger.info('my-package started')
```
Used to import modules, JSON, and local files. Modules can be imported from node_modules. Local modules and JSON files can be imported using a relative path (e.g. ./, ./foo, ./bar/baz, ../foo) that will be resolved against the directory named by __dirname (if defined) or the current working directory. 

# Creating a Module
The require function will return whatever is exported from a module.
```javascript
/* format.js */
const upper = (str) => {
  if (typeof str === 'symbol') str = str.toString()
  str += ''
  return str.toUpperCase()
}

module.exports = { upper: upper }
// or
exports.upper = upper


//////////////////////////////////
/* index.js */
const pino = require('pino')
const format = require('./format')
const logger = pino()
logger.info(format.upper('my-package started'))
```
Used to import modules, JSON, and local files. Modules can be imported from node_modules. Local modules and JSON files can be imported using a relative path (e.g. ./, ./foo, ./bar/baz, ../foo) that will be resolved against the directory named by __dirname (if defined) or the current working directory. 


# Detecting Main Module
We can check if module.parent is null or we can check if require.main is the module object.

```javascript
const format = require('./format')

if (require.main === module) {
  const pino = require('pino')
  const logger = pino()
  logger.info(format.upper('my-package started'))
  process.stdin.resume()
} else {
  const reverseAndUpper = (str) => {
    return format.upper(str).split('').reverse().join('')
  }
  module.exports = reverseAndUpper
}
```

# The module wrapper
Before a module's code is executed, Node.js will wrap it with a function wrapper that looks like the following:
```javascript
(function(exports, require, module, __filename, __dirname) {
// Module code actually lives in here
});
```