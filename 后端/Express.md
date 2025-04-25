# 一.路由

## 1.res和req

### 1.1. **`req`（请求对象）**

`req` 对象包含了客户端发来的请求信息，常见的属性和方法有：

#### 常见属性：

- **`req.params`**: 获取路由中的参数（通常用于动态路由）。

  - 例如：`/user/:id` 路由，`req.params.id` 会获取到 `id` 的值。

  ```
  app.get('/user/:id', (req, res) => {
    res.send(`User ID: ${req.params.id}`);
  });
  // 请求 /user/123 时，返回 "User ID: 123"
  ```

- **`req.query`**: 获取 URL 查询参数（例如：`?search=test`）。

  - 用法：`req.query.search` 获取查询参数 `search` 的值。

  ```
  app.get('/search', (req, res) => {
    const query = req.query.search;
    res.send(`Search query: ${query}`);
  });
  // 请求 /search?search=test 时，返回 "Search query: test"
  ```

- **`req.body`**: 获取请求体数据（通常用于 POST 请求）。

  - 必须使用中间件（如 `express.json()` 或 `express.urlencoded()`）来解析请求体。

  ```
  app.use(express.json()); // 使用中间件解析 JSON 请求体
  
  app.post('/user', (req, res) => {
    const name = req.body.name;
    res.send(`User name: ${name}`);
  });
  // 使用 POST 请求发送 JSON 数据 { "name": "John" }
  ```

- **`req.method`**: 获取请求的 HTTP 方法（如 GET、POST、PUT、DELETE）。

  ```
  app.all('/method', (req, res) => {
    res.send(`Request method: ${req.method}`);
  });
  // 请求 /method 会返回请求的 HTTP 方法，如 GET 或 POST
  ```

- **`req.headers`**: 获取请求头

  ​	它包含了客户端请求发送的所有 HTTP 头部信息。HTTP 请求头是由浏览器或其他 HTTP 客户端（例如 Postman）自动生成的，包含了关于请求的元数据，比如用户代理、内容类型、授权信息等

  以下是一些常见的请求头字段，你可以通过 `req.headers` 访问它们：

  - **`user-agent`**：浏览器或客户端的标识信息

  - **`referer`**：上一个页面的 URL（即来源网址）在前端我们也可以直接写`document.referrer`来直接返回给后端(可能性能差)

  - **`accept`**：客户端支持的 MIME 类型

  - **`accept-language`**：客户端支持的语言

  - **`content-type`**：请求体的数据格式

  - **`cookie`**：请求中携带的所有 cookie 信息
    ```
    //使用示例
    const userAgent = req.headers['user-agent'];
    const referer = req.headers['referer'];
    ```
    
    
  

#### 常见方法：

- **`req.get(headerName)`**: 获取请求头中的某个字段。其实跟`req.headers`的效果是一样的，只是写法不一样

  ```
  app.get('/headers', (req, res) => {
    const userAgent = req.get('User-Agent');
    res.send(`User-Agent: ${userAgent}`);
  });
  ```

### 1.2. **`res`（响应对象）**

`res` 对象用来设置和发送响应，常见的属性和方法有：

#### 常见属性：

- **`res.status(code)`**: 设置响应的 HTTP 状态码。

  ```
  app.get('/notfound', (req, res) => {
    res.status(404).send('Page not found');
  });
  // 请求 /notfound 会返回 404 状态码和 "Page not found" 消息
  ```

- **`res.set(headerName, value)`**: 设置响应头。

  ```
  app.get('/set-header', (req, res) => {
    res.set('X-Custom-Header', 'Hello');
    res.send('Header set');
  });
  // 响应会包含自定义的 X-Custom-Header 头部
  ```

#### 常见方法：

- **`res.send(body)`**: 发送响应内容，可以是字符串、对象、数组等。

  - 如果是字符串，会返回普通文本响应。
  - 如果是对象或数组，会自动将其转换为 JSON 格式返回。

  ```
  app.get('/send-text', (req, res) => {
    res.send('Hello, World!');
  });
  
  app.get('/send-json', (req, res) => {
    res.send({ message: 'Hello, World!' });
  });
  // 请求 /send-text 返回 "Hello, World!" 字符串
  // 请求 /send-json 返回 { "message": "Hello, World!" } JSON 数据
  ```

  ​	**这个超级神奇**，如果前端用的是`text()`，后端使用这个`send`来发送的数据，就不会触发跨域，但是如果后端是用下面的json，然后前端用json()来解析就会触发跨域（受同源策略的约束）

