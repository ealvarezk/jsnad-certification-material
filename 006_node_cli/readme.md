# Node CLI
Run `man node` in your CLI to get a complete list of available options.

## Options
### --version or -v
Using the node --version, or short, node -v, you can print the version of Node.js you are using.

### --eval or -e
Using the --eval option, you can run JavaScript code right from your terminal. The modules which are predefined in REPL can also be used without requiring them, like the http or the fs module.

### --print or -p
The --print option works the same way as the --eval, however it prints the result of the expression. To achieve the same output as the previous example, we can simply leave the console.log:

### --check or -c
The --check option instructs Node.js to check the syntax of the provided file, without actually executing it.

### -r, --require module
Preload the specified module at startup.
```node -r ./preload.js app.js```

### --inspect[=host:port]
Using node --inspect will activate the inspector on the provided host and port. If they are not provided, the default is 127.0.0.1:9229. 

### --inspect-brk[=host:port]
The --inspect-brk has the same functionality as the --inspect option, however it pauses the execution at the first line of the user script.

### --zero-fill-buffers
Node.js can be started using the --zero-fill-buffers command line option to force all newly allocated Buffer instances to be automatically zero-filled upon creation. 

### --prof-process
Using the --prof-process, the Node.js process will output the v8 profiler output.

To use it, first you have to run your applications using:

node --prof index.js
Once you ran it, a new file will be placed in your working directory, with the isolate- prefix.

Then, you have to run the Node.js process with the --prof-process option:

node --prof-process isolate-0x102001600-v8.log > output.txt

## V8 Options
You can print all the available V8 options using the `--v8-options` command line option.

### --harmony
With the harmony flag, you can enable all completed harmony features.

### --max_old_space_size
With this option, you can set the maximum size of the old space on the heap, which directly affects how much memory your process can allocate.

This setting can come handy when you run in low memory environments.

### --optimize_for_size
With this option, you can instruct V8 to optimize the memory space for size - even if the application gets slower.

Just as the previous option, it can be useful in low memory environments.

### --stack-trace-limit=x
Allows to change the stack trace limit .

## Environment Variables
### NODE_DEBUG=module[,…]
Setting this environment variable enables the core modules to print debug information. You can run the previous example like this to get debug information on the module core component (instead of module, you can go for http, fs, etc...)
### NODE_PATH=path
Using this setting, you can add extra paths for the Node.js process to search modules in.

### OPENSSL_CONF=file
Using this environment variable, you can load an OpenSSL configuration file on startup.

[For a full list of supported environment variables](https://nodejs.org/dist/latest-v14.x/docs/api/cli.html#cli_environment_variables)

