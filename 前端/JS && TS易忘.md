# 一.?? 对比 ||

 ??运算符返回的是第一个已定义的值，而||（逻辑或） 返回的是第一个真值

```
let height = 0;

alert(height || 100); // 100
alert(height ?? 100); // 0
```

# 二.事件对象e

#### **通用属性**

| 属性/方法          | 说明                                     |
| ------------------ | ---------------------------------------- |
| `type`             | 事件的类型（如 `click`, `keydown`）。    |
| `target`           | 触发事件的元素。                         |
| `currentTarget`    | 当前绑定事件的元素。                     |
| `bubbles`          | 事件是否冒泡。                           |
| `cancelable`       | 事件是否可以被取消。                     |
| `defaultPrevented` | `preventDefault` 是否被调用过。          |
| `stopPropagation`  | 阻止事件冒泡。                           |
| `preventDefault`   | 阻止浏览器的默认行为（如点击链接跳转）。 |

#### **鼠标事件特有**

| 属性/方法       | 说明                                              |
| --------------- | ------------------------------------------------- |
| `clientX`       | 鼠标相对于视口的水平坐标。                        |
| `clientY`       | 鼠标相对于视口的垂直坐标。                        |
| `button`        | 按下的鼠标按钮（0：左键，1：中键，2：右键）。     |
| `relatedTarget` | 与事件相关的元素（如 `mouseover` 时进入的元素）。 |

#### **键盘事件特有**

| 属性/方法  | 说明                                |
| ---------- | ----------------------------------- |
| `key`      | 按下的键值（如 `'a'`, `'Enter'`）。 |
| `code`     | 按下的物理按键代码（如 `'KeyA'`）。 |
| `altKey`   | 是否按下 `Alt` 键。                 |
| `ctrlKey`  | 是否按下 `Ctrl` 键。                |
| `shiftKey` | 是否按下 `Shift` 键。               |

### **常见应用场景**

#### 5.1 获取触发事件的元素

```
javascript复制代码document.addEventListener('click', (e) => {
    console.log(e.target); // 点击的具体元素
});
```

#### 5.2 阻止默认行为

```
javascript复制代码document.querySelector('a').addEventListener('click', (e) => {
    e.preventDefault(); // 阻止链接跳转
});
```

#### 5.3 判断按下的键

```
javascript复制代码document.addEventListener('keydown', (e) => {
    if (e.key === 'Enter') {
        console.log('回车键被按下');
    }
});
```

#### 5.4 鼠标位置

```
javascript复制代码document.addEventListener('mousemove', (e) => {
    console.log(`X: ${e.clientX}, Y: ${e.clientY}`);
});
```

# 三.Proxy代理（TS）

JS就是没有泛型<T>来指定类型而已

------

## 1.TypeScript 中 `Proxy`

**语法：**

```
const proxy = new Proxy<T>(target, handler);
```

- **`T`**：目标对象的类型，`Proxy` 的类型参数可以让你更清楚地指定目标对象的类型。
- **`target`** 和 **`handler`** 的用法与 JavaScript 相同。

------

## 2.以下是一些常见的hadler

​	你要是创建了Proxy，你以后就要使用创建的那个Proxy对象来调用target，否则不会触发proxy，比如下面的`console.log(proxy.name);`就是使用的创建的那个proxy来返回值，这样才能触发Proxy里面定义的get 

（也是ts的，有没有泛型都可以，有些时候) 

### **`get(target, property, receiver)`**

在读取目标对象的某个属性时触发。

```
const target = { name: 'babytiann' };
const proxy = new Proxy(target, {
    get(target, property) {
        console.log(`正在读取属性 ${property}`);
        return target[property];
    }
});

console.log(proxy.name); // 输出 "正在读取属性 name" 和 "babytiann"
```

### **`set(target, property, value, receiver)`**

在给目标对象的某个属性赋值时触发。

```
const target = { age: 25 };
const proxy = new Proxy(target, {
    set(target, property, value) {
        console.log(`正在设置属性 ${property}，值为 ${value}`);
        target[property] = value;
        return true;
    }
});

proxy.age = 30; // 输出 "正在设置属性 age，值为 30"
```

### **`apply(target, thisArg, args)`**

在调用目标函数时触发。

