# Promises
## States
- pending: initial state, neither fulfilled nor rejected.
- fulfilled: meaning that the operation was completed successfully.
- rejected: meaning that the operation failed.

![](https://mdn.mozillademos.org/files/15911/promises.png)

## Instance methods
### Promise.prototype.catch()
Appends a rejection handler callback to the promise, and returns a new promise resolving to the return value of the callback if it is called, or to its original fulfillment value if the promise is instead fulfilled.
### Promise.prototype.then()
Appends fulfillment and rejection handlers to the promise, and returns a new promise resolving to the return value of the called handler, or to its original settled value if the promise was not handled (i.e. if the relevant handler onFulfilled or onRejected is not a function).
### Promise.prototype.finally()
Appends a handler to the promise, and returns a new promise that is resolved when the original promise is resolved. The handler is called when the promise is settled, whether fulfilled or rejected.

## Chained Promises
The methods promise.then(), promise.catch(), and promise.finally() are used to associate further action with a promise that becomes settled. These methods also return a newly generated promise object, which can optionally be used for chaining.

```javascript
const myPromise = 
  (new Promise(myExecutorFunc))
  .then(handleFulfilledA,handleRejectedA)
  .then(handleFulfilledB,handleRejectedB)
  .then(handleFulfilledC,handleRejectedC);

// or, perhaps better ...

const myPromise =
  (new Promise(myExecutorFunc))
  .then(handleFulfilledA)
  .then(handleFulfilledB)
  .then(handleFulfilledC)
  .catch(handleRejectedAny);
```

## Creating a Promise
We can create a new Promise by initializing one with the Promise constructor
```javascript
const fs = require("fs");

const myPromise = new Promise((resolve, reject) => {
  fs.readFile("example.json", (err, data) => {
    if (err) {
      reject(err);
    } else {
      resolve(data);
    }
  });
});

myPromise.then(data => console.log(data)).catch(err => console.log(err));
```

## Static methods
### Promise.all(iterable)
Wait for all promises to be resolved, or for any to be rejected.
If the returned promise resolves, it is resolved with an aggregating array of the values from the resolved promises, in the same order as defined in the iterable of multiple promises.
If it rejects, it is rejected with the reason from the first promise in the iterable that was rejected.
### Promise.allSettled(iterable)
Wait until all promises have settled (each may resolve or reject).
Returns a Promise that resolves after all of the given promises have either resolved or rejected, with an array of objects that each describe the outcome of each promise.
### Promise.any(iterable)
Takes an iterable of Promise objects and, as soon as one of the promises in the iterable fulfills, returns a single promise that resolves with the value from that promise.
### Promise.race(iterable)
Wait until any of the promises is resolved or rejected.
If the returned promise resolves, it is resolved with the value of the first promise in the iterable that resolved.
If it rejects, it is rejected with the reason from the first promise that was rejected.
### Promise.reject(reason)
Returns a new Promise object that is rejected with the given reason.
### Promise.resolve(value)
Returns a new Promise object that is resolved with the given value. If the value is a thenable (i.e. has a then method), the returned promise will "follow" that thenable, adopting its eventual state; otherwise, the returned promise will be fulfilled with the value.
Generally, if you don't know if a value is a promise or not, Promise.resolve(value) it instead and work with the return value as a promise.

# Async/Await
The basic idea behind async/await is to write asynchronous code that looks and behaves similarly to synchronous code.
Async functions will implicitly return a Promise, which resolves with the return value from the function.

`await` can only be used inside functions marked with the `async` keyword.

```javascript
/////////// WITHOUT ASYNC

const fetch = require('node-fetch')

// with promises, 'Complete!' will be logged before the promise resolves
const data = fetch("url")
	.then(res => console.log(res.status))
console.log('Complete!')


/////////// WITH ASYNC

const fetch = require('node-fetch')

async function run() {
    // Will wait for promise to resolve before logging to console.
    const { status } = await fetch('https://jsonplaceholder.typicode.com/comments/1')
    console.log(status + ' - Complete!')
}
run()
```

## Error handling
Similarly to synchronous code, with async/await, errors are handled by wrapping await calls in a try...catch block, instead of using .catch handlers like you do with Promises.

```javascript
async function handleAsync() {
	try {
		const data = await fetch('url').then(res => res.json()
		console.log(data)
	} catch(error) {
		// If the fetch call has any errors, or if the Promise it
		// returns is rejected, an error is thrown and we can process it
		// here.
		console.log('Uh oh!', error)
	}
}

handleAsync()
```

Because an async function returns a Promise implicitly, we can also attach a catch handler to the async function call itself:

```javascript
// Another option: handle the exception outside the async function.
async function catchAsync() {
	const data = await fetch('url').then(res => res.json()
	console.log(data)
}

catchAsync.catch(error => console.log('Uh oh!', error))
```