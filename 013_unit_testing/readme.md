# Assert
The assert module provides a set of assertion functions for verifying invariants.

The core assert module exports a function that will throw an AssertionError when the value passed to it is falsy.

## Methods
- assert.ok(val) – the same as assert(val)
- assert.equal(val1, val2) – coercive equal, val1 == val2
- assert.notEqual(val1, val2) – coercive unequal, val1 != val2
- assert.strictEqual(val1, val2) – strict equal, val1 === val2
- assert.notStrictEqual(val1, val2) – strict unequal, val1 !== val2
- assert.deepEqual(obj1, obj2) – coercive equal for all values in an object
- assert.notDeepEqual(obj1, obj2) – coercive unequal for all values in an object
- assert.deepStrictEqual(obj1, obj2) – strict equal for all values in an object
- assert.notDeepStrictEqual(val1, val2) – strict unequal for all values in an object
- assert.throws(function) – assert that a function throws
- assert.doesNotThrow(function) – assert that a function doesn't throw
- assert.rejects(promise|async function) – assert promise or returned promise rejects
- assert.doesNotReject(promise|async function) – assert promise or returned promise - resolves
- assert.ifError(err) – check that an error object is falsy
- assert.match(string, regex) – test a string against a regular expression
- assert.doesNotMatch(string, regex) – test that a string fails a regular expression
- assert.fail() – force an AssertionError to be thrown

## strict 
The assert module also exposes a strict object where namespaces for non-strict methods are strict, so the two options are the same:

```javascript
const assert = require('assert')
const add = require('./get-add-from-somewhere.js')
assert.strictEqual(add(2, 2), 4)

// or
assert.strict.equal(add(2, 2), 4)
```