- **`res.json(data)`**: 发送 JSON 格式的响应。该方法会自动设置响应头为 `Content-Type: application/json`，并将数据序列化为 JSON。

  ```
  app.get('/json', (req, res) => {
    const data = { user: 'John', age: 30 };
    res.json(data);
  });
  // 请求 /json 返回 { "user": "John", "age": 30 } JSON 数据
  ```

- **`res.sendFile(path)`**: 发送一个文件作为响应。

  ```
  app.get('/file', (req, res) => {
    res.sendFile('/path/to/file.html');
  });
  // 请求 /file 会返回一个文件
  ```

- **`res.redirect(url)`**: 重定向到另一个 URL。

  ```
  app.get('/redirect', (req, res) => {
    res.redirect('/new-url');
  });
  // 请求 /redirect 会自动跳转到 /new-url
  ```

- **`res.render(view, locals)`**: 渲染视图并返回 HTML 页面（通常用于配合模板引擎，如 EJS、Pug 等）。

  ```
  app.set('view engine', 'ejs');
  
  app.get('/view', (req, res) => {
    res.render('index', { title: 'Hello, World!' });
  });
  // 该请求会渲染 `views/index.ejs` 模板并传递数据
  ```

#### 示例：一个完整的使用 `req` 和 `res` 的例子

```
js复制代码const express = require('express');
const app = express();

// 使用中间件解析请求体
app.use(express.json());

// 路由：GET 请求，获取查询参数
app.get('/greet', (req, res) => {
  const name = req.query.name || 'Guest'; // 获取查询参数
  res.send(`Hello, ${name}!`);
});

// 路由：POST 请求，处理 JSON 请求体
app.post('/user', (req, res) => {
  const { name, age } = req.body; // 获取请求体
  if (!name || !age) {
    return res.status(400).send('Name and age are required!');
  }
  res.status(201).json({ message: `User ${name} created`, name, age });
});

// 路由：GET 请求，查看请求头
app.get('/headers', (req, res) => {
  const userAgent = req.get('User-Agent'); // 获取请求头中的 User-Agent 字段
  res.send(`User-Agent: ${userAgent}`);
});

// 启动服务器
app.listen(5927, () => {
  console.log('Server is running on http://localhost:5927');
});
```

### 总结：

- **`req`** 对象用来接收客户端请求的各种信息，包括路由参数、查询参数、请求体和请求头。
- **`res`** 对象用来设置和发送响应，包括状态码、响应体、JSON 数据、文件等。

## 2.路由的use方法

这个东西可以挂载中间件，也可以挂载路由

### 2.1中间件

​	`app.use` 可以接收一个函数作为中间件，在请求到达路由之前对请求进行处理。中间件的作用是执行代码、修改请求对象（`req`）、修改响应对象（`res`）或终止请求-响应循环。

#### 示例：跨域资源共享（CORS）中间件

```typescript
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");  // 允许所有域
  res.header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE");  // 允许的方法
  res.header("Access-Control-Allow-Headers", "Content-Type");  // 允许的请求头
  next();  // 调用下一个中间件或路由处理函数
});
```

在这个例子中，`app.use` 接受了一个函数作为中间件，并且该中间件会在每个请求到达具体路由之前执行。它设置了 CORS 头部，允许跨域请求，并且调用 `next()` 继续处理请求，进入下一个中间件或路由。

#### 中间件的执行顺序：

- Express 会从上到下执行中间件，直到请求被处理或响应返回。
- 每个中间件必须调用 `next()` 来继续请求的处理，或者返回响应来终止请求。

### 2.2挂载路由

- `app.use` 也可以用于将路由挂载到特定的路径上。挂载后，该路径下的请求会被该路由处理。

  #### 示例：将路由挂载到 `/book` 路径下

  ```typescript
  import bookRouter from './routes/book';  // 导入书籍路由
  
  app.use('/book', bookRouter);  // 将 bookRouter 挂载到 /book 路径
  ```

  在这个例子中，`app.use('/book', bookRouter)` 表示将 `bookRouter` 路由挂载到 `/book` 路径上。这样，所有对 `/book` 路径的请求都会被 `bookRouter` 路由处理。

  #### 进一步解释：

  - **路由路径**：`/book` 表示所有以 `/book` 为路径前缀的请求都会交给 `bookRouter` 处理。
  - **路由处理**：`bookRouter` 是一个定义了多个路由的对象，所有在 `bookRouter` 中定义的路由都会处理 `/book` 路径下的请求。

