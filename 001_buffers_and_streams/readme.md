# Buffers
In Node.js binary data is handled with the Buffer constructor. The Buffer constructor is a global, so there's no need to require any core module in order to use the Node core Buffer API.
## Allocating Buffers
```js
const buffer = Buffer.alloc(10)
```
`Buffer.alloc(size[, fill[, encoding]])`

By default the Buffer.alloc function produces a zero-filled buffer.
There is an unsafe way:
```js
const buffer = Buffer.allocUnsafe(10)
```
## Converting x to Buffers
```js
Buffer.from(x)
```
x could be `array`, `arrayBuffer`, `buffer` or `object`

## Converting Buffers to String
```js
buffer.toString()
```
`buf.toString([encoding[, start[, end]]])`

encoding could be `utf-8` (default), `hex`, `base64`, `utf16le`, `latin1`, and others legacy

## JSON Serializing and Deserializing Buffers
```js
const buffer = Buffer.from('👀')
const json = JSON.stringify(buffer)
const parsed = JSON.parse(json)
console.log(parsed) // prints { type: 'Buffer', data: [ 240, 159, 145, 128 ] }
console.log(Buffer.from(parsed.data)) // prints <Buffer f0 9f 91 80>
```
So Buffer instances are represented in JSON by an object that has a `type` property with a string value of `Buffer` and a `data` property with an array of numbers, representing the value of each byte in the buffer.

When deserializing, JSON.parse will only turn that JSON representation of the buffer into a plain JavaScript object, to turn it into an object the `data` array must be passed to Buffer.from

