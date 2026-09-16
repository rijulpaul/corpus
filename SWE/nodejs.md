# Important Node Modules And Their Functioning

## Topics:
1. [File System](#file-system)
2. [HTTP and HTTPS](#http-and-https)
3. [Streams](#streams)
4. [Path](#path)
5. [Timers](#timers)
6. [URL](#url)
7. [Net](#net)
8. [Zlib](#zlib)
9. [Cluster](#cluster)
10. [Worker Threads](#worker-threads)
11. [Child Process](#child-process)
12. [Assert](#assert)
13. [Test](#test)

## File System
The fs module provides both asynchronous (non-blocking) and synchronous (blocking) methods for file system operations.

### File Operations
- **fs.readFile()** / **fs.readFileSync()** – Read the contents of a file.
- **fs.writeFile()** / **fs.writeFileSync()** – Write data to a file (overwrites by default).
- **fs.appendFile()** / **fs.appendFileSync()** – Append data to the end of a file.
- **fs.unlink()** / **fs.unlinkSync()** – Delete a file.

### Directory Operations
- **fs.mkdir()** / **fs.mkdirSync()** – Create a new directory.
- **fs.readdir()** / **fs.readdirSync()** – Read contents of a directory.
- **fs.rmdir()** / **fs.rmdirSync()** – Remove a directory.
- **fs.rm()** – Remove files/directories recursively (modern and safer).

### File Metadata and Info
- **fs.stat()** / **fs.statSync()** – Get file info (size, created date, etc.).
- **fs.existsSync()** – Check if a file or directory exists.
- **fs.access()** – Check permissions.

### File Streams (Efficient for large files)
- **fs.createReadStream()** – Create a readable stream.
- **fs.createWriteStream()** – Create a writable stream.

> [!IMPORTANT]
> The module also has a **promise** variant, its is better to use promise because of better code readability, error handling, composability, debugging and it aligns with the modern standards.

```javascript
const fs = require('fs'); //standard implementation
const fs = require('fs/promise'); //promise based implementation
```


## HTTP and HTTPS
**http**: Lets you create web servers or make HTTP requests (insecure).  
**https**: Same as http, but uses SSL/TLS encryption for secure communication.

### Basic http Server
```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello, world!');
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000');
});
```

### Basic https Server
```javascript
const http = require('https';
const fs = require('fs');

const options = {
  key: fs.readFileSync('key.pem'),
  cert: fs.readFileSync('cert.pem')
};

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello, world!');
});

server.listen(443, () => {
  console.log('Server running at http://localhost:3000');
});
```

### Agents
An Agent manages connection persistence and re-use for requests — useful for performance.
```javascript
const agent = new http.Agent({
  keepAlive: true,
  maxSockets: 10
});

http.get({ host: 'example.com', agent }, (res) => {
  // Request made using pooled connection
});
```

**Use Cases:**
- Making many requests to the same server
- Optimizing throughput
- Preventing DNS and TCP handshake overhead

## Streams
A Stream is like a flowing data pipe — it allows data to be processed piece-by-piece instead of loading it all into memory at once.
When Should You Use Streams?

### Example: Reading and Writing with Streams
```javascript
const fs = require('fs');

// Create read and write streams
const readStream = fs.createReadStream('input.txt', 'utf8');
const writeStream = fs.createWriteStream('output.txt');

// Pipe data from read to write
readStream.pipe(writeStream);
```

### Example: Transform Stream (Compression)
```javascript
const fs = require('fs');
const zlib = require('zlib');

const read = fs.createReadStream('input.txt');
const compress = zlib.createGzip();
const write = fs.createWriteStream('input.txt.gz');

read.pipe(compress).pipe(write);
```

### Use cases:
- Working with large files
- Processing real-time data
- Handling file uploads/downloads
- Writing APIs that stream responses

### Stream Events
Streams are event emitters. Common events include:
- data: a chunk is available
- end: no more data
- error: error during stream
- finish: writing is complete

### Basic Example
```javascript
readStream.on('data', chunk => {
  console.log('Chunk received:', chunk);
});
readStream.on('end', () => {
  console.log('Reading finished');
});
```

## Path
The path module provides utilities to handle and transform file paths without worrying about platform-specific path separators (\ vs /).

### Common path Methods
- **path.join(...parts)** - Joins parts into a valid path (normalizes slashes)
- **path.resolve(...parts)** - Resolves to an absolute path
- **path.basename(p)** - Gets filename from path
- **path.extname(p)** - Gets file extension
- **path.dirname(p)** - Gets directory name
- **path.parse(p)** - Returns object with root, dir, base, ext, name
- **path.format(obj)** - Converts parsed object back to a path

## Process
The process object is a global object in Node.js. It provides information and control over the current Node.js process and runtime environment.

- **process.env** - Access environment variables	process.env
- **process.exit()** - Exit or kill the process	process.exit()
- **process.on('uncaughtException')** - Handle uncaught exceptions
- **process.argv** - Read command-line arguments
- **process.on('SIGINT')** - Listen for process signals
- **process.memoryUsage()** - Get memory/CPU usage
- **process.uptime()** - Track runtime or performance

## Timers
Node's timers module is built-in and provides basic asynchronous scheduling tools. These aren’t imported explicitly — they’re globally available.

- **setTimeout()** - Run a function once after delay
- **setInterval()** - Run a function repeatedly
- **setImmediate()** - Run a function after I/O events
- **clearTimeout()** - Cancel a timeout
- **clearInterval()** - Cancel an interval

## URL
The url module helps parse, construct, and manipulate URLs.

- **new URL(string)** - Parses a full URL string
- **url.parse()** *(legacy)* - Old method for parsing URLs
- **url.format()** - Builds a URL string from an object
- **url.resolve()** - Resolves a target URL relative to base

> [!IMPORTANT]
> In newer code, it's recommended to use the WHATWG URL API (new URL(...)) instead of the older url.parse() style.

```javascript
const myURL = new URL('https://example.com:8080/path/page?name=John&age=30#top');

console.log(myURL.hostname); // example.com
console.log(myURL.port);     // 8080
console.log(myURL.pathname); // /path/page
console.log(myURL.search);   // ?name=John&age=30
console.log(myURL.searchParams.get('name')); // John

myURL.searchParams.set('name', 'Alice');
console.log(myURL.href); // Full updated URL
```

## Net
The net module allows you to create TCP clients and servers — useful for building things like:
- Chat servers
- Proxies
- Database drivers
- Custom protocols (e.g., Redis, MQTT)

### Basic TCP Server
```javascript
const net = require('net');

const server = net.createServer((socket) => {
  console.log('Client connected');

  socket.on('data', (data) => {
    console.log('Received:', data.toString());
    socket.write('Echo: ' + data);
  });

  socket.on('end', () => {
    console.log('Client disconnected');
  });
});

server.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Basic TCP Client
```javascript
const net = require('net');

const client = net.createConnection({ port: 3000 }, () => {
  client.write('Hello Server!');
});

client.on('data', (data) => {
  console.log(data.toString());
  client.end();
});
```

## Zlib
The zlib module provides Gzip, Deflate, and Brotli compression and decompression.

### Compressing and Decompressing a File
```javascript
const fs = require('fs');
const zlib = require('zlib');

const input = fs.createReadStream('file.txt');
const output = fs.createWriteStream('file.txt.gz');

input.pipe(zlib.createGzip()).pipe(output);

const unzip = fs.createReadStream('file.txt.gz')
                .pipe(zlib.createGunzip())
                .pipe(fs.createWriteStream('file-unzipped.txt'));

```

## Cluster
Creates multiple node proceses (multi-processing).

### Server Handling Request Across All CPU Cores
```javascript
const cluster = require('cluster');
const os = require('os');

if (cluster.isPrimary) {
  const numCPUs = os.cpus().length;
  console.log(`Primary ${process.pid} is running`);

  for (let i = 0; i < numCPUs; i++) {
    cluster.fork(); // Create workers
  }

  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died`);
    cluster.fork(); // Restart on crash
  });

} else {
  // Worker code
  const http = require('http');
  http.createServer((req, res) => {
    res.end(`Handled by worker ${process.pid}`);
  }).listen(3000);
}
```

### Key Features
- **cluster.fork()** - Spawns a new process
- **cluster.isPrimary** - Indicates the master process
- **cluster.worker** - Access current worker info
- **cluster.on('exit')** - Restart workers on crash

## Worker Threads
Unlike cluster, which uses processes, worker_threads uses threads inside the same process (multi-threading).

### Use Case
```javascript
// worker.js
const { parentPort } = require('worker_threads');

function heavyTask() {
  let count = 0;
  for (let i = 0; i < 1e9; i++) count++;
  return count;
}

parentPort.postMessage(heavyTask());
```

```javascript
// main.js
const { Worker } = require('worker_threads');

const worker = new Worker('./heavy-worker.js');
worker.on('message', (result) => {
  console.log('Heavy task result:', result);
});
```

## Child Process
The child-process module is helpful when:
- You need to run scripts in other languages
- Execute CLI tools (e.g., ffmpeg, git, ls)
- Avoid blocking the main event loop

### Methods Overview
- **exec()** - Runs a shell command, returns the output buffer
- **spawn()** - Runs a command as a stream (better for large output)
- **fork()** - Special case for spawning Node.js scripts
- **execFile()** - Similar to exec but skips shell parsing

### exec() - Run Shell Command
```javascript
const { exec } = require('child_process');

exec('ls -l', (error, stdout, stderr) => {
  if (error) {
    console.error(`Error: ${error.message}`);
    return;
  }
  console.log(`Output:\n${stdout}`);
});
```

### spawn() - Stream Large Output
```javascript
const { spawn } = require('child_process');

const child = spawn('ping', ['-c', '4', 'google.com']);

child.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});

child.stderr.on('data', (data) => {
  console.error(`stderr: ${data}`);
});

child.on('close', (code) => {
  console.log(`child process exited with code ${code}`);
});
```

### fork() - Run Another Node.js Script
```javascript
// child.js
process.on('message', (msg) => {
  console.log('Child received:', msg);
  process.send(`Processed: ${msg.toUpperCase()}`);
});
```

```javascript
// main.js
const { fork } = require('child_process');

const child = fork('./child.js');

child.send('hello from parent');

child.on('message', (msg) => {
  console.log('Parent got:', msg);
});
```

## Assert
The assert module provides basic assertion testing: it's used to verify conditions during code execution — especially in unit tests or debug checks.

### Example
```javascript
assert.strictEqual(2 + 2, 4);             // ✅ passes
assert.notStrictEqual(2 + 2, 5);          // ✅ passes
assert.ok(true);                          // ✅ passes
assert.throws(() => { throw new Error() }); // ✅ passes
```

### Common Assertion Methods
- **assert.strictEqual(a, b)** - Asserts a === b
- **assert.deepStrictEqual(a, b)** - Deep compare of objects
- **assert.ok(value)** - Passes if value is truthy
- **assert.throws(fn)** - Checks if function throws
- **assert.fail()** - Forces a failure

## Test
Starting in Node.js 18+, you can write tests without installing any external tools (like Mocha, Jest, etc.).

### Basic Example
```javascript
// example.test.js
const test = require('node:test');
const assert = require('assert');

test('adds numbers correctly', () => {
  assert.strictEqual(2 + 3, 5);
});

test('throws error when dividing by zero', () => {
  assert.throws(() => {
    throw new Error('Divide by zero');
  });
});
```

### Example with **describe** and async
```javascript
const { describe, test, beforeEach } = require('node:test');
const assert = require('assert');

describe('Math Utils', () => {
  let a;

  beforeEach(() => {
    a = 10;
  });

  test('a is still 10', () => {
    assert.strictEqual(a, 10);
  });

  test('addition works', async () => {
    const result = await Promise.resolve(a + 5);
    assert.strictEqual(result, 15);
  });
});
```

### Test Execution
```bash
node --test

node --test --watch
# This watches for file changes and re-runs tests automatically.
```
