# Child Process
The child_process module provides the ability to spawn subprocesses
## [exec](https://nodejs.org/dist/latest-v14.x/docs/api/child_process.html#child_process_child_process_exec_command_options_callback)
Spawns a shell then executes the command within that shell, buffering any generated output. The command string passed to the exec function is processed directly by the shell and special characters (vary based on shell) need to be dealt with accordingly:

```javascript
const { execSync } = require('child_process')
const cmd = process.platform === 'win32' ? 'dir' : 'ls'
const output = execSync(cmd)
console.log(output.toString())
```
The execSync method returns a buffer containing the output of the command. This is both STDOUT and STDERR output.

If the subprocess exits with a non-zero exit code, the execSync function will throw:
```javascript
const { execSync } = require('child_process')

try {
  execSync(`${process.execPath} -e "throw Error('kaboom')"`)
} catch (err) {
  console.error('CAUGHT ERROR:', err)
}
```

Unlike execSync the asynchronous exec function splits the STDOUT and STDERR output by passing them as separate arguments to the callback function:
```javascript
const { exec } = require('child_process')

exec(
  `${process.execPath} -e "console.log('A');console.error('B')"`,
  (err, stdout, stderr) => {
    console.log('err', err)
    console.log('subprocess stdout: ', stdout.toString())
    console.log('subprocess stderr: ', stderr.toString())
  }
)
```

## [execFile](https://nodejs.org/dist/latest-v14.x/docs/api/child_process.html#child_process_child_process_execfile_file_args_options_callback)
The child_process.execFile() function is similar to child_process.exec() except that it does not spawn a shell by default. Rather, the specified executable file is spawned directly as a new process making it slightly more efficient than child_process.exec().

```javascript
const { execFile } = require('child_process');
const child = execFile('node', ['--version'], (error, stdout, stderr) => {
  if (error) {
    throw error;
  }
  console.log(stdout);
});
```

## [spawn](https://nodejs.org/dist/latest-v14.x/docs/api/child_process.html#child_process_child_process_spawn_command_args_options)
The child_process.spawn() method spawns a new process using the given command, with command-line arguments in args.

```javascript
const { spawnSync } = require('child_process')
const result = spawnSync(
  process.execPath,
  ['-e', `console.log('subprocess stdio output')`]
)
console.log(result)
```
![](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/jkvzim7bzhzi-pastedimage0.png)
While exec accepts a callback, spawn does not. Both exec and spawn return a ChildProcess instance however, which has stdin, stdout and stderr streams and inherits from EventEmitter allowing for exit code to be obtained after a close event is emitted. 

```javascript
const { spawn } = require('child_process')

const sp = spawn(
  process.execPath,
  [`-e`, `console.log('subprocess stdio output')`]
)

console.log('pid is', sp.pid)

sp.stdout.pipe(process.stdout)

sp.on('close', (status) => {
  console.log('exit status was', status)
})
```
The spawn method is the only method of the four that doesn't buffer child process output


## [fork](https://nodejs.org/dist/latest-v14.x/docs/api/child_process.html#child_process_child_process_fork_modulepath_args_options)
The fork method is a specialization of the spawn method. By default, it will spawn a new Node process of the currently executing JavaScript file (although a different JavaScript file to execute can be supplied). It also sets up Interprocess Communication (IPC) with the subprocess by default.


```javascript
// parent.js
const { fork } = require('child_process');

const forked = fork('child.js');

forked.on('message', (msg) => {
  console.log('Message from child', msg);
});

forked.send({ hello: 'world' });
```

```javascript
// child.js
process.on('message', (msg) => {
  console.log('Message from parent:', msg);
});

let counter = 0;

setInterval(() => {
  process.send({ counter: counter++ });
}, 1000);
```

## [Child STDIO](https://nodejs.org/dist/latest-v14.x/docs/api/child_process.html#child_process_options_stdio)

```javascript
const { spawn } = require('child_process')
const sp = spawn(
  process.execPath,
  [
   '-e',
   `console.error('err output'); process.stdin.pipe(process.stdout)`
  ],
  { stdio: ['pipe', 'pipe', 'pipe'] }
)

sp.stdout.pipe(process.stdout)
sp.stderr.pipe(process.stdout)
sp.stdin.write('this input will become output\n')
sp.stdin.end()
```
The options object has an stdio property set to ['pipe', 'pipe', 'pipe']. This is the default, but we've set it explicitly as a starting point. In this context pipe means expose a stream for a particular STDIO device.

We can send :
- `pipe`: Create a pipe between the child process and the parent process.

- `ignore`: Instructs Node.js to ignore the fd in the child.

- `inherit`: Pass through the corresponding stdio stream to/from the parent process. In the first three positions, this is equivalent to process.stdin, process.stdout, and process.stderr, respectively.

- `<Stream> object:` Share a readable or writable stream that refers to a tty, file, socket, or a pipe with the child process. The stream's underlying file descriptor is duplicated in the child process to the fd that corresponds to the index in the stdio array. The stream must have an underlying descriptor (file streams do not until the 'open' event has occurred).

```javascript
const { spawn } = require('child_process')
const sp = spawn(
  process.execPath,
  [
   '-e',
   `console.error('err output'); process.stdin.pipe(process.stdout)`
  ],
  { stdio: ['pipe', 'inherit', process.stdout] }
)

sp.stdin.write('this input will become output\n')
sp.stdin.end()
```