### 一、安装以及使用

​	1.在**已经存在**的项目中使用`yarn add vue-router@4` 即可

​	2.如果要新创建一个项目则运行`yarn create vue`即可

​        	<img src="D:\Web学习\前端\assets\image-20241019191642734.png" alt="image-20241019191642734" style="zoom:67%;" />

```js
	`import { createRouter } from 'vue-router'`
```

### 二、入门

​	      Vue Router 基于 Vue 的组件系统构建，你可以通过配置**路由**来告诉 Vue Router 为每个 URL 路径显示哪些组件

​		先看如下一个例子： 

```HTML
<template>
    <h1>Hello App!</h1>
    <p>
      <strong>Current route path:</strong> {{ $route.fullPath }}
    </p>
    <nav>
      <RouterLink to="/">Go to Home</RouterLink>
  	  <RouterLink to="/about">Go to About</RouterLink>
    </nav>
    <main>
   		<RouterView />
	</main>
</template>
```


​	           <img src="D:\Web学习\前端\assets\image-20241019194447041.png" alt="image-20241019194447041" style="zoom:67%;" />   



 #### 	1.  创建路由器实例（当你添加了name属性就是一个命名路由）

使用`createRouter()`（前提是要import一下，通常也要用`createMemoryHistory`） 所以完整的导入是`import { createMemoryHistory, createRouter } from 'vue-router'`，例子如下:

```js

import HomeView from './HomeView.vue'
import AboutView from './AboutView.vue'

const routes = [
  { path: '/', component: HomeView },
  { path: '/about', component: AboutView },
]

const router = createRouter({
  history: createMemoryHistory(),
  routes,
})
```

​	但是当你写好了上面的代码，你需要将定义好的路由的那个常量**导出**否则不能被main.js组件使用

​	                                         `export default router`

​	routes就是定义的一组路由（还有其他可以设置的路由选项），使用了`createMemoryHistory()`可以忽略域名（就是访问这个网页的网址）直接用相对路径来定位组件，下面两个都可以实现（后面说）：

​				（`createWebHistory()``createWebHashHistory()`）

#### 		2.注册路由器插件

​	     <img src="D:\Web学习\前端\assets\image-20241019201541443.png" alt="image-20241019201541443" style="zoom:67%;" />

​                  **use一定要在Mount前面调用！！！！！**

#### 	3.访问路由器和当前路由

​		`useRouter()` 和 `useRoute()` 来访问路由器实例和当前路由。

​		`useRouter()`:  直接访问的就是 `router` 实例本身，这个实例包含导航、路由守卫等方法，能够执行像					  跳转（`push`、`replace`）等操作

​		<img src="D:\Web学习\前端\assets\image-20241019213938291.png" alt="image-20241019213938291" style="zoom:67%;" />

​		`useRoute()` ：访问的是`router` 常量的**详细信息**获取当前的路由对象（即当前 URL 对应的路由），包 					  含路径、参数、查询参数等，就是从router中抽取出来信息

​				<img src="D:\Web学习\前端\assets\image-20241019213949356.png" alt="image-20241019213949356" style="zoom:50%;" />

​		`router.push` 和所有其他导航方法都会返回一个 *Promise*

##### 		1.query(返回?后的）

​			①  `route.query`返回的是地址的？后面的东西，以js对象的方法返回，比如：		

​				https://example.com/users?search=vue&page=1这个URL

​			当你对这个URL使用**route.query**的时候就是返回，因为？后面是查询字符串，**query**要的就是查询

​				<img src="D:\Web学习\前端\assets\image-20241027111233645.png" alt="image-20241027111233645" style="zoom:67%;" />

​			②  query支持默认值（当url没有查询参数的时候）

​				如图所示：	

​	            	<img src="D:\Web学习\前端\assets\image-20241027111723051.png" alt="image-20241027111723051" style="zoom:60%;" />

##### 		2.params(返回:后的)