### 2.3**两者的区别**

- **挂载中间件**：`app.use` 后跟一个中间件函数，它在每个请求到达路由之前执行，用来处理一些公共逻辑（如认证、日志、跨域等）。可以用于所有请求或某个特定路径。

  例子：

  ```typescript
  app.use((req, res, next) => {
    console.log('Request received');
    next();
  });
  ```

- **挂载路由**：`app.use` 后跟一个路由模块，它处理指定路径下的所有请求。挂载后，该路径的所有请求都会交给对应的路由来处理。

  例子：

  ```typescript
  import bookRouter from './routes/book';
  app.use('/book', bookRouter);  // 处理 /book 路径的请求
  ```

### 2.4多个中间件结合路由

#### **实例1：**

```typescript
import express from "express";

const app = express();

// 公共中间件：处理跨域请求
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  next();  // 继续处理请求
});

// 公共中间件：打印请求路径
app.use((req, res, next) => {
  console.log(`Requested URL: ${req.url}`);
  next();  // 继续处理请求
});

// 路由处理
app.get("/", (req, res) => {
  res.send("Hello from root!");
});

app.get("/about", (req, res) => {
  res.send("About us page");
});

export default app;
```

#### 实例2：实现CORS预检请求

```
import express, { Request, Response } from "express";

const app = express();

// 中间件：处理 CORS 预检请求（OPTIONS 请求）
app.use((req: Request, res: Response, next) => {
  const origin = req.headers.origin as string;

  // 使用正则匹配检查来源是否为 gemdzqq.com 的子域
  const allowedOrigin = /^(https?:\/\/.*\.gemdzqq\.com)$/;

  // 如果请求的 Origin 是 gemdzqq.com 或其子域，允许跨域
  if (allowedOrigin.test(origin)) {
    res.header("Access-Control-Allow-Origin", origin); // 允许该域名
  } else {
    res.header("Access-Control-Allow-Origin", ""); // 不允许其他域
  }

  res.header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
  res.header("Access-Control-Allow-Headers", "Content-Type, Authorization");

  // 对于预检请求（OPTIONS），返回204状态，表示没有内容
  if (req.method === "OPTIONS") {
    return res.status(204).send(); // 204 No Content
  }

  next(); // 调用下一个中间件或路由
});

// 路由：处理实际的资源请求
app.get("/data", (req: Request, res: Response) => {
  // 返回实际的资源数据
  res.send("111");  // 这里可以替换为实际的数据响应
});

// 启动服务器
app.listen(3000, () => {
  console.log("Server is running on http://localhost:3000");
});

export default app;
```

​	这个是检查，请求源是否是gemdzqq.com以及其子域名。

​	`test()` 是 JavaScript 中 **正则表达式（RegExp）** 对象的一个方法。它用于测试一个字符串是否匹配某个正则表达式。如果字符串与正则表达式匹配，`test()` 方法会返回 `true`，否则返回 `false`。

### 语法：

```
regex.test(string);
```

- `regex`：正则表达式对象。
- `string`：要测试的字符串。

### 功能：

- `test()` 方法会检查字符串是否符合正则表达式的模式。
- 如果符合，返回 `true`；如果不符合，返回 `false`。

# 二.中间件

## 1.body-parser(解析前端请求体, 已经内置进express.js)

内置之后只需要把body-parser的地方换成express即可，如下所示

```
app.use(express.json());  // 解析 JSON 请求体
app.use(express.urlencoded({ extended: true }));  // 解析 URL-encoded 请求体
```

​	这个中间件用于解析前端请求的请求体中的内容，比如有如下请求

```
const trackEvent = async (eventData) => {
  try {
    const response = await fetch('http://localhost:3000/api/track', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json', // 发送 JSON 数据
      },
      body: JSON.stringify(eventData), // 将埋点数据转换为 JSON 字符串
    });

    const result = await response.json();
    console.log('上报成功:', result);
  } catch (error) {
    console.error('上报失败:', error);
  }
};

// 示例调用
trackEvent({
  event: 'page_view',
  page: '/home',
  userId: 12345,
  timestamp: Date.now(),
});
```

