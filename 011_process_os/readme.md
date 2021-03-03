# Process
A Node.js process is the program that is currently running our code. We can control and gather information about a process using the global `process` object.

The process object is an instance of EventEmitter.

## STDIO
The process object exposes three streams:
- *process.stdin*: A Readable stream for process input.
- *process.stdout*: A Writable stream for process output.
- *process.stderr*: A Writable stream for process error output.

```bash
node -p "crypto.randomBytes(100).toString('hex')" | node example.js
```
```javascript
// example.js
process.stdin.pipe(process.stdout)
```

The process.stdin, process.stdout and process.stderr streams are unique in that they never finish, error or close. That is to say, if one of these streams were to end it would either cause the process to crash or it would end because the process exited.

To check if a stream is connected to a TTY (text terminal) context, check the isTTY property.

```bash
$ node -p "Boolean(process.stdin.isTTY)"
true
$ echo "foo" | node -p "Boolean(process.stdin.isTTY)"
false
$ node -p "Boolean(process.stdout.isTTY)"
true
$ node -p "Boolean(process.stdout.isTTY)" | cat
false
```

## Exiting
To force a process to exit at any point we can call `process.exit`.

Any non-zero code indicates failure, and to indicate general failure we can use an exit code of 1.

```javascript
setInterval(() => {
  console.log('this interval is keeping the process open')
}, 500)
setTimeout(() => {
  console.log('exit after this')
  process.exit() // or process.exit(0)
  // or
  process.exit(1) // exits whith 
}, 1750)
```

The exit code can also be set independently be assigning process.exitCode:

The 'exit' event can also used to detect when a process is closing and perform any final actions.
```javascript
setInterval(() => {
  console.log('this interval is keeping the process open')
  process.exitCode = 1
}, 500)
setTimeout(() => {
  console.log('exit after this')
  process.exit()
}, 1750)

process.on('exit', (code) => {
  console.log('exiting with code', code)
  setTimeout(() => {
    console.log('this will never happen')
  }, 1)
})
```

## Other information
- `process.cwd()`: Current Directory
- `process.platform`: Process Platform
- `process.pid`: Process ID
- `process.chdir(directory)`: Changes the current working directory
- `process.uptime()`: Process Uptime in seconds
- `process.cpuUsage()`: return `{ user, system }`. The user property represents time that the Node process spent using the CPU. The system property represents time that the kernel spent using the CPU due to activity triggered by the process. Both are in microsecond.
- `process.memoryUsage()`: return `{ rss, heapTotal, heapUsed, external }`. All of the numbers are in bytes. The `external` metric refers to memory usage by the C layer,. The `heapTotal` metric refers to the total memory allocated for a process. Process memory can be split across RAM and swap space. So the `rss` metric, which stands for Resident Set Size is the amount of used RAM for the process, whereas the `heapUsed` metric is the total amount of memory used across both RAM and swap space.



## Environment variables
The process.env property returns an object containing the user environment.
```bash
$ node -p process.env
{
  TERM: 'xterm-256color',
  SHELL: '/usr/local/bin/bash',
  USER: 'maciej',
  PATH: '~/.bin/:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin',
  PWD: '/Users/maciej',
  EDITOR: 'vim',
  SHLVL: '1',
  HOME: '/Users/maciej',
  LOGNAME: 'maciej',
  _: '/usr/local/bin/node'
}
```
It is possible to modify this object, but such modifications will not be reflected outside the Node.js process.


# OS
The os module provides operating system-related utility methods and properties.

```javascript
const os = require('os')

console.log('Hostname', os.hostname())
console.log('Home dir', os.homedir())
console.log('Temp dir', os.tmpdir())
console.log('platform', os.platform())
console.log('type', os.type())
console.log('system uptime', os.uptime())
console.log('freemem', os.freemem())
console.log('totalmem', os.totalmem())
```