```
const target = function (x, y) {
    return x + y;
};
const proxy = new Proxy(target, {
    apply(target, thisArg, args) {
        console.log(`正在调用函数，参数为 ${args}`);
        return target(...args);
    }
});

console.log(proxy(1, 2)); // 输出 "正在调用函数，参数为 1,2" 和 "3"
```

### **`construct(target, args, newTarget)`**

在使用 `new` 调用目标对象（即构造函数）时触发。

```
class Person {
    constructor(name) {
        this.name = name;
    }
}

const proxy = new Proxy(Person, {
    construct(target, args) {
        console.log(`正在创建实例，参数为 ${args}`);
        return new target(...args);
    }
});

const babytiann = new proxy('babytiann'); // 输出 "正在创建实例，参数为 babytiann"
```

## 3.用法

### 3.1：基础用法

```
interface User {
    name: string;
    age: number;
}

const target: User = { name: 'babytiann', age: 25 };

const proxy: User = new Proxy(target, {
    get(target, property) {
        console.log(`读取属性 ${String(property)}`);
        return target[property as keyof User];
    },
    set(target, property, value) {
        console.log(`设置属性 ${String(property)} 为 ${value}`);
        target[property as keyof User] = value;
        return true;
    }
});

console.log(proxy.name); // 输出 "读取属性 name" 和 "babytiann"
proxy.age = 30;         // 输出 "设置属性 age 为 30"
```

- **类型安全**：通过声明 `proxy: User`，TypeScript 确保代理对象必须符合 `User` 接口的定义。

------

### 3.2：数组的代理

```
const numbers: number[] = [1, 2, 3];

const proxy = new Proxy<number[]>(numbers, {
    get(target, property) {
        console.log(`访问索引 ${String(property)}`);
        return target[property as keyof typeof target];
    },
    set(target, property, value) {
        console.log(`设置索引 ${String(property)} 为 ${value}`);
        target[property as keyof typeof target] = value;
        return true;
    }
});

console.log(proxy[0]); // 输出 "访问索引 0" 和 1
proxy[1] = 42;         // 输出 "设置索引 1 为 42"
```

- **数组类型安全**：通过 `Proxy<number[]>` 确保目标对象是一个 `number` 类型的数组。

------

### 3.3：动态属性的代理

```
interface Person {
    firstName: string;
    lastName: string;
    fullName?: string;
}

const person: Person = { firstName: 'baby', lastName: 'tiann' };

const proxy = new Proxy<Person>(person, {
    get(target, property) {
        if (property === 'fullName') {
            return `${target.firstName} ${target.lastName}`;
        }
        return target[property as keyof Person];
    },
    set(target, property, value) {
        if (property === 'fullName') {
            throw new Error('fullName 是只读属性');
        }
        target[property as keyof Person] = value;
        return true;
    }
});

console.log(proxy.fullName); // 输出 "baby tiann"
proxy.firstName = 'newName'; // 修改 firstName
console.log(proxy.fullName); // 输出 "newName tiann"
// proxy.fullName = '修改值'; // 抛出错误：fullName 是只读属性
```

- **动态属性处理**：通过 `get` 捕获器动态生成 `fullName` 属性值，同时设置了 `fullName` 为只读属性。

------

### 3.4：拦截函数调用

在 TypeScript 中也可以用 `Proxy` 来代理函数。

```
const add = (x: number, y: number): number => x + y;

const proxy = new Proxy(add, {
    apply(target, thisArg, args) {
        console.log(`调用函数，参数为 ${args}`);
        return target(...args);
    }
});

console.log(proxy(1, 2)); // 输出 "调用函数，参数为 1,2" 和 3
```

- **函数类型安全**：如果目标函数有明确的参数和返回值类型，TypeScript 会自动推断并进行类型检查。

------

### 3.5：数据校验

通过 `Proxy` 和 TypeScript 类型系统，可以实现数据校验。

```
interface Product {
    id: number;
    price: number;
    name: string;
}

const product: Product = { id: 1, price: 100, name: 'item' };

const proxy = new Proxy<Product>(product, {
    set(target, property, value) {
        if (property === 'price' && typeof value !== 'number') {
            throw new TypeError('价格必须是数字');
        }
        target[property as keyof Product] = value;
        return true;
    }
});

proxy.price = 200; // 正常
// proxy.price = '200'; // 抛出错误：价格必须是数字
```

