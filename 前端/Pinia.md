# [Store 是什么？](https://pinia.vuejs.org/zh/getting-started.html#what-is-a-store)

​	Store (如 Pinia) 是一个保存状态和业务逻辑的实体，它并不与你的组件树绑定。换句话说，**它承载着全局状态**。它有点像一个永远存在的组件，每个组件都可以读取和写入它。它有**三个概念**，[state](https://pinia.vuejs.org/zh/core-concepts/state.html)、[getter](https://pinia.vuejs.org/zh/core-concepts/getters.html) 和 [action](https://pinia.vuejs.org/zh/core-concepts/actions.html)，我们可以假设这些概念相当于组件中的 `data`、 `computed` 和 `methods`。

## [应该在什么时候使用 Store?](https://pinia.vuejs.org/zh/getting-started.html#when-should-i-use-a-store)

​	一个 Store 应该包含可以在整个应用中访问的数据。这包括在许多地方使用的数据，例如显示在导航栏中的用户信息，以及需要通过页面保存的数据，例如一个非常复杂的多步骤表单。

​	另一方面，你应该避免在 Store 中引入那些原本可以在组件中保存的本地数据，例如，一个元素在页面中的可见性。

并非所有的应用都需要访问全局状态，但如果你的应用确实需要一个全局状态，那 Pinia 将使你的开发过程更轻松。

# 一、定义Store

​	必须保证每一个store的名字都是**不一样的**不然肯定会冲突！这个**名字** ，也被用作 *id* ，是必须传入的，为了养成习惯性的用法，将返回的函数命名为 ***use...*** （就像下面代码的`useAlertStore`）是一个符合组合式函数风格的约定。

```
import { defineStore } from 'pinia'

// 你可以任意命名 `defineStore()` 的返回值，但最好使用 store 的名字，同时以 `use` 开头且以 `Store` 结尾。
// (比如 `useUserStore`，`useCartStore`，`useProductStore`)
// 第一个参数是你的应用中 Store 的唯一 ID。
export const useAlertsStore = defineStore('alerts', {
  // 其他配置...
})
```

`defineStore()` 的第二个参数可接受两类值：Setup 函数或 Option 对象

## 1.[Setup Store](https://pinia.vuejs.org/zh/core-concepts/#setup-stores)

​	与 Vue 组合式 API 的 [setup 函数](https://cn.vuejs.org/api/composition-api-setup.html) 相似，我们可以传入一个函数，该函数定义了一些响应式属性和方法，并且返回一个带有我们想暴露出去的属性和方法的对象。

```
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)    //pinia store中可以使用const来定义一个ref
  const doubleCount = computed(() => count.value * 2)
  function increment() {
    count.value++
  }

  return { count, doubleCount, increment }
})
```

在 *Setup Store* 中：

- `ref()` 就是 `state` 属性
- `computed()` 就是 `getters`
- `function()` 就是 `actions`

​	注意，要让 pinia 正确识别 `state`，你**必须**在 setup store 中返回 **`state` 的所有属性**。这意味着，你不能在 store 中使用**私有**属性。不完整返回会影响 [SSR](https://pinia.vuejs.org/zh/cookbook/composables.html) ，开发工具和其他插件的正常运行。

​	`store`可以传入其他组件传过来的各种属性，比如用`provide`的值，还有用useRoute()来获得的路由参数，可以在`store`里面使用`Inject`注入进`store`，然后在里面使用，但是要注意别在返回的时候**别给他`return`了就行**

## 2.[使用 Store](https://pinia.vuejs.org/zh/core-concepts/#using-the-store)（`store` 是一个用 `reactive` 包装的对象）

`	store`是一个用 `reactive` 包装的对象，这意味着不需要在 getters 后面写 `.value`，所以你不能像其他对象一样使用解构，会丧失响应性

​	虽然我们前面定义了一个 store，但在我们使用 `<script setup>` 调用 `useStore()`(或者使用 `setup()` 函数，**像所有的组件那样**) 之前，store 实例是不会被创建的：

```
<script setup>
import { useCounterStore } from '@/stores/counter'
// 可以在组件中的任意位置访问 `store` 变量 ✨
const store = useCounterStore()
</script>
```

​	但是你导入的时候一定要确保你这个store**已经定义并且导出**。你可以定义任意多的 store，但为了让使用 pinia 的益处最大化 (比如允许构建工具自动进行代码分割以及 TypeScript 推断)，**你应该在不同的文件中去定义不同的 store**

## 3.从Stroe解构(storeToRefs())

```
<script setup>
import { storeToRefs } from 'pinia'
const store = useCounterStore()
// `name` 和 `doubleCount` 是响应式的 ref
// 同时通过插件添加的属性也会被提取为 ref
// 并且会跳过所有的 action 或非响应式 (不是 ref 或 reactive) 的属性
const { name, doubleCount } = storeToRefs(store)
// 作为 action 的 increment 可以直接解构
const { increment } = store
</script>
```

`storeToRefs()` 主要用于将 Pinia store 中的响应式状态（`state` 和 `getter`）解构为 `ref`，而action直接解构就行

# 二、state

**state**和**$state**的区别

```
// 通过直接修改 store 字段
counter.count = 5;

// 使用 $state 修改
counter.$state.count = 10;

```

有很多使用方法，但是主要看符合setup store的

<img src="D:\Web学习\前端\assets\image-20241211161157254.png" alt="image-20241211161157254" style="zoom:67%;" />

## 1.重置 state

在 [Setup Stores](https://pinia.vuejs.org/core-concepts/#setup-stores) 中，您需要创建自己的 `$reset()` 方法：

```
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)

  function $reset() {
    count.value = 0
  }

  return { count, $reset }
})
```

这个$是一个约定俗成的命名规范

## 2.变更 state

除了用 `store.count++` 直接改变 store，你还可以调用 `$patch` 方法，如下使用方法

### 使用 `$patch` 来更新 `store`

#### 方法一：直接使用 `$patch` 传递补丁对象

你可以通过 `$patch` 来一次性更新多个属性。例如，修改 `count` 和其他属性（如果有的话）：

```
import { useCounterStore } from './stores/counter'

const counterStore = useCounterStore()

counterStore.$patch({
  count: counterStore.count.value + 1, // 更新 count
})
```

#### 方法二：通过 `$patch` 使用函数

当需要进行复杂的变更时，可以使用一个函数来修改 state，例如修改数组或其他复杂操作：

```
import { useCounterStore } from './stores/counter'

const counterStore = useCounterStore()

counterStore.$patch((state) => {
  state.count++ // 修改 count
})                                 //这个state就是你定义的counterStore里面的设置的若干个ref，因为你用的counterStore								     来调用的$patch
```

`$patch` 的触发时机是你调用它的时刻，它会立即执行并更新 state。而在 devtools 中，这些变更会被视为同一条操作记录。

## 3.替换state

你不能完全替换掉 store 的 state，因为那样会破坏其响应性。但是，你可以 patch 它。

```
// 这实际上并没有替换`$state`
store.$state = { count: 24 }
// 在它内部调用 `$patch()`：
store.$patch({ count: 24 })
```

还可以直接使用pinia实例：

```
pinia.state.value = {}
```

​	但是**如果你直接修改 `pinia.state.value = {}`，那么所有的 store 的状态都会被替换成 `{}`**，无论是 `a`、`b`、`c` 还是其他 store。都会被改变，因为Pinia实例代表的是整个Pinia状态的容器，也就是所有的store

总结：

- 使用 `$patch()` 更新 state 时，只能更新现有属性，无法添加新的属性。
- 直接修改 `pinia.state.value` 可以替换整个 state，包括添加新的属性。

## 4.订阅state($subscribe())

​	可以通过 store 的 `$subscribe()` 方法侦听 `state` 及其变化。

```
cartStore.$subscribe((mutation, state) => {
  // import { MutationType } from 'pinia'
  mutation.type // 'direct' | 'patch object' | 'patch function'
  // 和 cartStore.$id 一样
  mutation.storeId // 'cart'
  // 只有 mutation.type === 'patch object'的情况下才可用
  mutation.payload // 传递给 cartStore.$patch() 的补丁对象。

  // 每当状态发生变化时，将整个 state 持久化到本地存储。（目前仅想出来用于webStorage，其他的不知道）
  localStorage.setItem('cart', JSON.stringify(state))
})
```

​	`mutation.type`、`mutation.storeId` 和 `mutation.payload` 是 **Pinia 内部定义的**用于描述 store 状态变更的 **mutation** 对象的属性。当你通过 `store.$subscribe()` 订阅 store 的状态变化时，**Pinia 会自动生成并传递一个 `mutation` 对象**，包含这些属性。

### 4.1**`mutation.type`**：

​	记录状态变更的类型，告诉你是通过什么方式更新了状态。

- `'direct'`：表示直接赋值修改了状态（例如，`store.$state = {}` 或 `store.someProperty = newValue`）。
- `'patch object'`：表示使用 `$patch()` 并传递了一个对象来更新状态。
- `'patch function'`：表示使用 `$patch()` 并传递了一个函数来更新状态。

### 4.2**`mutation.storeId`**：

​	记录了触发状态变更的 **store 的 ID**，这对于多 store 的应用非常有用。通常，`storeId` 与定义 store 时的唯一标识符相同。例如，如果 store 是 `cartStore`，则 `mutation.storeId` 会是 `'cart'`。

### 4.3**`mutation.payload`**：

​	这个属性在 `mutation.type === 'patch object'` 或 `mutation.type === 'patch function'` 时会可用，记录了你传递给 `$patch()` 的补丁对象或函数。

- 如果你通过 `store.$patch({ count: 10 })` 来更新状态，那么 `mutation.payload` 就是 `{ count: 10 }`。

- 如果你使用 `$patch((state) => { state.count = 10 })`，那么 `mutation.payload` 就是传递的函数。

  

​	`$subscribe()` 是**通过 `patch()` 更新**状态时才触发回调，而 `watch()` 则是每次状态变更都会触发回调。所以，`$subscribe()` 更适合用于需要细粒度控制的场景，但不提供变更上下文（如变更的操作来源mutation），`watch()` 是 Vue 更为通用的响应式机制**。

​	这个subscribe是跟组件绑定的，当该组件被卸载时，它们将被自动删除。如果你想在**组件卸载后依旧保留**它们，请将 **`{ detached: true }`** 作为第二个参数，以将 *state subscription* 从当前组件中*分离*：

### 4.4对pinia实例使用watch

​	对pinia实例使用watch,可以监听所有的store的 state

```
watch(
  pinia.state,
  (state) => {
    // 每当状态发生变化时，将整个 state 持久化到本地存储。
    localStorage.setItem('piniaState', JSON.stringify(state))
  },
  { deep: true }
)
```

# 三、Getter 

​	Getter完全等同于 store 的 state 的[计算值](https://cn.vuejs.org/guide/essentials/computed.html)。可以通过 `defineStore()` 中的 `getters` 属性来定义它们。**推荐**使用箭头函数，并且它将接收 `state` 作为第一个参数：

```
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
  }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
})
```

## 1.访问其他 getter

​	你可以访问到其他任何 getter。在这种情况下，**你需要为这个 getter 指定一个返回值的类型**，就是自己的几个getter访问对方

```
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useCounterStore = defineStore('counter', () => {
  // 使用 ref 来定义 state
  const count = ref(0)

  // 使用 computed 来定义 getter
  const doubleCount = computed(() => count.value * 2)
  const doubleCountPlusOne = computed(() => doubleCount.value + 1)  //直接使用就行，但是要注意是一个ref

  // 定义 actions（如果需要）
  function increment() {
    count.value++
  }

  // 返回 state、getters 和 actions
  return {
    count,
    doubleCount,
    doubleCountPlusOne,
    increment,
  }
})

```

## 2.访问其他 store 的 getter

​	直接像正常访问getter的方式访问就可以

```
import { useOtherStore } from './other-store'

export const useStore = defineStore('main', {
  state: () => ({
    // ...
  }),
  getters: {
    otherGetter(state) {
      const otherStore = useOtherStore()
      return state.localData + otherStore.data
    },
  },
})
```

# 四、Action

​	在setup store中直接用`function`以及`async function`(异步的action)就可以定义，下面是一个例子

```
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('main', () => {
  // state
  const count = ref(0)

  // actions
  const increment = () => {
    count.value++
  }

  const randomizeCounter = () => {
    count.value = Math.round(100 * Math.random())
  }

  const fetchData = async () => {
    try {
      const response = await fetch('https://api.example.com/data')
      const data = await response.json()
      count.value = data.value // 假设 API 返回一个 `value` 字段
    } catch (error) {
      console.error('Failed to fetch data:', error)
    }
  }

  // 返回 state 和 actions
  return {
    count,
    increment,
    randomizeCounter,
    fetchData,
  }
})

```

## 1.访问其他 store 的 action

​	跟getter访问其他getter一样，直接使用就行

```
import { useAuthStore } from './auth-store'

export const useSettingsStore = defineStore('settings', {
  state: () => ({
    preferences: null,
    // ...
  }),
  actions: {
    async fetchUserPreferences() {
      const auth = useAuthStore()
      if (auth.isAuthenticated) {
        this.preferences = await fetchPreferences()
      } else {
        throw new Error('User must be authenticated')
      }
    },
  },
})
```

## 2.订阅action($onAction())

###  2.1所有参数：

1. **`name`**：

   - 当前执行的 `action` 名称（字符串）。

2. **`args`**：

   - 传递给该 `action` 的参数（数组）。

3. **`after`**：

   - 一个回调函数，在 `action` 执行完成后触发，接收 `action` 的返回值作为参数。

4. **`onError`**：

   - 一个回调函数，在 `action` 执行期间发生错误时触发，接收错误对象作为参数。

使用方法如下：

```
// 在组件或其他地方使用 $onAction
const store = useCounterStore()

store.$onAction(({ name, args, after, onError }) => {
    console.log(`Action "${name}" is called with arguments:`, args)

    // 执行后
    after((result) => {
        console.log(`Action "${name}" successfully completed with result:`, result)
    })

    // 发生错误时
    onError((error) => {
        console.error(`Action "${name}" failed with error:`, error)
    })
})
```

### 2.2保留$onAction

​	跟$subscribe一样，都是跟组件绑定的，所以组件如果被删除了那么订阅器也就没了，如果你想在组件卸载后依旧保留它们，请将 `true` 作为第二个参数传递给 *action 订阅器*，以便将其从当前组件中分离：

```
<script setup>
const someStore = useSomeStore()
// 此订阅器即便在组件卸载之后仍会被保留
someStore.$onAction(callback, true)
</script>

就是上面的
store.$onAction(({ name, args, after, onError }) => {
    console.log(`Action "${name}" is called with arguments:`, args)

    // 执行后
    after((result) => {
        console.log(`Action "${name}" successfully completed with result:`, result)
    })

    // 发生错误时
    onError((error) => {
        console.error(`Action "${name}" failed with error:`, error)
    })
}, true)   //这里放一个true！！！！！！！！！！！！！！！！！！！
```

# 五、插件

由于有了底层 API 的支持，Pinia store 现在完全支持扩展。可以扩展：

- 为 store 添加新的属性
- 定义 store 时增加新的选项
- 为 store 增加新的方法
- 包装现有的方法
- 改变甚至取消 action
- 实现副作用，如[本地存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
- **仅**应用插件于特定 store

可以用以下的方法创建一个插件，并且应用于Pinia

```
import { createPinia } from 'pinia'

// 创建的每个 store 中都会添加一个名为 `secret` 的属性。
// 在安装此插件后，插件可以保存在不同的文件中
function SecretPiniaPlugin() {
  return { secret: 'the cake is a lie' }
}

const pinia = createPinia()
// 将该插件交给 Pinia 
pinia.use(SecretPiniaPlugin)   //你也可以使用箭头函数，直接在括号里面写

// 在另一个文件中
const store = useStore()
store.secret // 'the cake is a lie'
```

​	`secret` 属性会作为默认值，直接出现在每个 store 的实例中，无需手动定义

## 1.简介(context Ts)

​	`context` 就是 Pinia 插件运行时，提供给插件的“工具包”或“环境描述”。它类似于 JavaScript 的函数调用中的 `this` 或某些框架中的 `context` 概念，包含当前运行的上下文信息，但是TS如果开启了strict: true就会说你没有声明类型然后报错，所以可以加上`import { PiniaPluginContext } from 'pinia';`然后作为类型传入

```
export function myPiniaPlugin(context) {
  context.pinia // 用 `createPinia()` 创建的 pinia。
  context.app // 用 `createApp()` 创建的当前应用(仅 Vue 3)。
  context.store // 该插件想扩展的 store
  context.options // 定义传给 `defineStore()` 的 store 的可选对象。
  // ...
}  //这里是说明context上下文都会有哪些东西

import { PiniaPluginContext } from 'pinia';  //这个是ts的用法

export function myPiniaPlugin(context: PiniaPluginContext) {
  context.store.logState = () => {
    console.log("Current state:", context.store.$state);
  };
}

```

​	然后再main.ts文件里面注册一下

```
import { createPinia } from 'pinia';
import { myPiniaPlugin } from './path/to/plugin';
import { createApp } from 'vue';                        // 在 Vue 项目中挂载 Pinia
import App from './App.vue';

const pinia = createPinia();
pinia.use(myPiniaPlugin); // 注册插件

const app = createApp(App);
app.use(pinia);
app.mount('#app');
```

​	然后就可以直勾勾的使用了比如：

```
// 定义一个 store
import { defineStore } from 'pinia';

export const useStore = defineStore('main', {
  state: () => ({
    count: 0,
  }),
});

// 使用 store
const store = useStore();
store.logState(); // 调用插件注入的方法
// 输出： "Current state: { count: 0 }"
```

## 2.拓展Store

​	你可以直接通过在一个插件中返回包含特定属性的对象来为每个 store 都添加上特定属性：

```
pinia.use(() => ({ hello: 'world' }))
```

​	你也可以直接在 `store` 上设置该属性，但**可以的话，请使用返回对象的方法，这样它们就能被 devtools 自动追踪到**：

```
pinia.use(({ store }) => {
  store.hello = 'world'
})     
```

​	这个里面的`store`就是`pinia`的所有实例，他会把这个hello应用到所有store上

因为每个 store 都被 [`reactive`](https://cn.vuejs.org/api/reactivity-core.html#reactive)包装过，所以可以自动解包任何它所包含的 Ref(`ref()`、`computed()`...)，所以

```
const sharedRef = ref('shared')
pinia.use(({ store }) => {
  // 每个 store 都有单独的 `hello` 属性
  store.hello = ref('secret')
  // 它会被自动解包
  store.hello // 'secret'

  // 所有的 store 都在共享 `shared` 属性的值
  store.shared = sharedRef         //不用使用sharedRrf.value
  store.shared // 'shared'
})
```

## 3.添加新的 state

如果你想给 store 添加新的 state 属性或者在服务端渲染的激活过程中使用的属性，**你必须同时在两个地方添加它**。。

- 在 `store` 上，然后你才可以用 `store.myState` 访问它。
- 在 `store.$state` 上，然后你才可以在 devtools 中使用它，并且，**在 SSR 时被正确序列化(serialized)**。

```
import { toRef, ref } from 'vue'

pinia.use(({ store }) => {
  // 为了正确地处理 SSR，我们需要确保我们没有重写任何一个
  // 现有的值
  if (!store.$state.hasOwnProperty('hasError')) {   //hasOwnProperty是js语言内置的对象方法，用于检查这个对象有没有这													 //个属性
    // 在插件中定义 hasError，因此每个 store 都有各自的
    // hasError 状态
    const hasError = ref(false)
    // 在 `$state` 上设置变量，允许它在 SSR 期间被序列化。
    store.$state.hasError = hasError    //专门改$state，下面那个是专门改state
  }
  // 我们需要将 ref 从 state 转移到 store
  // 这样的话,两种方式：store.hasError 和 store.$state.hasError 都可以访问
  // 并且共享的是同一个变量
  // 查看 https://cn.vuejs.org/api/reactivity-utilities.html#toref
  store.hasError = toRef(store.$state, 'hasError')   //toRef的第一个参数是有很多个值的ref，第二个值hasError是第一个													  //参数的众多ref之一，toRef就是专门从一堆ref中解构一个出来改

  // 在这种情况下，最好不要返回 `hasError`
  // 因为它将被显示在 devtools 的 `state` 部分
  // 如果我们返回它，devtools 将显示两次。
})
```

​	需要注意的是，在一个插件中， state 变更或添加(包括调用 `store.$patch()`)都是发生在 store 被激活之前，**因此不会触发任何订阅函数**

​	**hasOwnProperty是js语言内置的对象方法，用于检查这个对象有没有这个属性**

## 4.重写$reset

直接在`return`里面返回这个新的$reset就可以实现重置用插件添加的`state`了

```
import { toRef, ref } from 'vue'

pinia.use(({ store }) => {
  // 和上面的代码一样，只是为了参考
  if (!store.$state.hasOwnProperty('hasError')) {
    const hasError = ref(false)
    store.$state.hasError = hasError
  }
  store.hasError = toRef(store.$state, 'hasError')

  // 确认将上下文 (`this`) 设置为 store
  const originalReset = store.$reset.bind(store)

  // 覆写其 $reset 函数
  return {
    $reset() {
      originalReset()
      store.hasError = false
    },
  }
})
```
​	`.bind()` 是一个非常常用的方法，它用于创建一个新的函数，并将该函数的 this 关键字绑定到指定的对象上。所以上面的重写$reset就是把$reset原模原样提取出来，然后加了点东西又重新封装进去

## 5.第三方库实例提供给pinia调用

​	这个时候就要使用`import { markRaw } from 'vue'`，用markRaw来保持传入的第三方库的实例原本的性质，比如使用vue-route

```ts
import { markRaw } from 'vue'
import { router } from './router'  // 根据你的路由器路径来调整

pinia.use(({ store }) => {
  store.router = markRaw(router)
})
```

​	这是因为 Pinia 会自动将对象变为响应式的，而 `router` 实例本身并不需要响应式，因此需要使用 `markRaw()` 来避免 Pinia 对它进行响应式处理。这样，你就可以在 **store 中访问到 `router` 实例**，而它依然保持非响应式。

## 6.在插件中调用 `$subscribe`

直接用就行

```
pinia.use(({ store }) => {
  store.$subscribe(() => {
    // 响应 store 变化
  })
  store.$onAction(() => {
    // 响应 store actions
  })
})
```

## 7.添加新的选项

把`debounce`作为第三个参数传递

```
defineStore(
  'search',
  () => {
    // ...
  },
  {
    // 这将在后面被一个插件读取
    debounce: {
      // 让 action searchContacts 防抖 300ms
      searchContacts: 300,
    },
  }
)
```

## 8.TypeScript

### 1.为新的 store 属性添加类型

​	在 Pinia 中使用 `setup` 函数时，`get` 和 `set` 只能在类型声明中扩展 Pinia 的类型（例如 `PiniaCustomProperties`）时使用，要注意这个添加属性之前，下面一段代码的位置

```
project-root/
│
├── src/
│   ├── stores/
│   ├── components/
│   └── main.ts
├── types/
│   └── pinia.d.ts   <-- 这里定义 Pinia 的类型扩展
├── tsconfig.json    <-- tsconfig 配置文件，确保 types 目录被包含
└── vite.config.ts


// types/pinia.d.ts

import { Pinia } from 'pinia';
import { Router } from 'vue-router';
import { Ref } from 'vue';

declare module 'pinia' {
  export interface PiniaCustomProperties {
    // 通过 getter 和 setter 机制定义对 hello 变量的访问方式。这通常是用来控制如何读取和修改该变量的值
    set hello(value: string | Ref<string>)
    get hello(): string

    // 简单的数字属性
    simpleNumber: number

    // 自定义 router 属性
    router: Router
  }
}
```

​	这就相当于定义了一个模板，告诉pinia你会在之后的store添加哪些属性，当然也可以设置一些**可选属性**，这一堆代码最好单独放在一个文件里面

下面是使用上面那个模板的例子：

```
// src/stores/useStore.ts
import { defineStore } from 'pinia';
import { ref, computed, Ref } from 'vue';

export const useStore = defineStore('main', () => {
  // 状态定义
  const hello = ref('');
  
  // 使用 setter 来设置 hello 的值
  const setHello = (value: string | Ref<string>) => {
    if (value instanceof Ref) {
      hello.value = value.value;  // 如果传入的是 Ref 类型
    } else {
      hello.value = value;  // 否则直接赋值
    }
  };

  // 使用 getter 来获取 hello 的值
  const getHello = computed(() => hello.value);

  return {
    hello,  // 直接返回 hello 属性，Pinia 会自动生成 getter 和 setter，后面两个函数与hello的set和get无关
    setHello,
    getHello,
  };
});

```

​	你不需要写getter : XXX因为我们用的是setup store所以直接写就可以

#### 1.1. **getter (`get hello()`)**：

- `get hello()` 是一个计算属性（getter），它用来获取 `hello` 变量的值。你可以理解为这是 `hello` 变量的读取方法。
- 这个方法会在你访问 `hello` 时被自动调用。
- 例如：`store.hello` 会触发 `get hello()`，返回 `hello.value` 的值。

#### 1.2. **setter (`set hello(value: string | Ref<string>)`)**：

- `set hello()` 是一个自定义的 setter 方法，它控制你如何设置 `hello` 变量的值。你可以将它理解为写入方法。
- 在 setter 中，你可以做一些额外的处理，比如将 `Ref<string>` 解包成普通的 `string`，或者做一些验证等。
- 例如：`store.hello = 'new value'` 会触发 `set hello()` 方法来修改 `hello` 的值。

### 2.专门为 state 添加类型

​	当添加新的 state 属性(包括 `store` 和 `store.$state` )时，你需要将类型添加到 `PiniaCustomStateProperties` 中。与 `PiniaCustomProperties` 不同的是，它只接收 `State` 泛型：

```
import 'pinia'

declare module 'pinia' {
  export interface PiniaCustomStateProperties<S> {
    hello: string
  }
}
```

# 六、Vue-route & pinia

```
import { useUserStore } from '@/stores/user'
import { createPinia } from 'pinia'
import { createApp } from 'vue'
import App from './App.vue'

// ❌  失败，因为它是在创建 pinia 之前被调用的
const userStore = useUserStore()

const pinia = createPinia()
const app = createApp(App)
app.use(pinia)

// ✅ 成功，因为 pinia 实例现在激活了
const userStore = useUserStore()
```

​	如果要在导航守卫内使用store，那么这个store就必须在导航守卫内定义。这是因为在 `router.beforeEach` 中，Vue 的 `store`（无论是 Vuex 还是 Pinia）是通过 `useStore` 函数获取的，而这个函数只能在 Vue 组件或 Vue 相关的上下文中执行。当你在导航守卫外部尝试调用 `useStore()` 时，可能会因为在 Vue 应用的生命周期中尚未安装 Pinia 或者还没有初始化路由而导致报错。









































































# 拓展

PiniaCustomProperties

PiniaPluginContext

PiniaCustomStateProperties<S>

## 1.pinia插件和js对象原型链的区别

### 1.1. **原型链的实现：**

JavaScript 原型链中的共享行为依赖于 `__proto__`，属性访问是通过链式查找实现的：

- 如果对象本身没有某个属性，会沿着原型链查找。
- `Object.prototype` 是所有对象的默认终点。

### 1.2. **Pinia 插件的实现：**

Pinia 插件的内容并不是挂载到 store 的原型上，而是通过 Pinia 的内部机制在创建 store 时**直接注入**到 store 的实例中：

- 插件会返回一个对象（例如 `{ secret: '...' }`）。
- Pinia 会将这个对象的内容**直接合并**到每个 store 实例中，而不是通过原型链动态查找。
