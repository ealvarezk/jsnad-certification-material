# EventEmitter
The `EventEmitter` constructor in the events module is the functional backbone of many Node core API's. For instance, HTTP and TCP servers are an event emitter, a TCP socket is an event emitter, HTTP request and response objects are event emitters.

## Creating an Event Emitter
To create a new event emitter, call the constructor with new
```javascript
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();
```

## Emitting Events
To emit an event call the emit method. The first argument passed to emit is the event namespace. In order to listen to an event this namespace has to be known. The subsequent arguments will be passed to the listener.
```javascript
const myEmitter = new MyEmitter();
myEmitter.on('event', function(x, y) {
  console.log(x, y, this, this === myEmitter);
  // Prints:
  //   a b MyEmitter {
  //     domain: null,
  //     _events: { event: [Function] },
  //     _eventsCount: 1,
  //     _maxListeners: undefined } true
});
myEmitter.emit('event', 'a', 'b');
```

## Listening for Events
To add a listener to an event emitter the addListener method or it's alias on method is used:
```javascript
const { EventEmitter } = require('events')

const ee = new EventEmitter()

ee.on('add', (a, b) => { console.log(a + b) })
// or 
ee.addListener('add', (a, b) => { console.log(a + b) })

ee.emit('add', 7, 6)
```

The prependListener method can be used to inject listeners into the top position:

```javascript
const { EventEmitter } = require('events')
const ee = new EventEmitter()
ee.on('my-event', () => { console.log('2nd') })
ee.prependListener('my-event', () => { console.log('1st') })
ee.emit('my-event')
```

The once method will immediately remove its listener after it has been called:

```javascript
const { EventEmitter } = require('events')
const ee = new EventEmitter()
ee.once('my-event', () => { console.log('my-event fired') }) // only called once
ee.emit('my-event')
ee.emit('my-event')
ee.emit('my-event')
```

## Removing Listeners
The `removeListener` method can be used to remove a previously registered listener.

The `removeListener` method takes two arguments, the event name and the listener function.
```javascript
const { EventEmitter } = require('events')
const ee = new EventEmitter()

const listener1 = () => { console.log('listener 1') }
const listener2 = () => { console.log('listener 2') }

ee.on('my-event', listener1)
ee.on('my-event', listener2)

setInterval(() => {
  ee.emit('my-event')
}, 200)

setTimeout(() => {
  ee.removeListener('my-event', listener1)
}, 500)

setTimeout(() => {
  ee.removeListener('my-event', listener2)
}, 1100)
```

The `removeAllListeners` method can be used to remove listeners without having a reference to their function. It can take either no arguments in which case every listener on an event emitter object will be removed, or it can take an event name in order to remove all listeners for a given event.

```javascript
const { EventEmitter } = require('events')
const ee = new EventEmitter()

const listener1 = () => { console.log('listener 1') }
const listener2 = () => { console.log('listener 2') }

ee.on('my-event', listener1)
ee.on('my-event', listener2)
ee.on('another-event', () => { console.log('another event') })

setInterval(() => {
  ee.emit('my-event')
  ee.emit('another-event')
}, 200)

setTimeout(() => {
  ee.removeAllListeners('my-event')
}, 500)

setTimeout(() => {
  ee.removeAllListeners()
}, 1100)
```

## The error Event
Emitting an `error` event on an event emitter will cause the event emitter to throw an exception if a listener for the 'error' event has not been registered.

```javascript
const { EventEmitter } = require('events')
const ee = new EventEmitter()

process.stdin.resume() // keep process alive

ee.on('error', (err) => { // if not this it crash
  console.log('got error:', err.message )
})

ee.emit('error', new Error('oh oh'))
```