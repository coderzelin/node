## Node

### Node.js 是什么

node 没出来前，我们知道 JavaScript 是运行在浏览器上的，这是因为浏览器有两个内核，分别是渲染引擎和 JavaScript 引擎，渲染引擎负责渲染 HTML + CSS，JavaScript 引擎就是负责运行 JavaScript，chrome 使用的 v8 引擎运行速度非常快

node.js 是一个运行在服务端的框架，它的底层使用的是 V8 引擎

### 为什么要用 Node.js

node.js 是非阻塞的异步I/O，通过事件驱动的，node.js的应用是通过JavaScript开发的，然后直接在Google的V8引擎上跑。用了node.js，你就不用担心用户端的请求会在服务器里跑了一段能够造成阻塞的代码了。因为JavaScript本身就是事件驱动的脚本语言。

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
使用 node app.js 运行程序，访问 `http://localhost:3000` 你就会看到一个 Hello World 

### Node 的特点

#### 异步I/O

在Node中，绝大多数的操作都以异步的方式进行调用，作者在底层构建了很多异步I/O的API，从文件读取到网络请求，都是如此，这样做的意义是，我们可以从语言层面很自然的进行并行I/O操作，每个调用之间无须等待I/O调用结束，在编程模型上可以极大提升效率

在前端中，异步是很常见的，如AJAX，在Node中也很常见，以读取文件为例，我们可以看到它与前端调用AJAX的方式是类似的

```js
var fs = require('fs');

fs.readFile('/path', (err, file) => {
  console.log('读取文件完成');
})
console.log('开始读取文件');
```
这里的“开始读取文件”是在“读取文件完成”之前输出的，“读取文件完成”的执行取决于读取文件的异步调用何时完成

#### 事件与回调函数

Node 将前端浏览器的事件引入后端，配合异步I/O，将事件点暴露给业务逻辑

下面例子展示的是AJAX异步提交服务端处理过程

```js
var http = require('http');

http.createServer((req, res) => {
  var postData = '';
  req.setEncoding('utf8');
  req.on('data', chunk => {
    postData += chunk
  });
  req.on('end', () => {
    res.end(postData)
  })
}).listen(8080)

console.log('服务器启动完成')
```
相应地，我们在前端为AJAX绑定了success事件，在发出请求后，只需关心请求成功时执行相应的业务逻辑即可

```js
$.ajax({
  'url': '/url',
  'method': 'POST',
  'data': {},
  'success': function(data) {

  }
})
```

在node中回调函数无处不在，这是因为在JavaScript中，我们将函数作为一等公民来对待，可以将函数作为对象传递给方法作为实参进行调用，Node除了异步和事件外，回调函数是一大特色，回调函数也是最好接受的异步调用返回数据的方式

#### 单线程

node实际上也是JavaScript，所以node也是单线程

单线程的缺点
- 无法利用多核CPU
- 错误会引起整个应用退出
- 大量计算占用CPU导致无法继续调用异步I/O

node 采用子进程来解决大计算量的问题，子进程的出现让node可以从容地面对单线程在健壮性和无法利用多核CPU方面的问题，通过将计算分发到子进程，可以将大量计算分解掉，然后再通过进程之间的事件消息来传递结果，这可以很好地保证应用模型的简单和低依赖

#### 跨平台
node 能在多个平台上运行

### 模块化

CommonJS对模块的定义十分简单，主要分为模块引用，模块定义和模块标识3个部分

1. 模块引用

```js
var math = require('math');
```
在CommonJS规范中，存在require()方法，这个方法接受模块标识，以此引入一个模块的API到当前上下文中

2. 模块定义

在模块中，上下文提供require() 方法来引入外部模块。对应引入的功能，上下文提供了exports对象用于导出当前模块的方法或者变量，并且它是唯一导出的出口。在模块中，还存在一个module对象，它代表模块自身，而exports是module的属性。在Node中，一个文件就是一个模块，将方法挂载在exports对象上作为属性即可定义导出的方式：

```js
// math.js
exports.add = funtion() {
  var sum = 0,
  i = 0,
  args = arguments,
  l = args.length;
  while(i < l) {
    sum += args[i++];
  }
  return sum;
}
```

在另一个文件中，我们通过require()方法引入模块后，就能调用定义的属性和方法了

```js
// program.js
var math = require('math');
exports.increment = function(val) {
  return math.add(val, 1);
}
```

3. 模块标识

模块标识其实就是传递给require() 方法的参数，它必须是符合小驼峰命名的字符串，或者以. 、.. 开头的相对路径，或者绝对路径。它可以没有文件名后缀.js。 模块的定义十分简单，接口也十分简洁。每个模块具有独立的空间，它们互不干扰，在引用时也显得干净利落。