- **强类型校验**：TypeScript 的类型声明确保拦截逻辑与目标对象类型一致，避免类型错误。

------

### 3.6：与 `Readonly` 配合

TypeScript 提供了内置的工具类型，比如 `Readonly<T>`，可以结合 `Proxy` 使用。

```
const target = { name: 'babytiann', age: 25 };

const readonlyProxy = new Proxy<Readonly<typeof target>>(target, {
    set(target, property, value) {
        console.log(`属性 ${String(property)} 是只读的，无法设置`);
        return false;
    }
});

console.log(readonlyProxy.name); // 输出 "babytiann"
// readonlyProxy.age = 30; // 报错：无法设置只读属性
```

# 四.Error

JavaScript 和 TypeScript 使用 **异常机制**（Exception Handling）来处理错误。这意味着，当程序执行过程中发生错误时，会抛出一个异常，这个异常可以通过 `try...catch` 语句捕获并处理。

## **Error 对象**

在 JavaScript 和 TypeScript 中，错误是通过 `Error` 对象来表示的。`Error` 对象有几个常见的属性：

- `message`：描述错误的详细信息（字符串类型）。
- `name`：错误的名称（例如：`Error`、`TypeError`、`ReferenceError`）。
- `stack`：错误的堆栈跟踪信息（用于调试）。

## **抛出错误（Throwing Errors）**

可以通过 `throw` 关键字抛出错误，错误可以是任何类型，但通常是 `Error` 对象或其子类。例如：

```
throw new Error('Something went wrong');
```

## **捕获错误（Catching Errors）**

`try...catch` 用来捕获错误，并在发生错误时执行特定的代码：

```
try {
  // Some code that might throw an error
  throw new Error('Something went wrong');
} catch (error) {
  console.log(error.message); // 输出: 'Something went wrong'
}
```

## **错误类型**

JavaScript 的错误类型有很多种，最常见的是 `Error`、`TypeError`、`ReferenceError` 等。你可以根据需要自定义错误类型，继承 `Error` 类

# 五.fetch

下面是fetch的完全形态

```
fetch(url, {
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, *same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
    redirect: 'follow', // manual, *follow, error
    referrerPolicy: 'no-referrer', // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
    body: JSON.stringify(data) // body data type must match "Content-Type" header
  });

```

## 1.mode

​	在 `'no-cors'` 模式下，浏览器会限制响应内容的访问，主要用于跨域请求时，如果目标服务器没有正确设置 CORS 头部（例如 `Access-Control-Allow-Origin`），则请求不会被允许访问响应的内容。这种情况下，返回的 `Response` 对象的 `type` 会是 `'opaque'`，表示你无法访问响应的内容，只能知道请求是否成功。

## 2.response

​	fetch函数的返回结果是一个promise对象，其中resolve出来的是一个Response对象，通过这个对象我们可以使用一些方法读取其中的数据，并返回一个promise：

```ts
response.text() //读文本
response.json() //读json
response.blob() // Blob 对象
response.formData() // FormData 表单对象。
response.arrayBuffer() // 得到二进制 ArrayBuffer 对象
```

也可以通过一些属性判断请求是否成功，方便进行后续行为

```ts
Response.ok //请求是否成功
Response.status //表示 HTTP 回应的状态码
Response.statusText //一个字符串，表示 HTTP 回应的状态信息
```

# 六、axios

# 七、Web Beacon(只发送数据)

`Navigator.sendBeacon` 是一个非常适合在前端实现轻量级埋点、日志收集或统计的 API，它能够在页面卸载、刷新或跳转时异步地发送数据，而不阻塞页面的其他操作。相比传统的 `Image` 请求（即“Web Beacon”），`sendBeacon` 提供了更高效、可靠和无阻塞的方式来发送数据。

## 1.使用 `Navigator.sendBeacon` 实现 Web Beacon

`sendBeacon` 方法接收两个参数：

- **`url`**: 请求的目标 URL（通常是后端的日志接收端口或 API 地址）。
- **`data`**: 要发送的数据，可以是 `Blob`、`FormData`、`URLSearchParams` 或者是一个普通的字符串（通常是 JSON 格式的数据）。

