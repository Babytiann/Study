# [vite](https://cn.vitejs.dev/guide/build.html)

## 一、[Glob导入](https://cn.vitejs.dev/guide/features#multiple-patterns)

### 	1.使用import.meta.glob

​		Vite 支持使用特殊的 `import.meta.glob` 函数从文件系统导入多个模块：

```
const 
modules = import.meta.
glob('./dir/*.js')
```

以上将会被转译为下面的样子：

```
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js'),
  './dir/bar.js': () => import('./dir/bar.js'),
}
```

​	匹配到的文件默认是懒加载的，通过动态导入实现，并会在构建时分离为独立的 chunk。如果你倾向于**直接引入所有的模块**，而不是用哪个模块再去加载，你可以传入 `{ eager: true }` 作为第二个参数： 

```
const modules = import.meta.
glob('./dir/*.js', { 
eager: true })
```

### 	2.[多个匹配模式](https://cn.vitejs.dev/guide/features#multiple-patterns)

第一个参数可以是一个 glob 数组，例如：

```
const 
modules = import.meta.
glob(['./dir/*.js', './another/*.js'])
```

### 	3.[反面匹配模式](https://cn.vitejs.dev/guide/features#negative-patterns)

同样也支持反面 glob 匹配模式（以 `!` 作为前缀）。若要忽略结果中的一些文件，你可以添加“排除匹配模式”作为第一个参数：

```
const 
modules = import.meta.
glob(['./dir/*.js', '!**/bar.js'])
```

```
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js'),
```

## 二、[静态资源处理](https://cn.vitejs.dev/guide/assets.html)

### 1.将资源引入为URL

​	服务时引入一个静态资源会返回解析后的公共路径：

```
import imgUrl from './img.png'
document.getElementById('hero-img').src = imgUrl
```

​	这个代码的意思就是把图片装进imgurl，然后给`id`是`“hero-img”`的标签的`src`属性改成这个

​	行为类似于 Webpack 的 `file-loader`。区别在于导入既可以使用绝对公共路径（基于开发期间的项目根路径），也可以使用相对路径。

### 2.显式 URL 引入