​			`route.params` 返回的是一个对象(跟**query**一样），其中包含与当前路由匹配的动态路由参数。动态路由参数是指在路由		路径中使用冒号（`:`）定义的部分，例如 `/user/:username` 中的 `username`。

<img src="D:\Web学习\前端\assets\image-20241027112339735.png" alt="image-20241027112339735" style="zoom:60%;" />

##### 	3.路由元信息（meta)

​		这个是一个属性，再创建路由的时候加进去的，访问的时候就用`route.meta`就可以

#### 4.route的属性

##### 4.1 **`fullPath`**

- **类型**：`string`

- **含义**：当前路由的完整路径，包括路径、查询参数和哈希部分。

- 示例：

  ```
  console.log(route.fullPath) // "/about?name=John#section"
  ```

##### 4.2 **`path`**

- **类型**：`string`

- **含义**：当前路由的路径部分（不包括查询参数和哈希）。

- 示例：

  ```
  console.log(route.path) // "/about"
  ```

##### 4.3 **`query`**

- **类型**：`Record<string, string | string[]>`

- **含义**：包含当前路由的查询参数（`?` 后面的部分），是一个对象。

- 示例：

  ```
  //URL: "/about?name=John&age=30"
  console.log(route.query) // { name: "John", age: "30" }
  ```

##### 4.4 **`hash`**

- **类型**：`string`

- **含义**：当前路由的哈希部分（`#` 后面的部分）。

- 示例：

  ```
  // URL: "/about#section"
  console.log(route.hash) // "#section"
  ```

##### 4.5 **`name`**

- **类型**：`string | null`

- **含义**：当前路由的名字（如果路由是命名路由）。

- 示例：

  ```
  // 定义的路由：{ path: '/about', name: 'about' }
  console.log(route.name) // "about"
  ```

##### 4.6 **`params`**

- **类型**：`Record<string, string>`

- **含义**：当前路由的动态参数（路径参数）。

- 示例：

  ```
  // 定义的路由：{ path: '/user/:id', name: 'user' }
  // URL: "/user/123"
  console.log(route.params) // { id: "123" }
  ```

##### 4.7 **`matched`**

- **类型**：`RouteRecordNormalized[]`

- **含义**：包含当前路由匹配到的路由记录数组（从根到当前路由依次匹配的所有路由）。

- 示例：

  ```
  route.matched.forEach(record => console.log(record.path))
  ```

##### 4.8 **`meta`**

- **类型**：`Record<string, any>`

- **含义**：当前路由的元信息（从路由记录的 `meta` 属性继承而来）。

- 示例：

  ```
  // 定义的路由：{ path: '/about', meta: { requiresAuth: true } }
  console.log(route.meta.requiresAuth) // true
  ```

##### 4.9 **`redirectedFrom`**

- **类型**：`RouteLocation | undefined`

- **含义**：如果当前路由是通过重定向到达的，则此属性指向重定向前的路由信息。

- 示例：

  ```
  console.log(route.redirectedFrom) // 重定向前的路由
  ```

#### 5.router的属性及方法

​	<img src="D:\Web学习\前端\assets\image-20241117143955702.png" alt="image-20241117143955702" style="zoom:67%;" />

##### 1. **主要属性**

###### 1.1 `currentRoute`

- 类型：`Ref<RouteLocationNormalizedLoaded>`

- 含义：表示当前活动的路由，是一个响应式对象。

- 示例：

  ```
  console.log(router.currentRoute.value.fullPath) // 获取当前路由的完整路径
  console.log(router.currentRoute.value.query)    // 获取当前路由的查询参数
  console.log(router.currentRoute.value.name)     // 获取当前路由的名字
  ```

`router.currentRoute.value` 和 `route` 是等价的

###### 1.2 `options`

- 类型：`RouterOptions`

- 含义：包含初始化路由时的配置，比如 `routes` 数组、`history` 模式等。

- 示例：

  ```
  console.log(router.options.routes) // 获取初始化时定义的所有路由
  ```

------

##### 2. **主要方法**

###### 2.1 `push(location)`

- 用途：导航到指定的路由，同时新增一条浏览记录。

- 示例：

  ```
  router.push({ path: '/about', query: { name: 'John' } })
  ```

###### 2.2 `replace(location)`

- 用途：导航到指定的路由，但不会新增浏览记录，而是替换当前记录。

- 示例：

  ```
  router.replace('/profile')
  ```

###### 2.3 `addRoute(route)`

- 用途：动态添加路由。

- 示例：

  ```
  router.addRoute({ path: '/dashboard', component: Dashboard })
  ```

###### 2.4 `removeRoute(name)`

- 用途：动态移除通过 `addRoute` 添加的路由。

- 示例：

  ```
  router.removeRoute('dashboard')
  ```

###### 2.5 `hasRoute(name)`

- 用途：检查是否存在指定名字的路由。

- 示例：

  ```
  console.log(router.hasRoute('dashboard')) // true 或 false
  ```

###### 2.6 `resolve(location)`

- 用途：解析目标路由的完整信息（类似模拟路由跳转后会得到什么）。

- 示例：

  ```
  const resolved = router.resolve({ path: '/about' })
  console.log(resolved.fullPath) // 输出解析后的完整路径
  ```

###### 2.7 `beforeEach(guard)`

- 用途：添加全局前置导航守卫。

- 示例：

  ```
  router.beforeEach((to, from, next) => {
    if (!isLoggedIn) next('/login')
    else next()
  })
  ```

###### 2.8 `beforeResolve(guard)`

- 用途：添加全局解析守卫，在所有 `beforeEach` 完成后调用。

- 示例：

  ```
  router.beforeResolve((to, from) => {
    console.log('在解析路由之前执行')
  })
  ```

###### 2.9 `afterEach(guard)`

- 用途：添加全局后置钩子，无论导航是否成功都会调用。

- 示例：

  ```
  router.afterEach((to, from) => {
    console.log(`从 ${from.path} 到 ${to.path}`)
  })
  ```

###### 2.10 `isReady()`

- 用途：返回一个 Promise，表示路由器是否初始化完成。

- 示例：

  ```
  router.isReady().then(() => {
    console.log('路由已准备好')
  })
  ```

### 三、动态路由匹配

#### 	  		1.   带参数的动态路由匹配

​		就是加上   **：**就可以，因为有些时候用户不一样，就导致用户id不一样，但是我们想给他们都展示一样的页面，使用了这个就可以实现，例子如下:

	    import User from './User.vue'
	     // 这些都会传递给 `createRouter`
		const routes = [
	       // 动态字段以冒号开始
	      { path: '/users/:id', component: User },
	    ]

​             现在像 `/users/johnny` 和 `/users/jolyne` 这样的 URL 都会映射到同一个路由，因为用了：进行匹配，	当这个路由被匹配的时候会返回一个params属性，这个属性会包含：后面具体的值，要用`route.params`才  	可以获取，e.g.

```html
<template>
  <div>
    <!-- 当前路由可以通过 $route 在模板中访问 -->
    User {{ $route.params.id }}
  </div>
</template>
```
  如果你的路由是'/users/:babytian，那么 **$route.params.id**这个的值就是babytian，因为你动态路由定义	的时候**冒号后面就是id**，所以要用id访问

​		       <img src="D:\Web学习\前端\assets\image-20241019211218474.png" alt="image-20241019211218474" style="zoom:67%;" />  

#### 	2.  响应路由参数的变化

​		  说白了就是用侦听器（watch），因为你复用的时候不会再次执行生命周期钩子，但是你又要对这个变	  化进行响应那就只能使用侦听器

​		<img src="D:\Web学习\前端\assets\image-20241019212139185.png" alt="image-20241019212139185" style="zoom:67%;" />

#### 	3.  捕获所有路由或 404 Not found 路由

​		<img src="D:\Web学习\前端\assets\image-20241019213519979.png" alt="image-20241019213519979" style="zoom:67%;" />

​		如上所说**router**是实例但是**route**是详细信息，所以使用正则表达式匹配的东西放到的是**route**.params里面（具体还是看语句怎么写），但是你要用的时候就是**router**.push，push方法是把router实例的route取出来进行处理然后创建一个新的route把他放进去，让这个原本的router跟原来的route匹配上

### 四、路由的匹配语法

​	注意最重要的一点：路由都是从上到下匹配的，也就是在路由数组是从前往后匹配的，所以使用 `{ path: '/:pathMatch(.*)', component: NotFoundComponent }`来创建一个404页面的时候，要把他放在路由数组的最后一项

#### 	1.在参数中自定义正则

​		上一节讲了用：来使用动态路由，那么动态路由具体匹配什么类型，就可以使**正则表达式**，如下所示

<img src="D:\Web学习\前端\assets\image-20241020145808579.png" alt="image-20241020145808579" style="zoom:67%;" />

为什么要用两个反斜杠\\\\d，是因为单个反斜杠会被认为是**转义字符**，而不是反斜杠，而且一定要写在**括号里面**

#### 2.  可重复的参数

​	其实也是正则表达式的符号，跟上面相比就是不用括号，然后就会作用到路径上去

<img src="D:\Web学习\前端\assets\image-20241020192602139.png" alt="image-20241020192602139" style="zoom:67%;" />

​	reslove是**解析路由**，params为路由中的**动态参数传值**，name是为了**匹配到对应的路由**

​	只有用push的时候才会跳转

​       当然，也可以添加正则表达式然后两个一起用

​      		<img src="D:\Web学习\前端\assets\image-20241020162330303.png" alt="image-20241020162330303" style="zoom:67%;" />

#### 3.  Sensitive 与 strict 路由配置

​	默认情况路由**不区分大小写**且**不区分尾部是否带斜线**可用`strict`和`sensitive`区分，既可以应用在整个全局路由上，又可以应用于当前路由上

​            	<img src="D:\Web学习\前端\assets\image-20241020165953578.png" alt="image-20241020165953578" style="zoom:67%;" />

<img src="D:\Web学习\前端\assets\image-20241020170040863-1729427248870-4.png" alt="image-20241020170040863" style="zoom:67%;" />

#### 4.可选参数

​	用？让某一个参数变为可选**（没有问号就是必填项）**

<img src="D:\Web学习\前端\assets\image-20241020170240770.png" alt="image-20241020170240770" style="zoom:67%;" />



### 五、嵌套路由

#### 1.	基本嵌套思路

​	当你在根组件(app.vue)使用了`<router-view>`，那么顶层路由匹配的组件就会渲染到这里面来（就是用了router-link那个标签的），但是一个被渲染的组件也应该可以渲染其他的组件，那么这个时候就涉及到了嵌套路由

​	但是同样的，你要使用嵌套路由就一定要像搞一个顶层路由那样也定义一下，使用**children**，e.g.

​	<img src="D:\Web学习\前端\assets\image-20241020200159185.png" alt="image-20241020200159185" style="zoom:67%;" />

​        当然你也可以提供一个**空的嵌套路径**

​		<img src="D:\Web学习\前端\assets\image-20241020202538360.png" alt="image-20241020202538360" style="zoom:67%;" />

​        **空的嵌套路径会更加的符合所需要的路由结构**

#### 2.嵌套的命名路由

​	在处理[命名路由](https://router.vuejs.org/zh/guide/essentials/named-routes)时，**你通常会给子路由命名**：
​		<img src="D:\Web学习\前端\assets\image-20241020204158202.png" alt="image-20241020204158202" style="zoom:67%;" />

<img src="D:\Web学习\前端\assets\image-20241020204832276.png" alt="image-20241020204832276" style="zoom:67%;" />

 上图的意思是，只有当你用**命名路由**的名字来访问那这个父路由的时候（图中名字是user-parent），这个时候父路由的**空路径子路由**不会被渲染，但是你用路径来访问就会渲染

#### 3.省略父组件

​	<img src="D:\Web学习\前端\assets\image-20241020205044088.png" alt="image-20241020205044088" style="zoom:67%;" />

就是故意不给你父组件指定一个component，由于父路由没有指定路由组件，顶级的 `<router-view>` 将跳过父路由，而只使用相关子路由的组件。

**效果如图所示**（可以关联相关的子路由）

​	<img src="D:\Web学习\前端\assets\image-20241020205310120.png" alt="image-20241020205310120" style="zoom:67%;" />

### 六、命名路由

​	在创建路由的时候给路由加上一个`name`属性就行，然后就可以通过这个**name**属性里面的名字传递给`route-link`来访问这个路由的组件，你的name会跟path绑定，也就是你用的是name，其实会被编译器翻译成path，下面的例子你如果使用profile，就会被解析为/user/:username

​	<img src="D:\Web学习\前端\assets\image-20241021163305065.png" alt="image-20241021163305065" style="zoom:67%;" />

 如图所示，你用**命名路由**的名字，来访问这个组件，那么这个**to的前面就需要加上冒号**，然后用{}进行传递

​           	<img src="D:\Web学习\前端\assets\image-20241021165341973.png" alt="image-20241021165341973" style="zoom:67%;" />

​						[访问该实例](https://play.vuejs.org/#eNqtVVtP2zAU/itWNqlFauNNIB6iUMEQEps0NjH2tOzBtKY1JLZlO6VTlP++4+PcelnFwyRofe7fubaKCiZk/GyjJBKFVsaRiswNZ45faU1q8mRUQUbrko8yuaPwlRfK/LkV1sHXpGHeq9JxMzScGmT19t5xkMaUaR1vOb9VBe+kntgWXz2Cs06O1LbCTwvRW7knGnEm50paRwIYcrEFd1xlkpBVyCQ5lN74ZOJV0Nom5JcnCFRCM7dKyIiOJkSygsNzBZiBmivAI7l0SUipRvuhCfPge7uWHBiGZPctS0iLJv7T2/YutFFPIt+JjgUJPn7DZ32CtWg7PIZ/4BASg7txKE6gC1VKNx69gw6NTqJJ1HQK5iR1vNA52M+8Yrr6OLuD+AuCtbQpBQYK9Oy6NAZAhLI1KKuKvEc69jSp65Tqw/oh3V7f00P9MsdveOWiecE75DDNhXwhiVMXWVRttYbUWdRpE2xOZ0sHxq1v2jl/a5jQyZ042Mv/HKjvt2aGFTCXFWmnAsTcCMkAxw4SHIjG9E2AUtpUusWyFvyVUGCltBsFmJB2W/dHZCHWswdYLwJ/XiulnrNr323zcQeodthDuAHTgmm4aEqCH1zsrBHYLIISheyyqD9Nnp1FK+e0TSgtpX5ZxrBBtNe4PItP4w8Q07oBN+a2mD4a9erPzDN4bzY1iy5BiS742imV2ynT4l8h9hQvz+Pz+COU/pGCdyrkgm/Qt3ddw/5Cms7CLXsSy50k/dJDT8037QTcuq1kWZ6r1y/Ic6bkHdD5is9fDvCf7SZA/m44ZLfmg+QcM0vugvjmxx3fwLsTFmpRwlwdE95zq/LSYwxqn0q5ANgDPUT7GXsm5PLB3mwcl7ZNygPFaqA+NvL6SOo93NP4bFDF9sfh+LThtgxvkF80fyxxy/Ac7U9i/RcYNWrd)

​	**直接用路径**的例子如下：

​		<img src="D:\Web学习\前端\assets\image-20241021165459698.png" alt="image-20241021165459698" style="zoom:67%;" />

​	                                       [访问该实例](https://play.vuejs.org/#eNqFVVtv2zYU/itn6gArmC05btEHTXXTFcWyYZeiLfYy7UGWji02EsmRlOPA8H/fIambnaRD4Fg61++c7yN9DJqc8eirDpKANVIoA0coFOYG30kJJ9gq0cBs3+Is412AEq1B1Xmi2L+ObpvX+3IpI5+b8aFqSJ+rjANErcbQp/v3RrTchLMXlDa7CuZBl07YUoONrCl/bQPT6np9i3UtbLPv0phenVm6L3rQRgm+W79vlULeIQaZmypJ484HxyN87xzRtq3rj+SE08mViX2dlOf7vuAnh/I3xu/AiDdZEGfB+mdBz3ArGkzj0f9sRr4hy5D2zr49ykvjvmdqeTmv9RfDe4i7uM6dxsNiaF9+l0+y+Ts2Qj3cMm3oa94Zfd0py4uBzYFPO6Br3ZPaGzpme9rtQGdxg2WUgOC6Y0PDG/jbjnL0vMAsnhEsQcU4UZaMbU/z8zC3x/PYsbcN/ueilaJW03nDoy1Y+VUkT+0nvHI9PVB6PJE8M44HN2iJ27yt+9q09ek+rFR1oZg0RM5FgmvboKlEqRP/BrATX4SDH171JgBD4CIvThXJVldhP7Y7J9DtxP4nxZKk+470cnFQVuseHh2TlTduWmMEh5uiZsUdSXPAcKlOH/hIZmfEjhODRtPaozNKjyiiGcqn75Ej0Pl3lMyHp2fFeMHnEB/SRia+ict6ep/GXBWV1UGHyGtgh5O1K0KvuC8T/duieoi6tLdvYUYg+rXTmKH3jLmeKoW0owLDI7h8IrnvfAKrIargxfQ/lA0LHjmr8w3W3X3w2dVMIGWchoH9ohEl1pFRrCE2fccsgCY/1Mh3piLjaknc+pujr3TOqedk0eSSrg/BiVU3WtY5dBYMks2CkRtrzoLKGKmTOG65vNtFtON4jLh5Fb2MlnFJJ2tijVA3i40S99rdV1ngNmtr31BQXOLeCFHrRS7Zcy0eBd68jl5H13HNNjFVjxkv8eBq94unMY0mQWzZ7mJIKwtWo/pTGkaCORs2p9+Z+1+dzagWB6BFhcXdE/av+uAhf1RI0+1xMpzJFWnOuz98/gMP9Dw4icW2puhvOD+hFnVrMfqwn1peEuxJnEP7i+OM8d0X/eFgkOt+KAt0FLIj8v03Rh/hvoxeTbaozUONOiq0/aGhX6w5aY1xn7cRqkSVwEoegMCyEl4sl8sf3d1H5RhfbATdKk0C10t5cHaZlyWBHSzUJeNUFtaQww/08Tenz65xSzf+NLJaTTuP5UcARVFMACSwpL9VVyE4/QesCg/V)

​	所有路由的命名**都必须是唯一的**。如果为多条路由添加相同的命名，路由器只会保留**最后**那一条。你可以在[动态路由](https://router.vuejs.org/zh/guide/advanced/dynamic-routing.html#Removing-routes)章节了解更多。（注意，名字重复遵守后来居上的原则）

### 七、编程式导航（用router.push()）

#### 	1.导航到不同的位置

​		本来是使用<router-link>标签来创建一个跳转这个组件的超链接，但是我们也可以使用router.push的方法，来实现在一个	组件内跳转到另外一个组件，有如下几个方式

```js
// 字符串路径
router.push('/users/eduardo')

// 带有路径的对象
router.push({ path: '/users/eduardo' })

// 命名的路由，并加上参数，让路由建立 url
router.push({ name: 'user', params: { username: 'eduardo' } })

// 带查询参数，结果是 /register?plan=private
router.push({ path: '/register', query: { plan: 'private' } })

// 带 hash，结果是 /about#team
router.push({ path: '/about', hash: '#team' })
```

​		<img src="D:\Web学习\前端\assets\image-20241027115927286.png" alt="image-20241027115927286" style="zoom:67%;" />

​		**path**不可以跟**params**一起使用，**params**会被忽略

#### 	2.params支持传递多种类型

​		字符串，数字，数组，”“（空字符串）,null都可以，下面是一个例子

```
<script setup>
import { useRouter } from 'vue-router';

const router = useRouter();

const goToUser = () => {
  // 传递字符串参数
  router.push({ name: 'user', params: { username: 'eduardo' } });
};

const goToUserWithNumber = () => {
  // 传递数字参数
  router.push({ name: 'user', params: { username: 'eduardo', age: 30 } });
};

const goToUserWithArray = () => {
  // 传递数组参数（需要适当处理）
  router.push({ name: 'user', params: { username: 'eduardo', hobbies: ['reading', 'gaming'] } });
};

const goToUserWithOptional = () => {
  // 传递可选参数，设置为 null 以移除 age
  router.push({ name: 'user', params: { username: 'eduardo', age: null } });
};
</script>
```

​	这个是单文件组件里面的呀一部分，**传递数组是隐式的**，包含在**router.params**里面，可以通过**router.params.hobbies**来访问，但是他不会被包含在URL里面被看见，**传递null指忽略**，在URL直接就不会显示是null的那个参数

#### 	3.替换当前位置(router.replace)

​	这涉及到vue router的**history**，也就是路由访问历史以栈的方式存储，当用户访问新的路由的时候就会往这个历史栈添加一条记录

当使用router.push的时候就会往里面添加，但是如果是**router.replace**就会替换掉刚刚加进去的那个记录，如图所示：

​      	<img src="D:\Web学习\前端\assets\image-20241027142237506.png" alt="image-20241027142237506" style="zoom:67%;" />

​		

​	有三种方法，一个是用<router-link>的replace属性，一个是设置`replace: true`，最后一个就是直接用replace方法

#### 4.横跨历史(类似于window.history.go(n))

```
// 向前移动一条记录，与 router.forward() 相同
router.go(1)

// 返回一条记录，与 router.back() 相同
router.go(-1)

// 前进 3 条记录
router.go(3)

// 如果没有那么多记录，静默失败
router.go(-100)
router.go(100)
```

​	负的表示往后，正的表示往前，就是跟word执行撤销操作以及浏览器回退页面一样

### 八、命名视图(跟前面具名插槽(slot)像像的)

​	有时候想同时 (同级) 展示多个视图，而不是嵌套展示，如果`router-view` 没有设置名字，那么默认为 `default`。

```
   <router-view class="view left-sidebar" name="LeftSidebar" />
   <router-view class="view main-content" />
   <router-view class="view right-sidebar" name="RightSidebar" />
```

​	下面是个具体例子，将根据例子来进行讲解：***一定是components一定要加S！！！！！***![image-20241027150109560](D:\Web学习\前端\assets\image-20241027150109560.png)

​	`router.js`通过在components设置**default, a, b**来对应每一个组件（这个项目里面有Third.vue、Second.vue、First.vue)，这三个就是命名视图的名字，左边的根组件使用的时候都是用的这三个名字，没有显示指出name那么就是default，通过**命名视图**来实现`router-view` 的同级使用，就可以给**不同的组件放到同一个页面上**（当然，vue最基本的功能也可以，这个是另一种方法）

#### 	嵌套命名视图（就是嵌套路由+命名视图）

​		<img src="D:\Web学习\前端\assets\image-20241027152411632.png" alt="image-20241027152411632" style="zoom:67%;" />

<img src="D:\Web学习\前端\assets\image-20241027152425854.png" alt="image-20241027152425854" style="zoom:67%;" />

​			就是在children里面使用命名视图，不想其他<route-view>展示组件，就直接不给他匹配组件就行，不使用他的命名

### 九、重定向和别名

#### 	1.重定向（自动跳转到别的路由）

​		重定向路由实例：

​		①静态

```js
const routes = [{ path: '/home', redirect: '/' }]      访问/home自动跳转到/
const routes = [{ path: '/home', redirect: { name: 'homepage' } }]   访问/home自动跳转到名称为homepage的路由
```

​		②**动态**返回重定向目标

```
const routes = [
  {
    // /search/screens -> /search?q=screens
    path: '/search/:searchText',
    redirect: to => {
      // 方法接收目标路由作为参数
      // return 重定向的字符串路径/路径对象
      return { path: '/search', query: { q: to.params.searchText } }
    },
  },
  {
    path: '/search',
    // ...
  },
]
```

​		当用户访问 `/search/screens` 时，路由会被重定向到 `/search?q=screens`。**通过`to`来获取当前路由然后进行操作**

​		请注意，**[导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)并没有应用在跳转路由上，而仅仅应用在其目标上**。在上面的例子中，在 `/home` 路由中添加 `beforeEnter` 		守卫不会有任何效果。

#### 	2.相对重定向

```
const routes = [
  {
    // 将总是把/users/123/posts重定向到/users/123/profile。
    path: '/users/:id/posts',
    redirect: to => {
      // 该函数接收目标路由作为参数
      // 相对位置不以`/`开头
      // 或 { path: 'profile'}
      return 'profile'
    },
  },
]
```

#### 	3. 别名（alias）

​		重定向是指当用户访问 `/home` 时，URL 会被 `/` 替换，然后匹配成 `/`。那么什么是别名呢？**将 `/` 别名为 `/home`，意味着当用户访问 `/home` 时，URL 仍然是 `/home`，但会被匹配为用户正在访问 `/`。**

​		但是注意，别名前面如果加了**"/"**就表明是一个**绝对路径**，如下例子：

​	<img src="D:\Web学习\前端\assets\image-20241027160815845.png" alt="image-20241027160815845" style="zoom:67%;" />

 问题：为什么不用**正则表达式**匹配呢？ 如果用因为这里我们只想让**这三个路由**呈现相同的东西，而正则表达式会给你**匹配所有满足的**

带参数的路由的别名：**（一定要用绝对别名包含参数！！！！）**

```js
const routes = [
  {
    path: '/users/:id',
    component: UsersByIdLayout,
    children: [
      // 为这 3 个 URL 呈现 UserDetails
      // - /users/24
      // - /users/24/profile
      // - /24
      { path: 'profile', component: UserDetails, alias: ['/:id', ''] },
    ],
  },
]
```

### 十、路由组件传参

​	我们可以通过设置 `props: true` 来配置路由将 `id` 参数作为 prop 传递给组件，因为useroute()跟路由紧密耦合，所以有些时候你要获得动态路由到底是什么值，你要写的代码会很繁杂比如，`route.params.id`，但是你用了props就会很智能，如下所示：

​	<img src="D:\Web学习\前端\assets\image-20241027164225969.png" alt="image-20241027164225969" style="zoom:67%;" />

​	当你使用**:123**来访问到这个组件的时候，这个**123**会自动传递给**id**这个props（因为你在routes里设置了props: true）

#### 	1.布尔模式	

​	当你设置`props: true`的时候，`route.params` 将被设置为组件的 props进行传递

#### 2.命名视图

​	你要对命名视图（也就是components: {default: User, sidebar: Sidebar}）这种东西设置props就不可以props：true

​	你要为每一个都单独设置

```
const routes = [
  {
    path: '/user/:id',
    components: { default: User, sidebar: Sidebar },
    props: { default: true, sidebar: false }
  }
]
```

#### 3.对象模式

```
const routes = [
  {
    path: '/promotion/from-newsletter',
    component: Promotion,
    props: { newsletterPopup: false }
  }
]
```

​	每次访问`/promotion/from-newsletter`这个路由的时候，都会传递一个`newsletterPopup: false`作为传递的props，而不再把`route.params`作为props传递，**如果你想传递其他参数就要如下操作**：

```
const routes = [
  {
    path: '/promotion/:id', // 例如，这里有个动态参数 id
    component: Promotion,
    props: route => ({ newsletterPopup: false, id: route.params.id })
  }
];
```

​	把你要传的也变成一个对象（就是下面的函数模式）

#### 4.函数模式（就是上面对象模式的第二段代码）

​	<img src="D:\Web学习\前端\assets\image-20241027165711120.png" alt="image-20241027165711120" style="zoom:67%;" />

#### 5.通过 RouterView（具体后面再讲，这也是个知识点）

​	<img src="D:\Web学习\前端\assets\image-20241027170053335.png" alt="image-20241027170053335" style="zoom:67%;" />

### 十一、活动链接（方便应用css样式）

​	`router-link-active和router-link-exact-active`

​	这两个区别看如下一个例子：

​	<img src="D:\Web学习\前端\assets\image-20241104211115125.png" alt="image-20241104211115125" style="zoom:67%;" />

​	这其实就是vue在你精确访问这个路径的时候，会给对应的`router-link`加上`router-link-active`**或者**`router-link-exact-active`，所以我们可以直接用**选择器选中这两个属性，然后添加样式**

#### 	配置类名

​	因此，为了方便且区分不同的地方的组件激活时的样式，我们就可以对他们使用**配置类名**。通过设置 `RouterLink` 组件的两个 `props` 属性——`activeClass` 和 `exactActiveClass`，可以自定义当前链接处于“活跃”或“精确活跃”状态时的类名。

```js
<RouterLink
  activeClass="border-indigo-500"
  exactActiveClass="border-indigo-700"
  ...
>
```

​	也可以全局设置，上面的是局部的，专门对这个路径的

```js
const router = createRouter({
  linkActiveClass: 'border-indigo-500',
  linkExactActiveClass: 'border-indigo-700',
  // ...
})
```

​	也就是在创建路由实例的时候应用这个，当你没有给<router-link>单独创建一个类名的时候他就会被应用这个全局类名，当你创建了局部的时候就会把这个全局的类名给覆盖了。

### 十二、不同的历史记录模式

​	[原文档](https://router.vuejs.org/zh/guide/essentials/history-mode.html)

​	不懂那种看哪种

​	<img src="D:\Web学习\前端\assets\image-20241105194007434.png" alt="image-20241105194007434" style="zoom:67%;" />

Hash模式：通过加一个用户看不见的`#`来实现

Memory 模式：没有历史记录，不可实现回退和前进

**HTML5 模式**：路由会看起来很正常，所以会被浏览器错误理解成，路由的路径就是真实的文件路径，然后导致变成404，所以要添加上			回退路由(在服务器里面添加回退路由，分用的是`Apache`还是`Nginx`)

​	注意最重要的一点：路由都是从上到下匹配的，也就是在路由数组是从前往后匹配的，所以使用 `{ path: '/:pathMatch(.*)', component: NotFoundComponent }`来创建一个404页面的时候，要把他放在路由数组的最后一项（当你使用HTML5模式来记录历史记录，并且进行了对应的服务器配置来防止出现某些错误（具体见文档）这个时候就要自己创建一个404页面，就是上面那句话



------



# 进阶：

### 十三、导航守卫

<img src="D:\Web学习\前端\assets\image-20241112200022019.png" alt="image-20241112200022019" style="zoom:67%;" />

五种守卫的调用顺序，[原文档](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)

`to` 和 `from` 是 **`route` 对象**

#### 	1.全局前置守卫

**常见用途**：

- 路由级别的守卫，用于更细粒度的控制（例如：在进入某个页面之前做一些验证或权限控制）。
- 在路由级别做权限验证、重定向等。

```ts
const router = createRouter({ ... })

router.beforeEach((to, from) => {
  // ...
  // 返回 false 以取消导航
  return false
})
```

​	to是**即将跳转**的路由

​	from是用户的**当前路由**

​	使用实例如下：

```
router.beforeEach(async (to, from) => {
   if (
     // 检查用户是否已登录
     !isAuthenticated &&
     // 避免无限重定向
     to.name !== 'Login'
   ) {
     // 将用户重定向到登录页面
     return { name: 'Login' }
   }
})
```

举个例子：

1. 用户在首页 `/home` 时点击“关于”按钮，准备跳转到 `/about` 页面。
   - 触发导航守卫，`from` 是 `/home`，`to` 是 `/about`。
2. 如果全局前置守卫放行（未返回 `false`），页面就会导航到 `/about`。
3. 用户在 `/about` 再次点击跳转到登录页 `/login`。
   - 此时，`from` 是 `/about`，而 `to` 是 `/login`。
4. 如果守卫逻辑中要求未登录用户只能跳转到登录页，那么守卫会阻止任何非登录页的访问，自动返回 `{ name: 'Login' }`，使页面导航到 `/login`。

 **为什么要用async呢？**因为验证是否登录等其他需要等待结果才能继续的步骤，都要等他返回了才能继续所以要用async，这样才能在函数里面用await来实现异步

```
router.beforeEach(async (to, from) => {
  // 异步检查用户身份
  const isAuthenticated = await checkUserAuthentication();
  
  // 如果未登录并且目标页面不是登录页，则重定向到登录页
  if (!isAuthenticated && to.name !== 'Login') {
    return { name: 'Login' };
  }

  // 已登录，允许导航
  return true; //vue router4可以使用 true来表示继续
});
```

#### 2.全局解析守卫

​	`router.beforeResolve`注册

**常见用途**：

- 用于做最后的导航守卫处理。
- 比 `beforeEach` 更精确，它在所有异步组件解析之后执行

​	解析守卫刚好会在导航被最终确认之前、**所有组件内守卫和异步路由组件被解析之后**调用

#### 3.全局后置守卫

​	`afterEach`

​	它们对于分析、更改页面标题、声明页面等辅助功能以及许多其他事情都很有用。

**常见用途**：

- 记录页面访问日志、分析路由跳转等。
- 页面加载完毕后执行某些副作用操作。

**日志记录**：记录用户访问了哪些页面，生成访问日志。

**页面统计**：统计每个页面被访问的次数。

**执行副作用**：例如更新页面状态、进行一些异步操作（如通知发送等）。

```
router.afterEach((to, from) => {
  sendToAnalytics(to.fullPath)
})
```

​	虽然跟前置守卫一样都有to和false但是他们只可以使用，**不能干涉导航本身**

​	他的第三个参数是failure，用于检测**全局导航故障**

```
router.afterEach((to, from, failure) => {
  if (!failure) sendToAnalytics(to.fullPath)
})
```



#### 4.在守卫内的全局注入

​	学了pinia再看

#### 5.路由守卫

​	专门给路由用的守卫，正是因为路由独享，所以要跟创建路由的东西一起写，如下所示：

```ts
const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: (to, from) => {
      // reject the navigation
      return false
    },
  },
]
```

​	`beforeEnter` 守卫 **只在进入路由时触发**，不会在 `params`、`query` 或 `hash` 改变时触发。例如，从 `/users/2` 进入到 `/users/3` 或者从 `/users/2#info` 进入到 `/users/2#projects`。它们只有在 **从一个不同的** 路由导航时，才会被触发。

​	也可以传递**函数数组**，当进入路由时自动调用

```
function removeQueryParams(to) {
  if (Object.keys(to.query).length) //这个里面的object.keys指的是获取js对象的键值对的键
    return { path: to.path, query: {}, hash: to.hash }
}

function removeHash(to) {
  if (to.hash) return { path: to.path, query: to.query, hash: '' }
}

const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: [removeQueryParams, removeHash],
  },
  {
    path: '/about',
    component: UserDetails,
    beforeEnter: [removeQueryParams],
  },
]
```

​	当配合[嵌套路由](https://router.vuejs.org/zh/guide/essentials/nested-routes.html)使用时，父路由和子路由都可以使用 `beforeEnter`如果放在父级路由上，路由在具有相同父级的子路由之间移动时

它不会被触发，不懂就看原文档

#### 6.组件内的守卫（

​	就三个守卫API可以在组件里面用

- `beforeRouteEnter`：

  - **常见用途**：
    - 在组件被创建之前进行数据获取。
    - 例如：根据路由参数加载数据，在组件实例创建之前完成数据加载。

- `beforeRouteUpdate`：	

  - **常见用途**：
    - 用于处理路由参数的变化，不需要离开当前页面时更新组件状态。
    - 适用于需要响应路由变化的场景，例如：更新组件中的数据（例如用户 ID 变化时重新加载数据）

- `beforeRouteLeave`：

  - **常见用途**：
    - 在用户离开页面时执行清理操作。
    - 例如：保存表单数据、停止定时器、取消订阅、提示用户是否丢失未保存的更改。

  **组合式API**的写法：

 - `onBeforeRouteEnter`

 - `onBeforeRouteUpdate`

 - `onBeforeRouteLeave`

​	 这三个里面只有`beforeRouteEnter`支持回调“next”，因为这个会在**组件被加载之前**出发，后面两个都是在组件加载完之后触发的，他们已经可以访问this了

```
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```

通过传入next来进行回调，等组件加载完了回调回来使用

组合式API的组件内守卫：其实就是一样用，只是加了个on以及改成箭头函数

```
<script setup>
import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router'
import { ref } from 'vue'

// 与 beforeRouteLeave 相同，无法访问 `this`
onBeforeRouteLeave((to, from) => {
  const answer = window.confirm(
    'Do you really want to leave? you have unsaved changes!'
  )
  // 取消导航并停留在同一页面上
  if (!answer) return false
})

const userData = ref()

// 与 beforeRouteUpdate 相同，无法访问 `this`
onBeforeRouteUpdate(async (to, from) => {
  //仅当 id 更改时才获取用户，例如仅 query 或 hash 值已更改
  if (to.params.id !== from.params.id) {
    userData.value = await fetchUser(to.params.id)
  }
})
</script>
```

#### 7.导航解析流程

​	<img src="D:\Web学习\前端\assets\image-20241112210723359.png" alt="image-20241112210723359" style="zoom:67%;" />

### 十四、路由元信息

​		就是在定义一个路由的时候加上**`meta`属性**，当你访问具有这个属性的路由的时候，你可以在匹配的组件里面使用`route.meta`来访问到这个路由元信息，当然，你使用导航守卫的时候可以使用`to.meta  from.meta`（hash query params也行）

```
const routes = [
  {
    path: '/posts',
    component: PostsLayout,
    meta: { requiresAuth: false },
    children: [
      {
        path: 'new',
        component: PostsNew,
        meta: { requiresAuth: true },
      },
      {
        path: ':id',
        component: PostsDetail,
        meta: { requiresAuth: false },
      },
    ],
  },
];
```

​	对于 `/posts/new` 路径，父路由 `meta` 为 `{ requiresAuth: false }`，而子路由 `meta` 为 `{ requiresAuth: true }`。

​	`to.meta` 会合并这些 `meta` 字段，最终为 `{ requiresAuth: true }`（子路由的 `meta` 覆盖了父路由的 `meta`，当meta冲突的时候才会如此，不冲突则会合并成一个js对象）。

<img src="D:\Web学习\前端\assets\image-20241112221108115.png" alt="image-20241112221108115" style="zoom:70%;" />

​	所以你可以在导航守卫里面直接使用to.meta.XXX来访问当前路由的**某一个meta的具体值**，因为都会被包装成js对象`.XXX`来访问`XXX`这个键的值

#### 	以下是TS语言专属（可以直接添加到.ts   .d.ts文件中）

```
// 这段可以直接添加到你的任何 `.ts` 文件中，例如 `router.ts`
// 也可以添加到一个 `.d.ts` 文件中。确保这个文件包含在
// 项目的 `tsconfig.json` 中的 "file" 字段内。
import 'vue-router'

// 为了确保这个文件被当作一个模块，添加至少一个 `export` 声明
export {}

declare module 'vue-router' {
  interface RouteMeta {
    // 是可选的
    isAdmin?: boolean
    // 每个路由都必须声明
    requiresAuth: boolean
  }
}
```

### 十五、[数据获取](https://router.vuejs.org/zh/guide/advanced/data-fetching.html)

​	有时进入某个路由后，需要从服务器获取数据。例如，在渲染用户信息时，你需要从服务器获取用户的数据

以下两个方法用于从服务器获取数据：

- **导航完成之后获取**：先完成导航，然后在接下来的组件生命周期钩子中获取数据。在数据获取期间显示“加载中”之类的指示。

  使用侦听器（一般要设置成即时回调侦听器）侦听路由的某个参数的变化比如用户id等等，进行对应的异步操作

- **导航完成之前获取**：导航完成前，在路由进入的守卫中获取数据，在数据获取成功后执行导航。

​	可以在接下来的组件的 `beforeRouteEnter` 守卫中获取数据，当数据获取成功后只调用 **`next` 方法**（因为组件内的守卫只有他有组件加载完之后，回调的功能）

### 十六、RouteView

​	<router-view />实际上等于

```
<router-view v-slot="{ Component }">
  <component :is="Component" />
</router-view>
```

**`Component` 代表当前路由匹配的组件**，他会把当前组件装进去作为子组件的属性（类似于奥，不是真是子组件的属性）传给父组件，因为这个Component是在router.ts里面顶一个路由，父组件通过解构赋值语法就知道这个到底是哪个组件，然后通过`:is`来选中那个组件

#### 1. KeepAlive & Transition

```
<router-view v-slot="{ Component }">
  <transition>
    <keep-alive>
      <component :is="Component" />
    </keep-alive>
  </transition>
</router-view>
```

​	可以把路由视图的**组件套在里面**，一样能实现组件切换时的**过渡**和**保存缓存**

#### 2.传递 props 和插槽（实践中通常不会这么做）

​	

#### 3.模板引用

​	使用插槽可以让我们直接将[模板引用](https://vuejs.org/guide/essentials/template-refs.html)放置在路由组件上：

```
<router-view v-slot="{ Component }">
  <component :is="Component" ref="mainContent" />
</router-view>
```

### 十七、过渡动效

#### 	1.单个路由的过渡

​		让每个路由的组件有不同的过渡，你可以将[元信息](https://router.vuejs.org/zh/guide/advanced/meta)和动态的 `name` 结合在一起，放在`<transition>` 上：

```
const routes = [
  {
    path: '/custom-transition',
    component: PanelLeft,
    meta: { transition: 'slide-left' },
  },
  {
    path: '/other-transition',
    component: PanelRight,
    meta: { transition: 'slide-right' },
  },
]
```

```
<router-view v-slot="{ Component, route }">
  <!-- 使用任何自定义过渡和回退到 `fade` -->
  <transition :name="route.meta.transition || 'fade'">
    <component :is="Component" />
  </transition>
</router-view>
```



#### 	2.基于路由的动态过渡

​		也可以根据目标路由和当前路由之间的关系，动态地确定使用的过渡。使用和刚才非常相似的片段。使用一个导航守卫来改变路由元信息，<router-view>的代码跟前面一样

```
router.afterEach((to, from) => {
  const toDepth = to.path.split('/').length
  const fromDepth = from.path.split('/').length
  to.meta.transition = toDepth < fromDepth ? 'slide-right' : 'slide-left'
})
```

#### 	3.强制在复用的视图之间进行过渡

​		Vue 可能会自动复用看起来相似的组件，从而忽略了任何过渡。幸运的是，可以[添加一个 `key` 属性](https://cn.vuejs.org/api/built-in-special-attributes.html#key)来强制过渡。这也允许你在相同路由上使用不同的参数触发过渡：

```
<router-view v-slot="{ Component, route }">
  <transition name="fade">
    <component :is="Component" :key="route.path" />
  </transition>
</router-view>
```

### 十八、[滚动行为](https://router.vuejs.org/zh/guide/advanced/scroll-behavior.html)(scrollBehavior)

​	就是让你自己来控制，当你切换路由的时候新路由的页面给你**展示的是他的哪里**，例如：当你从当前路由切换走又切换回来的时候，你可以让页面给你**展示你最开始**，第一次切换之前的那个位置

​	**注意: 这个功能只在支持 history.pushState 的浏览器中可用。**

创建路由的时候添加`scrollBehavior`这个方法就行

```
const router = createRouter({
  history: createWebHashHistory(),
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // return 期望滚动到哪个的位置
  }
})
```

第三个参数 `savedPosition`，只有当这是一个 `popstate` 导航时才可用（由浏览器的后退/前进按钮触发）。

你也可以通过 `el` 传递一个 CSS 选择器或一个 DOM 元素。在这种情况下，`top` 和 `left` 将被视为该**元素的相对偏移量**。

```
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    // 始终在元素 #main 上方滚动 10px
    return {
      // 也可以这么写
      // el: document.getElementById('main'),
      el: '#main',
      // 在元素上 10 像素
      top: 10,
    }
  },
})
```

通过el也可以实现**滚动到锚点**的效果，就是去抓路由传过来的`hash`的值

```
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
      return {
        el: to.hash,
      }
    }
  },
})
```

浏览器支持**滚动行为**的话，添加`behavior: 'smooth'`属性可以让他定位的时候更丝滑

```
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
      return {
        el: to.hash,
        behavior: 'smooth',
      }
    }
  }
})
```

还可以实现**延迟滚动**

```
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve({ left: 0, top: 0 })
      }, 500)
    })
  },
})
```



### 十九、[路由懒加载](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html)

​	把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就会更加高效

#### 	1.动态导入(所有路由都用)

​	就是动态导入组件，然后传递给`component`

```
// 将
// import UserDetails from './views/UserDetails.vue'
// 替换成
const UserDetails = () => import('./views/UserDetails.vue')

const router = createRouter({
  // ...
  routes: [
    { path: '/users/:id', component: UserDetails }
    // 或在路由定义里直接使用它
    { path: '/users/:id', component: () => import('./views/UserDetails.vue') },
  ],
})
```

​	`component` (和 `components`) 配置接收一个返回 Promise 组件的函数，Vue Router **只会在第一次进入页面时才会获取这个函数**，然后使用缓存数据

```
const UserDetails = () =>
  Promise.resolve({
    /* 组件定义 */
  })
```

​	直接用`promise.resolve`就行因为这个可以直接返回一个已解决的`promise`，如果用`new promise`，会比较繁琐而且new一般用于需要手动控制异步逻辑（如延迟、条件判断等）

​	尽量对**所有的路由**都使用动态导入！！！！

在构建工具webpack以及vite中，可以把组件按组分块，这样一来就可以按块加载，每一块加载XXX组件，**直接看文档**

### 二十、类型化路由 (v4.1.0+)

​	通过 Vite/webpack/Rollup 插件启用

### 二十一、拓展RouteLink

​	在现有的 `RouterLink` 基础上，创建一个功能更强大的自定义组件，它既保留了原有 `RouterLink` 的功能，又加入了额外的功能，适用于应用中的特殊需求

```
<script setup>
import { computed } from 'vue'
import { RouterLink } from 'vue-router'

defineOptions({
  inheritAttrs: false,
})

const props = defineProps({
  // 如果使用 TypeScript，请添加 @ts-ignore，就是//@ts-ignore，一定要用注释的形式，这是TS语言约定的
  ...RouterLink.props, //继承route-link原有的所有props（比如to、replace）
  inactiveClass: String,
})

const isExternalLink = computed(() => {
  return typeof props.to === 'string' && props.to.startsWith('http') 
  //判断 to 属性是否是字符串，并且是否以 http 开头
})
</script>

<template>
  <a v-if="isExternalLink" v-bind="$attrs" :href="to" target="_blank">
    <slot />
  </a> //如果是外部链接，直接渲染一个普通的 <a> 标签，也就是没有给出isExternalLink
  <router-link
    v-else              	//就是if else的语句，if不成立就渲染else的东西
    v-bind="$props"
    custom
    v-slot="{ isActive, href, navigate }"   //这个v-slot涉及到route
  >
    <a
      v-bind="$attrs"
      :href="href"
      @click="navigate"
      :class="isActive ? activeClass : inactiveClass" //注意，因为activeClass和inactiveClass是个props所以还是要依														   靠父组件传递值，然后就可以选择性的直接应用
    >
      <slot />
    </a>
  </router-link>
</template>
```

父组件使用（继续封装一次AppLink）的时候，按下面那样写：

```
<template>
  <AppLink
    v-bind="$attrs"
    class="inline-flex items-center px-1 pt-1 border-b-2 border-transparent text-sm font-medium leading-5 text-gray-500 hover:text-gray-700 hover:border-gray-300 focus:outline-none focus:text-gray-700 focus:border-gray-300 transition duration-150 ease-in-out"
    active-class="border-indigo-500 text-gray-900 focus:border-indigo-700"
    inactive-class="text-gray-500 hover:text-gray-700 hover:border-gray-300 focus:text-gray-700 focus:border-gray-300"
  >
    <slot />
  </AppLink>
</template>
```

​	v-bind="$attrs，因为这个透传Attribute的缘故，所以父组件会把他的active-class等传递给他，然后因为这个标签刚好接了$attrs，所以全都应用过来了

<img src="D:\Web学习\前端\assets\image-20241116165911118.png" alt="image-20241116165911118" style="zoom:67%;" />

<img src="D:\Web学习\前端\assets\image-20241116165924456.png" alt="image-20241116165924456" style="zoom:67%;" />

#### 1.`<router-link>` 的props

`<router-link>` 是 Vue Router 提供的一个组件，用于创建导航链接。`<router-link>` 的 `props` 主要包括以下几项：

1. **to** (string | object)
   指定导航的目标，可以是字符串（路径）或对象（包含路由相关参数）。
2. **replace** (boolean)
   如果设置为 `true`，则导航将不会留下历史记录。默认值为 `false`。
3. **activeClass** (string)
   设置当前路由匹配时应用的 CSS 类名。默认值是 `router-link-active`。如果你希望使用不同的类名来表示激活状态，可以通过 `activeClass` 来指定。
4. **exact** (boolean)
   如果为 `true`，则只有在路径完全匹配时才会添加 `activeClass`。默认值为 `false`，即只要部分路径匹配就会被激活。
5. **event** (string | array)
   指定触发路由跳转的事件，默认是 `'click'`，也可以设置为其他事件（例如 `mouseover`）。
6. **custom** (boolean)
   如果设置为 `true`，则 `<router-link>` 不会自动渲染为 `<a>` 标签，而是作为一个普通的组件渲染。这在与自定义组件一起使用时很有用

#### 2.通过 **`useLink`** 方法扩展 `<router-link>`

```
<script setup>
import { RouterLink, useLink } from 'vue-router'
import { computed } from 'vue'

const props = defineProps({
  // 如果使用 TypeScript，请添加 @ts-ignore
  ...RouterLink.props,
  inactiveClass: String,
}）

const {
  // 解析出来的路由对象
  route,
  // 用在链接里的 href
  href,
  // 布尔类型的 ref 标识链接是否匹配当前路由
  isActive,
  // 布尔类型的 ref 标识链接是否严格匹配当前路由
  isExactActive,
  // 导航至该链接的函数
  navigate             //这些属性可以选择性的写，但是最好全写，你也不知道你什么时候会用到他们
} = useLink(props)

const isExternalLink = computed(
  () => typeof props.to === 'string' && props.to.startsWith('http')
)
</script>
```

​	**`useLink` 的作用是解析和管理与路由链接相关的所有属性和行为**，它通过解析传入的 `props`，提供了这些属性的响应式版本以及相关的跳转逻辑，然后代码就可以按下面那样写：

```
const linkClass = computed(() => {
  if (isActive.value) {
    return 'active' // 激活时的类
  }
  if (props.inactiveClass) {
    return props.inactiveClass // 未激活时使用自定义类
  }
  return ''
})

// 点击事件处理
const handleClick = () => {
  if (!isExternalLink.value) {
    navigate() // 内部链接导航
  }
}
```

### 二十二、[等待导航结果](https://router.vuejs.org/zh/guide/advanced/navigation-failures.html)

当使用 `router-link` 组件时，Vue Router 会自动调用 `router.push` 来触发一次导航。

大多数时候都是导航到新页面，但是也有时候会停留在原页面（大多都是导航守卫搞的）：

- 用户已经位于他们正在尝试导航到的页面
- 一个[导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)通过调用 `return false` 中断了这次导航
- 当前的[导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)还没有完成时，一个新的导航守卫会出现了
- 一个[导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)通过返回一个新的位置，重定向到其他地方 (例如，`return '/login'`)
- 一个[导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)抛出了一个 `Error`

所以等待导航结果只需要加上一个await即可

```
await router.push('/my-profile')
this.isMenuOpen = false
```

#### 1.检测导航故障

​	如果导航被阻止，导致用户停留在同一个页面上，由 `router.push` 返回的 `Promise` 的解析值将是 ***Navigation Failure***

```
const navigationResult = await router.push('/my-profile')

if (navigationResult) {
  // 导航被阻止
} else {
  // 导航成功 (包括重新导航的情况)
  this.isMenuOpen = false
}
```

​	*Navigation Failure* 有额外属性，方便我们知道是什么原因返回的failure，使用 `isNavigationFailure`函数以及 `NavigationFailureType`对象（有三个属性）

```
import { NavigationFailureType, isNavigationFailure } from 'vue-router'

// 试图离开未保存的编辑文本界面
const failure = await router.push('/articles/2')

if (isNavigationFailure(failure, NavigationFailureType.aborted)) {
  // 给用户显示一个小通知
  showToast('You have unsaved changes, discard and leave anyway?')  //这个showToast函数是自定义的
}
```

详细讲解：

`isNavigationFailure` 结合 `NavigationFailureType`： vue-router 提供了 isNavigationFailure

 函数，它允许你根据不同的导航失败类型来检测是否发生了错误。以下是几个常见的错误类型：

- `aborted`：导航被中断，通常由于用户在离开页面时未保存更改。
- `duplicated`：目标路由与当前路由相同，即重复导航。
- `unresolved`：目标路由无法解析。

示例：

```
ts

import { NavigationFailureType, isNavigationFailure } from 'vue-router'

const failure = await router.push('/some-route')
if (isNavigationFailure(failure, NavigationFailureType.aborted)) {
  // 处理被中断的导航
}
if (isNavigationFailure(failure, NavigationFailureType.duplicated)) {
  // 处理重复导航
}
if (isNavigationFailure(failure, NavigationFailureType.unresolved)) {
  // 处理路由解析失败的情况
}
```

#### 2.全局导航故障

```
router.afterEach((to, from, failure) => {
  if (failure) {
    sendToAnalytics(to, from, failure)
  }
})
```

#### 3.鉴别导航故障(结合1的方法)

`NavigationFailureType` 有三种不同的类型：

- `aborted`：在导航守卫中返回 `false` 中断了本次导航。
- `cancelled`： 在当前导航完成之前又有了一个新的导航。比如，在等待导航守卫的过程中又调用了 `router.push`。
- `duplicated`：导航被阻止，因为我们已经在目标位置了。

#### 4.导航故障的属性

导航失败都会暴露 `to` 和 `from` 属性，以反映失败导航的当前位置和目标位置

```
// 正在尝试访问 admin 页面
router.push('/admin').then(failure => {
  if (isNavigationFailure(failure, NavigationFailureType.aborted)) {
    failure.to.path // '/admin'
    failure.from.path // '/'
  }
})
```

所以就可以用to和from来找到那个位置

#### 5.检测重定向

就是检测有没有被导航守卫给重定向到别的地方去

```
await router.push('/my-profile')
if (router.currentRoute.value.redirectedFrom) {
  // redirectedFrom 是解析出的路由地址，就像导航守卫中的 to和 from
} router.currentRoute.value.redirectedFrom  等价于 route.redirectedFrom
```

### 二十三、[动态路由](https://router.vuejs.org/zh/guide/advanced/dynamic-routing.html)

#### 	1.添加路由

​	动态路由主要通过两个函数实现。`router.addRoute()` 和 `router.removeRoute()`。它们**只**注册一个新的路由，也就是说，如果新增加的路由与当前位置相匹配，就需要你用 `router.push()` 或 `router.replace()` 来**手动导航**，才能显示该新路由。我们来看一个例子:

```
const router = createRouter({
  history: createWebHistory(),
  routes: [{ path: '/:articleName', component: Article }],
})


router.addRoute({ path: '/about', component: About })
// 我们也可以使用 this.$route 或 useRoute() 因为`router.currentRoute.value` 和 `route` 是等价的
router.replace(router.currentRoute.value.fullPath) //用了之后才可以进行跳转
```

#### 2.删除路由

- 通过添加一个名称冲突的路由。如果添加与现有途径名称相同的途径，会先删除路由，再添加路由：

  ```
  router.addRoute({ path: '/about', name: 'about', component: About })
  // 这将会删除之前已经添加的路由，因为他们具有相同的名字且名字必须是唯一的
  router.addRoute({ path: '/other', name: 'about', component: Other })
  ```

- 通过调用 `router.addRoute()` 返回的回调：

  ```
  const removeRoute = router.addRoute(routeRecord)
  removeRoute() // 删除路由如果存在的话
  ```

  当路由没有名称时，这很有用。

- 通过使用 `router.removeRoute()` 按名称删除路由：

  ```
  router.addRoute({ path: '/about', name: 'about', component: About })
  // 删除路由
  router.r
  ```

#### 3.添加嵌套路由

​	要将嵌套路由添加到现有的路由中，可以将路由的 *name* 作为第一个参数传递给 `router.addRoute()`，这将有效地添加路由，就像通过 `children` 添加的一样：

```
router.addRoute({ name: 'admin', path: '/admin', component: Admin })
router.addRoute('admin', { path: 'settings', component: AdminSettings })
```



#### 4.查看现有路由

Vue Router 提供了两个功能来查看现有的路由：

- [`router.hasRoute()`](https://router.vuejs.org/zh/api/interfaces/router#Methods-hasRoute)：检查路由是否存在。

- [`router.getRoutes()`](https://router.vuejs.org/zh/api/interfaces/router#Methods-getRoutes)：获取一个包含所有路由记录的数组

  这样就不会在不知情的情况下覆盖路由了































# 拓展

### 一、route-view的上下文

​	当我们使用<router-view>的时候，Vue Router会给他的插槽传递一个上下文，所以你可以像这样去使用

```
<router-view v-slot="{ Component, route }"> </router-view>
```

可能出现的上下文属性：

#### 1. **`Component`**

​	这是当前路由匹配的组件，它表示的是 **Vue 组件构造函数**，用于渲染当前路由对应的组件。例如，假设你有如下的路由配置

<img src="D:\Web学习\前端\assets\image-20241114203503264.png" alt="image-20241114203503264" style="zoom:67%;" />

#### 2. **`route`**

​	`route` 对象包含了当前路由的详细信息，包括路径、路由参数、查询参数、路由的元数据等。

```
{
  // 当前的路径
  path: '/home',
  
  // 当前路由的匹配参数
  params: { ... },

  // 当前路由的查询参数
  query: { ... },

  // 当前路由的哈希值
  hash: '#some-hash',

  // 路由的元数据
  meta: {
    transition: 'fade',  // 例如，路由切换的动画过渡
  },

  // 当前路由的完整信息
  fullPath: '/home?search=test',
  
  // 路由记录的其它详细信息
  name: 'home',
  // ...
}
```

#### 3. **`matched`**

​	在某些情况下，`route` 对象会包括一个 `matched` 属性，这个属性是一个数组，包含当前路由以及所有父路由的 `route` 对象。这个数组从最外层的路由开始，按嵌套顺序排列。

​	假设你有如下嵌套路由配置：

```
const routes = [
  {
    path: '/',
    component: Layout,
    children: [
      {
        path: 'home',
        component: HomePage,
      },
      {
        path: 'about',
        component: AboutPage,
      },
    ],
  },
];
```

当路由路径为 `/about` 时，`route.matched` 会是：

```
[
  { path: '/', component: Layout, ... },  // 父路由
  { path: '/about', component: AboutPage, ... }  // 当前路由
]
```

#### 4. **`href`**

​	在一些特定的情况下，`href` 可能出现在 `route` 对象中。它表示当前路由的完整 URL 地址。这通常用于外部链接或者动态生成链接时。

#### 5. **`props`**

​	如果路由配置中定义了 `props`，该属性会包含传递给路由组件的 props。这对于动态传递路由参数给组件是非常有用的。`props` 属性可以是布尔值、对象或函数，具体表现取决于路由的配置。

例子：

```
{
  path: '/user/:id',
  component: UserProfile,
  props: true,  // 将路由参数作为 props 传递给 UserProfile 组件
}
```

​	在这种情况下，`props` 会自动包含路由参数，`UserProfile` 组件会接收到 `id` 作为 `props`。

#### 6. **`isRouteActive`**

​	Vue Router 在某些版本中（比如 Vue 2.x + Vue Router 3.x）支持 `isRouteActive` 函数，它会告诉你某个路由是否处于激活状态。它**不总是被直接暴露在 `v-slot` 的上下文中**，但在某些场景下，如果你在 `<router-link>` 或 `<router-view>` 中使用，可能会看到与路由激活状态相关的功能。

#### 7. **`navigationFailure` (Vue Router 4)**

​	在 Vue Router 4 中，如果路由导航失败（例如由于不匹配的路径或守卫阻止了导航），可能会通过 `navigationFailure` 提供失败的原因。在 `<router-view>` 插槽上下文中并不直接暴露这个属性，但它可以用于自定义导航错误处理。

#### 8. **`key`**

​	在某些情况下（特别是配合动态组件渲染时），`key` 可能会被用于控制组件的复用与重新渲染。当路由变化时，`key` 可以确保重新渲染组件，而不是复用之前的组件实例。这通常用于多次切换相同的组件时。

例子：

```
<router-view :key="route.path" />
```

​	通过这种方式，`key` 是动态变化的，从而确保每次路由变化时，组件都会被重新渲染，而不是复用之前的组件实例。

### 二、route-link的上下文(不是props！！)

### **1. `isActive`**

- **类型**: `boolean`

- 含义: 表示当前链接是否处于激活状态。

  - 当目标路由与当前路由匹配时，值为 `true`。
  - 支持部分匹配。例如，目标路由是 `/about`，当前路由是 `/about/team` 时，也可能激活（取决于匹配策略）。

- 用途: 通常用于动态设置样式。

  ```
  <router-link
    to="/about"
    custom
    v-slot="{ isActive }"
  >
    <a :class="{ active: isActive }">About</a>
  </router-link>
  ```

------

### **2. `isExactActive`**

- **类型**: `boolean`

- 含义: 表示当前链接是否与目标路由完全匹配。

  - 与 `isActive` 不同，`isExactActive` 仅在目标路由完全匹配当前路由时为 `true`。
  - 例如，目标路由是 `/about`，当前路由是 `/about/team` 时，`isExactActive` 为 `false`。

- 用途: 用于区分部分匹配和完全匹配的样式逻辑。

  ```
  <router-link
    to="/about"
    custom
    v-slot="{ isExactActive }"
  >
    <a :class="{ exact: isExactActive }">About</a>
  </router-link>
  ```

------

### **3. `href`**

- **类型**: `string`

- 含义: 解析后的完整链接地址。

  - 基于 `to` 属性生成，支持路径、命名路由和参数的解析。

- 用途: 为 <a> 标签提供 href

   属性，确保正常导航。

  ```
  <router-link
    to="/contact"
    custom
    v-slot="{ href }"
  >
    <a :href="href">Contact</a>
  </router-link>
  ```

------

### **4. `navigate`**

​	在 `<router-link>` 的上下文中，`navigate` 提供了程序化触发导航的能力，相当于调用 `router.push()` 或 `router.replace()`，但它已经绑定了当前 `to` 的目标路由，因此可以直接执行跳转操作。

- **类型**: `Function`

- 含义: 手动触发导航的函数。

  - 调用时会按照 `to` 属性指定的目标路由进行跳转。
  - 通常在绑定点击事件时使用，避免默认行为（如浏览器直接跳转）。

- 用途: 可用于自定义点击逻辑。

  ```
  <router-link
    to="/profile"
    custom
    v-slot="{ navigate }"
  >
    <a @click="navigate">Profile</a>
  </router-link>
  ```

------

### **5. `isPending`** （Vue Router 4.1+）

- **类型**: `boolean`

- 含义: 指示目标路由是否正在解析。

  - 当路由切换需要时间（例如异步组件加载、导航守卫处理）时，`isPending` 为 `true`。

- 用途: 实现加载状态的视觉反馈。

  ```
  <router-link
    to="/dashboard"
    custom
    v-slot="{ isPending, navigate }"
  >
    <a @click="navigate" :class="{ loading: isPending }">Dashboard</a>
  </router-link>
  ```

------

### **6. `route`**（并非直接上下文，但可以访问）

- **类型**: `RouteLocationNormalized`

- 含义: 当前目标路由的完整对象。

  - 包含路由的 `name`、`path`、`params`、`query` 等详细信息。

- 用途: 可根据目标路由的细节动态设置内容。

  ```
  <router-link
    to="/settings"
    custom
    v-slot="{ href, route }"
  >
    <a :href="href">{{ route.name || 'Settings' }}</a>
  </router-link>
  ```

### 三、route-link的props

​	<img src="D:\Web学习\前端\assets\image-20241116201711099.png" alt="image-20241116201711099" style="zoom:67%;" />
