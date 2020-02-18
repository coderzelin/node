## Node

### Node.js 是什么

node 没出来前，我们知道 JavaScript 是运行在浏览器上的，这是因为浏览器有两个内核，分别是渲染引擎和 JavaScript 引擎，渲染引擎负责渲染 HTML + CSS，JavaScript 引擎就是负责运行 JavaScript，chrome 使用的 v8 引擎运行速度非常快

node.js 是一个运行在服务端的框架，它的底层使用的是 V8 引擎

### 为什么要用 Node.js

node.js 是非阻塞的异步I/O，通过事件驱动的，node.js的应用是通过javascript开发的，然后直接在Google的V8引擎上跑。用了node.js，你就不用担心用户端的请求会在服务器里跑了一段能够造成阻塞的代码了。因为javascript本身就是事件驱动的脚本语言。你

node.js 适合这些场景
1. 实时性应用，比如网页聊天应用等
2. 以 I/O 为主的高并发应用，比如为客户端提供API，读取数据库
3. 流式应用，比如客户端经常上传文件
4. 前后端分离

### 构建一个 web 服务器

创建一个 app.js 文件
```js
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req,res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}`);
})
```