未被包含在内部列表或 `assetsInclude` 中的资源，可以使用 `?url` 后缀显式导入为一个 URL。这十分有用，例如，要导入 [Houdini Paint Worklets](https://houdini.how/usage) 时：

```
import workletURL from 'extra-scalloped-border/worklet.js?url'
CSS.paintWorklet.addModule(workletURL)
```

### 3.显式内联处理

可以分别使用`?inline`或`?no-inline`后缀，明确导入带内联或不带内联的静态资源。

```
import imgUrl1 from './img.svg?no-inline'
import imgUrl2 from './img.png?inline'
```

​	默认是不内联的，内联的意思就是把资源**直接嵌入**到最终的js里面，而不是作为**独立文件**

### 4.将资源引入为字符串

资源可以使用 `?raw` 后缀声明作为字符串引入。

```
import 
shaderString from './shader.glsl?raw'
```

### 5.[导入脚本作为 Worker](https://cn.vitejs.dev/guide/assets.html#importing-script-as-a-worker)

脚本可以通过 `?worker` 或 `?sharedworker` 后缀导入为 web worker。

```
// 在生产构建中将会分离出 chunk
import Worker from './shader.js?worker'
const worker = new Worker()
```

```
// sharedworker
import SharedWorker from './shader.js?sharedworker'
const sharedWorker = new SharedWorker()
```

```
// 内联为 base64 字符串
import InlineWorker from './shader.js?worker&inline'
```

### 6.public目录

可以把下面的问价放入public，然后可以直接通过“ / ”来访问比如， `public/icon.png` 应该在源码中被引用为 `/icon.png`。

- 不会被源码引用（例如 `robots.txt`）

- 必须保持原有文件名（没有经过 hash）

- ...或者你压根不想引入该资源，只是想得到其 URL。

### 7.new URL(url, import.meta.url)

  [import.meta.url](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import.meta) 是一个 ESM 的原生功能，会暴露当前模块的 URL。将它与原生的 [URL 构造器](https://developer.mozilla.org/en-US/docs/Web/API/URL) 组合使用，在一个 JavaScript 模块中，通过相对路径我们就能得到一个被完整解析的静态资源 URL：

  ```
const imgUrl = new URL('./img.png', import.meta.url).href

document.getElementById('hero-img').src = imgUrl
  ```

 这会把一个相对路径解析成一个绝对 URL，**但是它不可以在SSR中使用**

​	<img src="D:\Web学习\前端\assets\image-20241119193649802.png" alt="image-20241119193649802" style="zoom:67%;" />

## 三、chunk

​	在生产构建中，`chunk` 通常指的是打包工具（如 Vite、Webpack 等）生成的一个代码块或文件。

​	当你使用类似 `import Worker from './shader.js?worker'` 这样的语法时，构建工具会将 `shader.js` 文件提取出来，作为一个独立的 chunk 进行处理。这是因为 Web Workers 需要在独立线程中执行代码，而不是作为主线程的一部分

## 四、[构建生产版本](https://cn.vitejs.dev/guide/build#rebuild-on-files-changes)

写在build:{ }里面，比如

```
export default defineConfig({
  build: {
    rollupOptions: {           //这里是自定义构建通过 build.rollupOptions 直接调整底层的 Rollup 选项
      // https://rollupjs.org/configuration-options/
    },
  },
})
```

​	<img src="D:\Web学习\前端\assets\image-20241119195617874.png" alt="image-20241119195617874" style="zoom:50%;" />

### 1.公共基础路径

​	就是把打包完的静态资源放到指定的路径下去

### 2.自定义构建

​	构建过程可以通过多种 [构建配置选项](https://cn.vitejs.dev/config/#build-options) 来自定义构建。具体来说，你可以通过 `build.rollupOptions` 直接调整底层的 [Rollup 选项](https://rollupjs.org/configuration-options/)：

```
vite.config.js

export default defineConfig({
  build: {
    rollupOptions: {
      // https://rollupjs.org/configuration-options/
    },
  },
})
```

### 3.文件变化时重新构建

​	你可以使用 `vite build --watch` 来启用 rollup 的监听器。或者可以直接通过 `build.watch` 调整底层的 [`WatcherOptions`](https://rollupjs.org/configuration-options/#watch) 选项：

```
vite.config.js

export default defineConfig({
  build: {
    watch: {
      // https://rollupjs.org/configuration-options/#watch
    },
  },
})
```

​	当启用 `--watch` 标志时，对 `vite.config.js` 的改动，以及任何要打包的文件，都将触发重新构建。

### 	4.多页面应用模式

​	这里就是用了上面的自定义构建**`rollupOptions`**

```
vite.config.js

import { resolve } from 'path'
import { defineConfig } from 'vite'

export default defineConfig({
  build: {
    rollupOptions: {
      input: {
        main: resolve(__dirname, 'index.html'),
        nested: resolve(__dirname, 'nested/index.html'),
      },
    },
  },
})
```

​	就是使用`rollupOptions`以及`resolve`实现多入口文件，但是主要的还是index.html，你访问根路由的时候会展示，但是你访问/nested路由的时候才会给你展示`nested`这个入口文件的内容

​	如果指定了根目录，就要把根目录加到你给出的路径中：

```
import { resolve } from 'path'
import { defineConfig } from 'vite'

export default defineConfig({
  root: 'src', // 自定义根目录
  build: {
    rollupOptions: {
      input: {
        main: resolve(__dirname, 'src/index.html'), // 添加 root 的路径
        nested: resolve(__dirname, 'src/nested/index.html'),
      },
    },
  },
})
```



### 	5.库模式（Ts小心）

在Ts中要先安装TypeScript 类型定义包`@types/node`，不然你会发现你没有`resolve、__dirname`等等

```
yarn add --dev @types/node
```



```
vite.config.js

import { resolve } from 'path'
import { defineConfig } from 'vite'

export default defineConfig({
  build: {
    lib: {
      entry: resolve(__dirname, 'lib/main.js'), //这个就是入口文件，在写库的时候入口文件不能是.html只能是.js或.ts
      name: 'MyLib',
      // 将添加适当的扩展名后缀
      fileName: 'my-lib',
    },
    rollupOptions: {
      // 确保外部化处理那些
      // 你不想打包进库的依赖
      external: ['vue'],
      output: {
        // 在 UMD 构建模式下为这些外部化的依赖
        // 提供一个全局变量
        globals: {
          vue: 'Vue',
        },
      },
    },
  },
})
```

​	使用如上配置运行 `vite build` 时，将会使用一套面向库的 Rollup 预设，并且将为该库提供两种构建格式：

- `es` 和 `umd` (单入口)，在上面的代码中，Vite 会自动生成 `my-lib.es.js` 和 `my-lib.umd.js` 两个文件
- `es` 和 `cjs` (多入口)，在多入口配置中，Vite 会为每个入口（如 `foo` 和 `bar`）生成 `foo.es.js`、`foo.cjs.js`、`bar.es.js` 和 `bar.cjs.js` 文件。

​	 多入口其实就是刚刚讲的多页面应用模式，只是一个在`input`里面写，这个在` entry`里面写（**具体看build.lib**），多入口把entry换成下面这个就行：

```
 entry: {
        'my-lib': resolve(__dirname, 'lib/main.js'),
        secondary: resolve(__dirname, 'lib/secondary.js'),
      },
 没有给出filename这个属性，会自动给你加上build.lib预置的那个
```

然后在入口文件里面写：

```
import Foo from './Foo.vue'
import Bar from './Bar.vue'
export { Foo, Bar }
```

​	就可以把这两个组件变成库文件

当然，package.json的有些东西是要你自己改的

```
package.json
//单入口

{
  "name": "my-lib",
  "type": "module",
  "files": ["dist"],
  "main": "./dist/my-lib.umd.cjs",
  "module": "./dist/my-lib.js",
  "exports": {
    ".": {    
      "import": "./dist/my-lib.js",
      "require": "./dist/my-lib.umd.cjs"
    }
  }  // " . "是默认导出、根路径，代表库的根入口文件
}

//多入口

{
  "name": "my-lib",
  "type": "module",
  "files": ["dist"],
  "main": "./dist/my-lib.cjs",   //这个字段指定了库的 CommonJS 版本的入口文件
  "module": "./dist/my-lib.js",  //这个字段指定了库的 ECMAScript Module (ESM) 版本的入口文件
  "exports": {
    ".": {
      "import": "./dist/my-lib.js",  // ESM 格式入口（自动省略.es）
      "require": "./dist/my-lib.cjs"  // CommonJS 格式入口
    },
    "./secondary": {
      "import": "./dist/secondary.js",  // ESM 格式的 secondary 模块（自动省略.es）
      "require": "./dist/secondary.cjs"  // CommonJS 格式的 secondary 模块
    }
  }
}
```

​	使用了多入口可以实现让用户自己选择想导入这个库中的什么模块、功能

这里的 **`.`** 表示这个库的根路径，打包后的 **入口文件通常放在 `dist` 文件夹的根目录**，以方便引用。也就是说：

- **`import`**：如果其他开发者通过 `import` 语法导入你的库，使用的是 `./dist/my-lib.js` 文件（ESM 格式）。
- **`require`**：如果通过 `require` 语法导入库，使用的是 `./dist/my-lib.umd.cjs` 文件（CommonJS 格式）当然这里看你用的啥，你如果用单入口那么就是umd格式。

#### （1）如何使用这个库（多入口）：

​	单入口其实同理，就用默认导入就是一个意思

1. **默认导入**： 用户可以直接导入库的默认功能：

   ```
   import MyLib from 'my-lib';
   // 或者使用 CommonJS
   const MyLib = require('my-lib');
   ```

2. **按需导入 secondary 模块**： 用户只需要导入 `secondary` 功能：

   ```
   import Secondary from 'my-lib/secondary';
   // 或者使用 CommonJS
   const Secondary = require('my-lib/secondary');
   ```

#### （2）可单独导出CSS

不用在vite.config.js里面写什么，因为CSS的部分在构建的时候就会自动生成，这里就是为了让用户可以单独导入

```
package.json

{
  "name": "my-lib",
  "type": "module",
  "files": ["dist"],
  "main": "./dist/my-lib.umd.cjs",
  "module": "./dist/my-lib.js",
  "exports": {
    ".": {
      "import": "./dist/my-lib.js",
      "require": "./dist/my-lib.umd.cjs"
    },
    "./style.css": "./dist/my-lib.css"
  }
}
```

## 五、[部署静态站点](https://cn.vitejs.dev/guide/static-deploy.html)

​	这个有非常非常多，他们做的事情就是把你写好的源代码构建好，把生成的成果放到网页上，供大家访问，访问你仓库地址的时候会展示网页，而不是直接进去源码界面

<img src="D:\Web学习\前端\assets\image-20241120162117562.png" alt="image-20241120162117562" style="zoom:60%;" />

这里只说一下`vercel`的

### Vercel：

​	有两种使用它的方法，一种是`Vercel CLI`，另一种是`Vercel for Git`

#### 1.Vercel CLI

1. 安装 [Vercel CLI](https://vercel.com/cli) 并运行 `vercel` 来部署。
2. Vercel 会检测到你正在使用 Vite，并会为你开启相应的正确配置。
3. 你的应用被部署好了！（示例：[vite-vue-template.vercel.app](https://vite-vue-template.vercel.app/)）

bash

```
$ npm i -g vercel
$ vercel init vite
Vercel CLI
> Success! Initialized "vite" example in ~/your-folder.
- To deploy, `cd vite` and run `vercel`.
```

------

#### 2.Vercel for Git

1. 将你的代码推送到远程仓库（GitHub，GitLab，Bitbucket）
2. [导入你的 Vite 仓库](https://vercel.com/new) 到 Vercel
3. Vercel 会检测到你正在使用 Vite，并会为你的部署开启相应的正确配置。
4. 你的应用被部署好了！（示例：[vite-vue-template.vercel.app](https://vite-vue-template.vercel.app/)）

在你的项目被导入和部署后，所有对分支的后续推送都会生成 [预览部署](https://vercel.com/docs/concepts/deployments/environments#preview)，而所有对生产分支（通常是"main"）的更改都会生成一个 [生产构建](https://vercel.com/docs/concepts/deployments/environments#production)

查看 Vercel 的 [Git 集成](https://vercel.com/docs/concepts/git) 了解更多细节。

## 六、环境变量和模式

### 1.`.env` 文件

#### 1.1**创建 `.env` 文件**

你可以在项目的根目录手动创建以下文件之一或多个，取决于需要：

- `.env`
- `.env.local`
- `.env.[mode]`（例如 `.env.production`）
- `.env.[mode].local`（例如 `.env.production.local`）

------

#### 1.2. **添加环境变量**

环境变量的格式为 `KEY=VALUE`。如果需要让变量暴露给客户端代码，确保使用 `VITE_` 前缀。例如：

```
dotenv

# .env
VITE_API_BASE_URL=https://api.example.com
VITE_API_KEY=1234567890abcdef
DB_PASSWORD=mysecretpassword
```

在这个示例中：

- `VITE_API_BASE_URL` 和 `VITE_API_KEY` 会被暴露给客户端。
- `DB_PASSWORD` 只在服务端使用，不能通过 `import.meta.env` 获取。

------

#### 1.3. **加载优先级**

加载优先级（顺序从先到后）如下（从高到低）：

1. 执行命令时直接设置的变量，例如：`VITE_API_BASE_URL=https://example.com vite build`。这个是直接在命令行里面写的

2. `.env.[mode].local`（特定模式，且本地文件）。

3. `.env.[mode]`（特定模式）。

4. `.env.local`（通用本地文件）。

5. `.env`（通用文件）。

   当产生冲突时，优先级高的那个环境变量覆盖优先级低的

------

#### 1.4. **在代码中访问**

在客户端代码中，你可以通过 `import.meta.env` 获取环境变量，例如：

```
javascript

console.log(import.meta.env.VITE_API_BASE_URL); // 输出 https://api.example.com
console.log(import.meta.env.VITE_API_KEY);      // 输出 1234567890abcdef
console.log(import.meta.env.DB_PASSWORD);       // 输出 undefined
```

------

#### 1.5. **环境变量类型处理**

- 默认情况下，所有通过 `import.meta.env` 获取的变量都是字符串。
- 如果你需要布尔值或数字，必须手动转换，例如：

```
javascript

const isDebugMode = import.meta.env.VITE_DEBUG === 'true';
const timeout = Number(import.meta.env.VITE_TIMEOUT);
```

------

#### 1.6. **扩展变量**

如果你需要引用其他环境变量，可以使用 `dotenv-expand` 提供的功能：

```
dotenv

# .env
API_HOST=https://api.example.com
VITE_FULL_API_URL=$API_HOST/v1
```

在代码中，`import.meta.env.VITE_FULL_API_URL` 将解析为 `https://api.example.com/v1`。

------

#### 1.7. **Git 忽略 `.local` 文件**

确保将 `.env.local` 和 `.env.[mode].local` 文件添加到 `.gitignore` 中，避免敏感信息泄漏：

```
.gitignore

# 忽略所有本地 .env 文件
*.local
```

### 2.HTML 环境变量替换

​	Vite 还支持在 HTML 文件中替换环境变量。`import.meta.env` 中的任何属性都可以通过特殊的 `%ENV_NAME%` 语法在 HTML 文件中使用：

```
html

<h1>Vite is running in %MODE%</h1>
<p>Using data from %VITE_API_URL%</p>
```

​	如果环境变量在 `import.meta.env` 中不存在，比如不存在的 `%NON_EXISTENT%`，则会将被忽略而不被替换，这与 JS 中的 `import.meta.env.NON_EXISTENT` 不同，JS 中会被替换为 `undefined`

### 3.模式

默认情况下：

​	这个就是默认模式

1. **开发服务器（`vite` 或 `vite dev` 命令）** 运行在 **开发模式**（`development` 模式）。
2. **构建命令（`vite build`）** 运行在 **生产模式**（`production` 模式）。

例如，如果有一个 `.env.production` 文件，里面写着：

```
dotenv

# .env.production
VITE_APP_TITLE=My App
```

​	当你执行 `vite build` 时，这个文件会自动被加载，然后你可以在代码中通过 `import.meta.env.VITE_APP_TITLE` 来使用这个变量，比如用它来动态渲染页面标题。

#### 3.1如果想构建其他模式，比如预发布模式：

​	这个就是指定你要进入的模式

有时你可能需要构建一个非生产的版本（比如用于测试的**预发布模式**）。这时可以使用 `--mode` 参数指定模式。例如：

```
bash

vite build --mode staging
```

​	为了支持这个模式，需要创建一个 `.env.staging` 文件，写入对应的变量：

```
dotenv

# .env.staging
VITE_APP_TITLE=My App (staging)
```

​	这样，当你运行上述命令时，Vite 会加载 `.env.staging` 中的配置，而不是默认的 `.env.production`。

#### 3.2使用开发模式进行构建

​	这个就是自定义一种模式

​	通常 `vite build` 是为生产模式设计的，但你也可以通过创建一个自定义模式的 `.env` 文件并使用 `--mode` 来让它加载非生产模式的配置文件。例如，创建一个 `.env.testing` 文件：

```
dotenv

# .env.testing
NODE_ENV=development
```

然后运行以下命令：

```
bash

vite build --mode testing
```

​	这样你就能构建一个**基于开发环境变量的应用**。

### 4.NODE_ENV 和 模式

​	直接把NODE_ENV理解成真正的模式就行，因为模式可以由我们自己设置，但是NODE_ENV只有那几个

### 

























# 补充

## 一、build.lib

开发库的时候可能用，build.lib只是补充，原文档没这些

​	[build.lib](https://cn.vitejs.dev/config/build-options.html#build-lib)

- **类型：** `{ entry: string | string[] | { [entryAlias: string]: string }, name?: string, formats?: ('es' | 'cjs' | 'umd' | 'iife')[], fileName?: string | ((format: ModuleFormat, entryName: string) => string), cssFileName?: string }`
- **相关内容：** [库模式](https://cn.vitejs.dev/guide/build.html#library-mode)

​	以库的形式构建。`entry` 是必需的，因为库不能使用 HTML 作为入口（可以以js、ts作为入口）。`name` 是暴露的全局变量，当 `formats` 包括 `'umd'` 或 `'iife'` 时必须使用。默认的 `formats` 为 `['es'、'umd']`，如果使用多个入口，则为 `['es'、'cjs']`。

​	`fileName` 是软件包输出文件的名称，默认为 `package.json` 中的 `"name"`。它也可以定义为以 `format` 和 `entryName` 为参数的函数，并返回文件名。

​	如果软件包导入了 CSS，`cssFileName` 可用于指定 CSS 输出文件的名称。如果设置为字符串，则默认值与 `fileName` 相同，否则也会返回到 `package.json` 中的 `"name"`。

```
import { defineConfig } from 'vite'

export default defineConfig({
  build: {
    lib: {
      entry: ['src/main.js'],
      fileName: (format, entryName) => `my-lib-${entryName}.${format}.js`,
      cssFileName: 'my-lib-style',
    },
  },
})
```

## 二、base

​	这玩意儿是设置公共基础路径的

### 1. **开发模式**：

​	在开发时，如果你访问项目中的某个资源，比如 `import '/babytian'`，Vite 会从 `./src` 目录下查找 `babytian` 文件。但是，如果这个文件被用作静态资源（如图片、脚本、样式等），Vite 会将其路径前缀加上 `/foo/`，例如：

- `src="/babytian"` 会变成 `src="/foo/babytian"`，确保资源在开发模式下以正确的路径加载。

### 2. **生产模式（打包后的 `dist` 文件夹）**：

​	当你运行 `vite build` 进行构建时，Vite 会将 `base: '/foo/'` 应用于所有静态资源的路径。因此：

- 假设你在项目中引用了 `/babytian`，在构建后的 `dist` 目录中，这个资源文件会被处理为 `/foo/babytian`，并且对应的静态文件会被放置在 `dist/foo/babytian`。

- 构建后的 HTML 文件中，所有指向静态资源的链接（如 JS、CSS、图片等）也会被自动加上 /foo/ 前缀。例如：

  ```
  <script src="/foo/main.js"></script>
  <link rel="stylesheet" href="/foo/styles.css">
  <img src="/foo/logo.png" />
  ```

## 三、import.meta的属性

**标准属性：**

- `import.meta.url`

**Vite 扩展：**

- `import.meta.env`：就是环境变量

  内置变量：

  - `MODE`：运行模式（如 `development` 或 `production`）。
  - `BASE_URL`：部署应用时的基本 URL。他由[`base` 配置项](https://cn.vitejs.dev/config/shared-options.html#base)决定。
  - `DEV`：是否是开发环境（布尔值）。
  - `PROD`：是否是生产环境（布尔值）（使用 `NODE_ENV='production'` 运行开发服务器或构建应用时使用 `NODE_ENV='production'` ）。
  - `SSR`：应用是否运行在 [server](https://cn.vitejs.dev/guide/ssr.html#conditional-logic) 上。

  用户自定义环境变量：

  - 需要以 `VITE_` 为前缀，定义在 `.env` 文件中。

    ```
    console.log(import.meta.env.MODE); // 'development'
    console.log(import.meta.env.VITE_CUSTOM_VAR); // 用户自定义变量
    ```

- `import.meta.glob`

- `import.meta.globEager`

- `import.meta.hot`