# Streams
## Types
-   [`Readable`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_readable_streams): A source of input. You receive data from a  `Readable`  stream.
-   [`Writable`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_writable_streams): A destination. You stream data  _to_  a  `Writable`  stream. Sometimes referred to as a “[sink](https://developer.mozilla.org/en-US/docs/Web/API/WritableStream)”, because it is the end-destination for streaming data.
-   [`Duplex`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_class_stream_duplex): A stream that implements both a  `Readable`  and  `Writable`  interface.  `Duplex`  streams can receive data, as well as produce data. An example would be a TCP socket, where data flows in both directions.
-   [`Transform`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_class_stream_transform): A type of  `Duplex`  stream, where the data passing through the stream is altered. The output will be different from the input in some way. You can send data to a  `Transform`  stream, and also read data from it after the data has been transformed.
-   [`PassThrough`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_class_stream_passthrough): A special type of  `Transform`  stream, which does not alter the data passing through it. According to the documentation, these are mostly used for testing and examples; they serve little actual purpose beyond passing data along.

## Events
* All streams inherits from [`EventEmitter`](https://nodejs.org/docs/latest-v14.x/api/events.html#events_events)
* Working with stream events directly is useful when you want finer grained control over how a stream is consumed.

### `Readable`  stream events

-   [`data`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_data): Emitted when the stream outputs a chunk of data. The data chunk is passed to the handler.
-   [`readable`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_readable): Emitted when there is data ready to be read from the stream.
-   [`end`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_end): Emitted when no more data is available for consumption.
-   [`error`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_error_1): An error has occurred within the stream, and an error object is passed to the handler. Unhandled stream errors can crash your program.
-   [`close`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_close_1): The 'close' event is emitted when the stream and any of its underlying resources (a file descriptor, for example) have been closed. The event indicates that no more events will be emitted, and no further computation will occur.

### Writable stream events

-   [`drain`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_drain): The writable stream's internal buffer has been cleared and is ready to have more data written into it.
-   [`finish`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_finish): All data has been written to the underlying system.
-   [`error`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_error): An error occurred while writing data, and an error object is passed to the handler. Unhandled stream errors can crash your program.
-   [`close`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_event_close_1): The 'close' event is emitted when the stream and any of its underlying resources (a file descriptor, for example) have been closed. The event indicates that no more events will be emitted, and no further computation will occur.

![](https://cdn-media-1.freecodecamp.org/images/1*HGXpeiF5-hJrOk_8tT2jFA.png)
## Important methods
* To connect streams together and start the flow of data, we use the [`pipe`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_readable_pipe_destination_options) method available on readable streams.
* The   [`pipeline`](https://nodejs.org/docs/latest-v14.x/api/stream.html#stream_stream_pipeline_streams_callback)  method takes any number of streams as arguments, and a callback function as its last argument. If an error occurs anywhere in the pipeline, the pipeline will end and the callback will be invoked with the error that occurred. The callback function is also invoked when the pipeline successfully ends, providing us a way to know when the pipeline has completed.
## [Object mode](https://nodejs.org/dist/latest-v14.x/docs/api/stream.html#stream_object_mode)

All streams created by Node.js APIs operate exclusively on strings and  `Buffer`  (or  `Uint8Array`) objects. It is possible, however, for stream implementations to work with other types of JavaScript values. Stream instances are switched into object mode using the  `objectMode`  option when the stream is created. 

## Buffers

Both [`Writable`](https://nodejs.org/dist/latest-v14.x/docs/api/stream.html#stream_class_stream_writable) and [`Readable`](https://nodejs.org/dist/latest-v14.x/docs/api/stream.html#stream_class_stream_readable) streams will store data in an internal buffer that can be retrieved using `writable.writableBuffer` or `readable.readableBuffer`, respectively.
Data is buffered in `Readable` streams when the implementation calls [`stream.push(chunk)`](https://nodejs.org/dist/latest-v14.x/docs/api/stream.html#stream_readable_push_chunk_encoding)
Data is buffered in `Writable` streams when the [`writable.write(chunk)`](https://nodejs.org/dist/latest-v14.x/docs/api/stream.html#stream_writable_write_chunk_encoding_callback) method is called repeatedly.

## Writable streams
```js
const { Writable } = require('stream');

const data = []

const writable = new Writable({
  decodeStrings: false, // allow to set that always decode strings
  objectMode: true, // allow to use abny kind og javascript object
  write (chunk, enc, next) {
    if (chunk.toString().indexOf('a') >= 0) {
      next(new Error('chunk is invalid'));
    } else {
      data.push(chunk)
      next()
    }
  }
});

writable.on('finish', () => { console.log('finished writing', data) })
writable.write('A\n')
writable.write(1)
writable.write('C\n')
writable.end('nothing more to write')
```

## Readable streams
```js
const { Readable } = require('stream');

const data = ['some', 'data', 'to', 'read'];

const readable = new Readable({
  encoding: 'utf8', // allow to set encoding if it is a string
  objectMode: true, // allow to not use Buffer 
  read(size) {
    if (data.length === 0) this.push(null)
    else this.push(data.shift())
  }
});

readable.on('data', (data) => { console.log('got data', data) })
readable.on('end', () => { console.log('finished reading') })
```

Simple option:  [`stream.Readable.from(iterable, [options])`](https://nodejs.org/dist/latest-v14.x/docs/api/stream.html#stream_stream_readable_from_iterable_options)
```js
const { Readable } = require('stream')
const readable = Readable.from(['some', 'data', 'to', 'read'])
readable.on('data', (data) => { console.log('got data', data) })
readable.on('end', () => { console.log('finished reading') })
```
## Duplex streams
```js
const { Duplex } = require('stream');

const myDuplex = new Duplex({
  read(size) {
    // ...
  },
  write(chunk, encoding, callback) {
    // ...
  }
});
```

## Transform streams
```javascript
const { Transform, pipeline } = require("stream");

const upperCaseTransform = new Transform({
  transform: function(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  }
});

pipeline(process.stdin, upperCaseTransform, process.stdout, err => {
  if (err) {
    console.log("Pipeline encountered an error:", err);
  } else {
    console.log("Pipeline ended");
  }
});
```
## Finshed streams
A function to get notified when a stream is no longer readable, writable or has experienced an error or a premature close event.

```javascript
const { finished } = require('stream');

const rs = fs.createReadStream('archive.tar');

finished(rs, (err) => {
  if (err) {
    console.error('Stream failed.', err);
  } else {
    console.log('Stream is done reading.');
  }
});

rs.resume(); // Drain the stream.
```
## Pipe, piping
The [pipeline](https://nodejs.org/dist/latest-v14.x/docs/api/stream.html#stream_stream_pipeline_source_transforms_destination_callback) command is a module method to pipe between streams and generators forwarding errors and properly cleaning up and provide a callback when the pipeline is complete.

```javascript
const { PassThrough, pipeline } = require("stream");

const pass = new PassThrough();

pipeline(process.stdin, pass, process.stdout, err => {
  if (err) {
    console.log("Pipeline encountered an error:", err);
  } else {
    console.log("Pipeline ended");
  }
});

///// or
process.stdin.pipe(pass).pipe(process.stdout)
```