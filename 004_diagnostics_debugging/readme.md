# Debugging Node.js

## V8 Inspector
V8 Inspector integration allows attaching Chrome DevTools to Node.js instances for debugging and profiling. It uses the Chrome DevTools Protocol.

V8 Inspector can be enabled by passing the --inspect flag when starting a Node.js application. It is also possible to supply a custom port with that flag, e.g. --inspect=9222 will accept DevTools connections on port 9222.
```
$ node --inspect index.js
Debugger listening on ws://127.0.0.1:9229/dc9010dd-f8b8-4ac5-a510-c1a114ec7d29
For help, see: https://nodejs.org/en/docs/inspector
```
To break on the first line of the application code, pass the --inspect-brk flag instead of --inspect.
```
$ node --inspect-brk app.js
```
In order to begin debugging the process, the next step is to set a Chrome browser tab's address bar to `chrome://inspect`.

## Debugger command
The debugger statement can be used to explicitly pause on the line that the statement appears when debugging.
```javascript
function f (n = 99) {
  if (n === 0) throw Error()
  debugger
  f(n - 1)
}
f()
```

## Profile
The following methods are exposed by the V8 engine in the general API but do not display anything unless used in conjunction with the inspector (--inspect flag).

### [console.profile](https://nodejs.org/dist/latest-v12.x/docs/api/console.html#console_console_profile_label)
The console.profile() method starts a JavaScript CPU profile with an optional label until console.profileEnd() is called. The profile is then added to the Profile panel of the inspector.

```javascript
console.profile('MyLabel');
// Some code
console.profileEnd('MyLabel');
// Adds the profile 'MyLabel' to the Profiles panel of the inspector.
```

### [console.profileEnd](https://nodejs.org/dist/latest-v12.x/docs/api/console.html#console_console_profileend_label)
Stops the current JavaScript CPU profiling session if one has been started and prints the report to the Profiles panel of the inspector.
If this method is called without a label, the most recently started profile is stopped.

### [console.timeStamp](https://nodejs.org/dist/latest-v12.x/docs/api/console.html#console_console_timestamp_label)
This method does not display anything unless used in the inspector. The console.timeStamp() method adds an event with the label 'label' to the Timeline panel of the inspector.