**语法**

```
Navigator.sendBeacon(url, data);
```

**示例：**

假设你有一个后端 API 可以接收统计数据，URL 为 `https://example.com/track`，你希望发送一些 JSON 数据，可以这样写：

```
// 准备要发送的数据
const data = JSON.stringify({
  productID: 123,
  userID: 456,
  event: 'pageview',
  timestamp: Date.now()
});

// 发送数据到后端
if (navigator.sendBeacon) {
  const url = 'https://example.com/track';
  const isSent = navigator.sendBeacon(url, data);
  if (isSent) {
    console.log('Beacon data sent successfully');
  } else {
    console.log('Failed to send Beacon data');
  }
}
```

## 2.`sendBeacon` 的优势：

1. **不阻塞页面**：
   - `sendBeacon` 的请求会在浏览器空闲时处理，并且不会影响页面的卸载、刷新或跳转。
2. **页面卸载时也能发送成功**：
   - 当页面正在卸载或跳转时（比如用户刷新页面或导航到新页面），`sendBeacon` 会尽量确保数据被成功发送，不会因为页面加载过程中的网络请求被中断。
3. **低优先级请求**：
   - `sendBeacon` 请求被视为低优先级请求，因此不会与页面的主业务逻辑竞争带宽或线程资源。

## 3.兼容性处理：

为了确保在不支持 `sendBeacon` 的浏览器中也能发送数据，可以使用图片 `src` 或其他方式进行兜底处理。