​	那么后端就要用到这个中间件来解析这个`eventData`

```
const express = require('express');
const bodyParser = require('body-parser');

const app = express();
const port = 3000;

// 使用 body-parser 中间件解析 JSON 格式的请求体
app.use(bodyParser.json());

app.post('/api/track', (req, res) => {
  // 解析埋点数据
  const eventData = req.body;

  // 模拟存储或处理埋点数据
  console.log('收到的埋点数据:', eventData);

  // 这里可以将数据存入数据库，或者进行其他处理

  res.json({ message: '埋点数据上报成功' });
});

app.listen(port, () => {
  console.log(`服务器运行在 http://localhost:${port}`);
});
```

​	为什么这里使用中间件的方式跟上面将中间件的时候用的CORS使用的方式不一样？为什么这个没有`res, req, next`参数？

​	因为body-parser.json()返回的是一个函数，所以根本不需要你多写，如下所示：

```
function jsonParser(req, res, next) {
  if (req.headers['content-type'] === 'application/json') {
    let data = '';
    
    req.on('data', chunk => {
      data += chunk;
    });
    
    req.on('end', () => {
      try {
        req.body = JSON.parse(data);  // 解析 JSON 数据
        next();  // 继续处理请求
      } catch (error) {
        res.status(400).send('Invalid JSON');
      }
    });
  } else {
    next();  // 如果不是 JSON 格式，跳过此中间件
  }
}
```

# 三、流式请求

`response.data.on` 主要用于**监听流式响应（stream response）**，其中 `.on` 是 Node.js **EventEmitter** 事件监听的方法。在 `axios` 发送请求时，如果 `responseType` 设置为 `"stream"`，`response.data` 就变成了一个可读流（Readable Stream）。

------

## **1. 语法解释**

```
typescript


复制编辑
response.data.on("event", callback)
```

- `response.data` 是 **可读流（Readable Stream）**。
- `.on("event", callback)` 监听某个事件，**当事件发生时，执行回调函数 `callback`**。
- 适用于 **流式数据处理**，通常用于处理大文件、音视频流、服务器推送消息（如 SSE）、AI 语言模型流式响应等。

------

## **2. `response.data.on("data", callback)`**

```
typescript复制编辑response.data.on("data", (chunk: Buffer) => {
    console.log("收到数据块:", chunk.toString());
});
```

- **监听 `"data"` 事件**：每当有新的数据块到达时，`callback` 就会被调用。
- **参数 `chunk: Buffer`**：每次返回的数据是一个 `Buffer` 类型的**二进制数据块**。
- **`chunk.toString()`**：转换为字符串，以便解析文本。

**示例**：

```
typescript复制编辑response.data.on("data", (chunk: Buffer) => {
    console.log("收到的数据片段:", chunk.toString());
});
```

⚡ **用途**：用于**逐步接收数据**，适用于 AI API、流式传输、日志监听等。

------

## **3. `response.data.on("end", callback)`**

```
typescript复制编辑response.data.on("end", () => {
    console.log("数据传输完成");
});
```

- **监听 `"end"` 事件**：数据流完成，服务器不再发送新数据。
- **适用于流式传输的终止标志**。

**示例**：

```
typescript复制编辑response.data.on("end", () => {
    console.log("所有数据接收完毕");
});
```

⚡ **用途**：用于**在数据流结束后执行操作**（比如关闭连接、合并数据等）。

------

## **4. `response.data.on("error", callback)`**

```
typescript复制编辑response.data.on("error", (err) => {
    console.error("发生错误:", err);
});
```

- **监听 `"error"` 事件**：当请求失败或数据流中断时触发。
- **参数 `err`**：错误对象，包含错误信息。

⚡ **用途**：**捕获数据流错误**，防止应用崩溃。

------

## **5. 完整示例**

```
typescript复制编辑response.data.on("data", (chunk: Buffer) => {
    console.log("收到数据:", chunk.toString());
});

response.data.on("end", () => {
    console.log("数据接收完毕");
});

response.data.on("error", (err) => {
    console.error("数据流错误:", err);
});
```
