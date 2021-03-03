# Module Variables

## __filename
The __filename variable holds the absolute path to the currently executing file.
```javascript
console.log('current filename', __filename)
```

## __dirname
the __dirname variable holds the absolute path to the directory that the currently executing file is in.
```javascript
console.log('current dirname', __dirname)
```

# Path Module

## join
The path.join() method joins all given path segments together using the platform-specific separator as a delimiter, then normalizes the resulting path.
```javascript
const { join } = require('path')
console.log('out file:', join(__dirname, 'out.txt'))
```

## isAbsolute
The path.isAbsolute() method determines if path is an absolute path.
```javascript
path.isAbsolute('/foo/bar'); // true
path.isAbsolute('/baz/..');  // true
path.isAbsolute('qux/');     // false
path.isAbsolute('.');        // false
```

## relative 
Given two absolute paths, calculates the relative path between them.
```javascript
path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb');
// Returns: '../../impl/bbb'
```

## path.resolve 
Accepts multiple string arguments representing paths. Conceptually each path represents navigation to that path. The path.resolve function returns a string of the path that would result from navigating to each of the directories in order using the command line cd command. 
```javascript
path.resolve('/foo/bar', './baz');
// Returns: '/foo/bar/baz'
```

## path.normalize 
Resolves .. and . dot in paths and strips extra slashes.
```javascript
path.normalize('/foo/../bar//baz');
// Returns: '/bar/baz'
```

## path.format 
Builds a string from an object. The object shape that `path.format` accepts, corresponds to the object returned from `path.parse`.
```javascript
path.format({
  root: '/',
  name: 'file',
  ext: '.txt'
});
// Returns: '/file.txt'
```

## path.parse 
Returns an object whose properties represent significant elements of the path
```javascript
path.parse('/home/user/dir/file.txt');
// Returns:
// { root: '/',
//   dir: '/home/user/dir',
//   base: 'file.txt',
//   ext: '.txt',
//   name: 'file' }
```

## path.extname 
Returns the extension of the path
```javascript
path.extname('index.html');
// Returns: '.html'
```

## path.dirname 
Returns the directory name of the path
```javascript
path.dirname('/foo/bar/baz/asdf/quux');
// Returns: '/foo/bar/baz/asdf'
```

## path.basename 
Returns the the last portion of the path
```javascript
path.basename('/foo/bar/baz/asdf/quux.html');
// Returns: 'quux.html'
```

# fs Module
## Reading and writing
The higher level methods for reading and writing are provided in four abstraction types:

- Synchronous
- Callback based
- Promise based
- Stream based

### Synchronous
```javascript
const { readFileSync } = require('fs')
const contentsBuffer = readFileSync(__filename) // return Buffer
// OR
const contents = readFileSync(__filename, {encoding: 'utf8'}) // return string

writeFileSync(join(__dirname, 'out.txt'), contents.toUpperCase())
writeFileSync(join(__dirname, 'out.txt'), contents.toUpperCase(), {
  flag: 'a'
}) // append
```
If there's a problem with an operation the *Sync APIs will throw. So to perform error handling they need to be wrapped in a try/catch.

### Callback based
```javascript
const { join } = require('path')
const { readFile, writeFile } = require('fs')
readFile(__filename, {encoding: 'utf8'}, (err, contents) => {
  if (err) {
    console.error(err)
    return
  }
  const out = join(__dirname, 'out.txt')
  writeFile(out, contents.toUpperCase(), (err) => {
    if (err) { console.error(err) }
  })
})
```

### Promise based
```javascript
const { join } = require('path')
const { readFile, writeFile } = require('fs').promises
async function run () {
  const contents = await readFile(__filename, {encoding: 'utf8'})
  const out = join(__dirname, 'out.txt')
  await writeFile(out, contents.toUpperCase())
}

run().catch(console.error)
```

### Stream based
```javascript
const { pipeline } = require('stream')
const { join } = require('path')
const { createReadStream, createWriteStream } = require('fs')

pipeline(
  createReadStream(__filename),
  createWriteStream(join(__dirname, 'out.txt')),
  (err) => {
    if (err) {
      console.error(err)
      return
    }
    console.log('finished writing')
  }
)
```

## Reading Directories
fs module provides multiple ways to read a directory:

- Synchronous
- Callback based
- Promise based
- An async iterable that inherits from fs.Dir

```javascript
const { readdirSync, readdir } = require('fs')
const { readdir: readdirProm } = require('fs').promises

try {
  console.log('sync', readdirSync(__dirname))
} catch (err) {
  console.error(err)
}

readdir(__dirname, (err, files) => {
  if (err) {
    console.error(err)
    return
  }
  console.log('callback', files)
})

async function run () {
  const files = await readdirProm(__dirname)
  console.log('promise', files)
}

run().catch((err) => {
  console.error(err)
})
```

## File Metadata
Metadata about files can be obtained with the following methods:

- fs.stat, fs.statSync, fs.promises.stat
- fs.lstat, fs.lstatSync, fs.promises.lstat

The only difference between the `stat` and `lstat` methods is that `stat `follows symbolic links, and `lstat` will get meta data for symbolic links instead of following them.

```javascript
const { readdirSync, statSync } = require('fs')

const files = readdirSync('.')

for (const name of files) {
  const stat = statSync(name)
  const typeLabel = stat.isDirectory() ? 'dir: ' : 'file: '
  const { atime, birthtime, ctime, mtime } = stat
  console.group(typeLabel, name)
  console.log('atime:', atime.toLocaleString())
  console.log('ctime:', ctime.toLocaleString())
  console.log('mtime:', mtime.toLocaleString())
  console.log('birthtime:', birthtime.toLocaleString())
  console.groupEnd()
  console.log()
}
```
There are four stats available for files:

- Access time
- Change time
- Modified time
- Birth time

## Watching

```javascript
const { watch } = require('fs')

watch('.', (evt, filename) => {
  console.log(evt, filename)
})
```
The listener callback gets two arguments (eventType, filename). eventType is either 'rename' or 'change', and filename is the name of the file which triggered the event.