```
// 兼容性处理
if (navigator.sendBeacon) {
  const data = JSON.stringify({
    productID: 123,
    userID: 456,
    event: 'pageview',
    timestamp: Date.now()
  });
  navigator.sendBeacon('https://example.com/track', data);
} else {
  // 如果不支持 sendBeacon，可以使用图片请求作为兜底方案
  const img = new Image();
  const data = `productID=123&userID=456&event=pageview&timestamp=${Date.now()}`;
  img.src = `https://example.com/track?${data}`;
}
```

# 八、performance.timing

​	`performance.timing` 是浏览器的 Performance API 中的一部分，它提供了一个对象，包含了浏览器加载网页时的关键时间戳。通过这些时间戳，你可以分析网页加载的各个阶段，帮助你优化页面性能。

​	`performance.timing` 返回一个对象，其中包含从页面加载开始到完全加载过程中各个重要阶段的时间数据。以下是该对象的各个属性及其含义：

## 1.`performance.timing` 对象属性：

1. **`navigationStart`**: 浏览器开始导航（加载页面）时的时间戳。
2. **`unloadEventStart`**: 上一个文档的卸载事件开始的时间戳。
3. **`unloadEventEnd`**: 上一个文档的卸载事件结束的时间戳。
4. **`redirectStart`**: 重定向开始的时间戳。如果没有发生重定向，则为 0。
5. **`redirectEnd`**: 重定向结束的时间戳。如果没有发生重定向，则为 0。
6. **`fetchStart`**: 浏览器开始请求资源的时间戳。通常是 HTTP 请求的开始时间。
7. **`domainLookupStart`**: DNS 查找开始的时间戳。
8. **`domainLookupEnd`**: DNS 查找结束的时间戳。
9. **`connectStart`**: 浏览器开始与服务器建立连接的时间戳。
10. **`connectEnd`**: 浏览器完成与服务器建立连接的时间戳（包括 SSL 握手完成的时间）。
11. **`secureConnectionStart`**: SSL 握手开始的时间戳。如果没有进行 SSL 握手，则为 0。
12. **`requestStart`**: 请求发送开始的时间戳。
13. **`responseStart`**: 浏览器开始接收到响应的时间戳。
14. **`responseEnd`**: 浏览器接收到响应的最后一个字节的时间戳。
15. **`domLoading`**: 浏览器开始解析 HTML 文档的时间戳。
16. **`domInteractive`**: 浏览器完成 HTML 解析并开始执行 JavaScript 的时间戳。
17. **`domContentLoadedEventStart`**: `DOMContentLoaded` 事件开始的时间戳。
18. **`domContentLoadedEventEnd`**: `DOMContentLoaded` 事件结束的时间戳。
19. **`domComplete`**: 浏览器完成解析和加载所有资源（如图片、样式等）的时间戳。
20. **`loadEventStart`**: `load` 事件开始的时间戳。
21. **`loadEventEnd`**: `load` 事件结束的时间戳。

上面的**不需要全部了解**，但比较关键的数据有下面几个，根据它们可以计算出FP/DCL/Load等关键事件的时间点：

1. 页面首次渲染时间：`FP(firstPaint)=domLoading-navigationStart`
2. DOM加载完成：`DCL(DOMContentEventLoad)=domContentLoadedEventEnd-navigationStart`
3. 图片、样式等外链资源加载完成：`L(Load)=loadEventEnd-navigationStart`

**FP (First Paint)**：计算的是从页面开始加载到页面首次绘制的时间，这有助于评估用户首次感知到页面的速度。

**DCL (DOMContentLoaded)**：计算的是页面的 DOM 结构加载完毕的时间，它标志着 JavaScript 可以开始执行，是一个衡量页面交互准备就绪的指标。

**L (Load)**：计算的是页面及其所有资源完全加载完毕的时间，这是一个最全面的性能指标，表示页面完全加载所需的总时间。



## 2.使用方法：

```
performance.getEntriesByType('navigation')
```

​	这是浏览器提供的内置API，只要浏览器支持，这样直接用就不会有问题

# 九、错误

## 1.err

在 JavaScript 中，`err` 通常指的是一个错误对象（Error object），它包含了一些标准的属性。以下是常见的错误对象属性：

1. **`message`**：错误的描述信息，通常是一个简短的字符串，说明错误的原因。

   - 示例：`"TypeError: undefined is not a function"`

2. **`name`**：错误的名称，表示错误类型。JavaScript 中有多个内置错误类型，如 `Error`、`TypeError`、`SyntaxError` 等。

   - 示例：`"TypeError"`

3. **`stack`**：堆栈跟踪信息，记录了错误发生时的调用栈，通常用于调试和定位错误的发生位置。`stack` 属性是一个字符串，包含了函数调用的历史记录。

   - 示例：

     ```
     at Object.<anonymous> (/path/to/file.js:12:10)
     at Module._compile (node:internal/modules/cjs/loader:1200:14)
     at Object.Module._extensions..js (node:internal/modules/cjs/loader:1261:10)
     ```

4. **`fileName`**（可选）：错误发生的文件名（在某些环境下，如 Node.js）。通常是一个字符串，表示抛出错误的文件路径。

   - 示例：`"/path/to/file.js"`

5. **`lineNumber`**（可选）：错误发生的行号（在某些环境下，如 Node.js）。是一个数字，表示错误发生的行。

   - 示例：`42`

6. **`columnNumber`**（可选）：错误发生的列号（在某些环境下，如 Node.js）。是一个数字，表示错误发生的列。

   - 示例：`15`

7. **`cause`**（可选，错误链）：指向导致当前错误的根本原因。这个属性在一些现代 JavaScript 环境（如 Node.js）中可用，用于捕捉嵌套错误。

   - 示例：`Error("Underlying database connection failed")`
   

### 完整error：

### 1.js抛出的error(触发的事件为'error')

例如，如果页面中的 JavaScript 代码出现如下错误：

```
throw new Error('Something went wrong');
```

`error` 事件的 `event` 对象可能如下所示：

```
{
    "message": "Something went wrong",
    "filename": "http://example.com/script.js",
    "lineno": 10,
    "colno": 5,
    "error": {
        "message": "Something went wrong",
        "stack": "Error: Something went wrong at script.js:10:5"
    }
}
```

### 2.Promise被拒绝且未被catch捕获

`unhandledrejection` 事件会在一个被拒绝的 `Promise`（未被捕获的拒绝）没有 `.catch()` 处理时触发。

#### 事件对象的主要属性：

- `reason`：拒绝的原因，通常是一个 `Error` 对象，包含错误信息。
- `promise`：触发该事件的 `Promise` 实例。

**示例**：

```
window.addEventListener('unhandledrejection', function (event) {
    console.log(event.reason);  // 拒绝的原因（Error 对象或其他类型）
    console.log(event.promise); // 被拒绝的 Promise
});
```

例如，如果有如下的代码，`Promise` 被拒绝且没有捕获：

```
new Promise((_, reject) => {
    reject(new Error('Promise rejection'));
});
```

`unhandledrejection` 事件的 `event` 对象可能如下所示：

```
{
    "reason": {
        "message": "Promise rejection",
        "stack": "Error: Promise rejection at <anonymous>:2:13"
    },
    "promise": "Promise { <state>: 'rejected', <reason>: Error: Promise rejection }"
}
```

## 2.错误捕获

### 2.1. **同步错误**（`try...catch` 可以捕获）

```
function synchronousError() {
  throw new Error("同步错误示例");
}

try {
  synchronousError();
} catch (err) {
  console.log("捕获到同步错误：", err.message);  // 输出: 捕获到同步错误：同步错误示例
}
```

这种错误是同步的，可以通过 `try...catch` 捕获并处理。但是如果你没有使用 `try...catch`，这个错误就会是一个全局未处理的错误，浏览器会触发 `window.addEventListener('error')`。

### 2.2. **异步错误**（`Promise` 拒绝，`unhandledrejection` 事件监听器捕获）

```
function asynchronousError() {
  return new Promise((resolve, reject) => {
    reject(new Error("异步错误示例"));
  });
}

// 注意：这里没有 .catch() 来处理 Promise 错误
asynchronousError();

// 监听未处理的 Promise 拒绝
window.addEventListener('unhandledrejection', event => {
  console.log("捕获到未处理的异步错误：", event.reason.message);  // 输出: 捕获到未处理的异步错误：异步错误示例
});
```

在这个例子中，`Promise` 被拒绝但没有使用 `.catch()` 处理。没有处理的 `Promise` 错误会触发 `unhandledrejection` 事件，你可以通过 `window.addEventListener('unhandledrejection')` 捕获并处理它。

### 2.3. **全局错误**（`window.addEventListener('error')` 捕获）

```
// 假设存在某个页面元素没有定义
let nonExistentElement = document.querySelector('.non-existent-class');

nonExistentElement.click();  // 会抛出一个 "TypeError: Cannot read property 'click' of null" 错误
```

这段代码中，试图在一个不存在的元素上触发 `click` 事件，导致抛出 `TypeError`。如果没有 `try...catch` 包裹这个错误，浏览器会触发全局 `error` 事件，可以通过 `window.addEventListener('error')` 捕获。

```
window.addEventListener('error', event => {
  console.log("捕获到全局错误：", event.message);  // 输出: 捕获到全局错误：Cannot read property 'click' of null
});
```

### 2.4. **异步错误与 `async/await`**（`async/await` 捕获）

```
async function asyncError() {
  const result = await Promise.reject(new Error("异步错误示例 with async/await"));
}

asyncError();

// 捕获未处理的 Promise 拒绝错误
window.addEventListener('unhandledrejection', event => {
  console.log("捕获到未处理的异步错误：", event.reason.message);  // 输出: 捕获到未处理的异步错误：异步错误示例 with async/await
});
```

这里，我们使用 `async/await` 来处理 `Promise`，如果没有 `.catch()` 或 `try...catch` 来捕获错误，`unhandledrejection` 事件会触发。

### 2.5. **加载错误**（如图片加载失败）

```
const img = new Image();
img.src = "http://nonexistent-image.com/image.jpg";  // 图片不存在，会触发加载错误

img.onload = () => {
  console.log("图片加载成功");
};

img.onerror = (event) => {
  console.log("图片加载失败：", event.message);  // 输出: 图片加载失败：加载图片失败
};
```

在这个例子中，图片加载失败时会触发 `onerror` 事件，这种错误通常在资源加载失败时出现。

**总结**：

- **同步错误**：`try...catch` 可以捕获，但如果未捕获，`window.addEventListener('error')` 可以捕获。
- **异步错误**：`Promise` 错误可以通过 `unhandledrejection` 捕获，`async/await` 错误也适用相同的方法。
- **全局错误**：一些全局错误（如 DOM 操作错误）无法通过 `try...catch` 捕获，但可以通过 `window.addEventListener('error')` 捕获。

# 十、常用的时间函数

## 1. **`new Date()`**

- **无参**：创建一个表示当前日期和时间的 `Date` 对象。

```
const currentDate = new Date();
```

- **有参**：`Date` 构造函数还可以接收以下几种参数：

  - **无参数**：获取当前的日期和时间。
  - **字符串**：一个合法的日期字符串（例如："2025-01-26T00:00:00Z"）。
  - **年月日**：`new Date(year, monthIndex, day, hour, minute, second, millisecond)`，月份从 `0` 开始（0 表示 1 月，1 表示 2 月，依此类推）。

  ```
  javascript复制编辑const dateFromString = new Date("2025-01-26T00:00:00Z");  // 从字符串创建日期
  const specificDate = new Date(2025, 0, 26, 0, 0, 0, 0);  // 创建 2025 年 1 月 26 日 00:00:00
  ```

## 2. **`getDay()`**

- 无参：返回 `Date`对象所表示的星期几。
  - 返回值是整数，表示一周中的某一天：
    - 0 - 星期天
    - 1 - 星期一
    - 2 - 星期二
    - 3 - 星期三
    - 4 - 星期四
    - 5 - 星期五
    - 6 - 星期六

```
const dayOfWeek = currentDate.getDay();  // 获取星期几（0-6）
```

## 3. **`getDate()`**

- **无参**：返回当前日期在当月中的第几天，范围是 1 到 31。

```
const dayOfMonth = currentDate.getDate();  // 获取当前月的天数（1-31）
```

## 4. **`setDate()`**

- **参数**：接收一个整数，表示设置的日期（1 到 31 之间的值）。

```
currentDate.setDate(10);  // 将日期设置为当月的 10 号
```

## 5. **`setMonth()`**

- 参数：
  - `monthIndex`：月份，0 表示 1 月，1 表示 2 月，依此类推（0 到 11）。
  - `day?`（可选）：设置的日期，默认为当前日期。

```
currentDate.setMonth(0);  // 设置为 1 月
currentDate.setMonth(1, 15);  // 设置为 2 月 15 日
```

## 6. **`setHours()`**

- 参数：
  - `hours`：小时数，0 到 23。
  - `minutes?`（可选）：分钟，0 到 59，默认为 0。
  - `seconds?`（可选）：秒数，0 到 59，默认为 0。
  - `milliseconds?`（可选）：毫秒数，0 到 999，默认为 0。

```
currentDate.setHours(0, 0, 0, 0);  // 设置时间为 00:00:00.000
```

## 7. **`setUTCDate()`**

- 参数：
  - `day`：UTC 时间的日期部分，1 到 31 之间的整数。

```
currentDate.setUTCDate(15);  // 设置 UTC 日期为 15 号
```

## 8. **`setUTCHours()`**

- 参数：
  - `hours`：UTC 小时数，0 到 23。
  - `minutes?`（可选）：分钟，0 到 59，默认为 0。
  - `seconds?`（可选）：秒数，0 到 59，默认为 0。
  - `milliseconds?`（可选）：毫秒数，0 到 999，默认为 0。

```
currentDate.setUTCHours(0, 0, 0, 0);  // 设置 UTC 时间为 00:00:00.000
```

## 9. **`toISOString()`**

- **无参**：将 `Date` 对象转换为 ISO 8601 格式的字符串（例如："2025-01-26T00:00:00.000Z"）。

```
const isoString = currentDate.toISOString();  // 获取 ISO 格式的日期字符串
```

## 10. **`setSeconds()`**

- 参数：
  - `seconds`：秒数，0 到 59。
  - `milliseconds?`（可选）：毫秒数，0 到 999，默认为 0。

```
currentDate.setSeconds(30);  // 设置秒为 30
currentDate.setSeconds(30, 500);  // 设置秒为 30，毫秒为 500
```

## 11. **`setMilliseconds()`**

- 参数：
  - `milliseconds`：毫秒数，0 到 999。

```
currentDate.setMilliseconds(500);  // 设置毫秒为 500
```

## 12. **`getUTCHours()`**

- **无参**：获取 `Date` 对象中 UTC 时间的小时部分，返回值是 0 到 23 之间的整数。

```
const utcHours = currentDate.getUTCHours();  // 获取当前时间的 UTC 小时部分
```
