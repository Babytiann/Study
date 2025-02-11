## 一、 [Transition](https://cn.vuejs.org/guide/built-ins/transition.html#transition)

`<Transition>` 是一个内置组件，这意味着它在任意别的组件中都可以被使用，无需注册

<img src="D:\Web学习\前端\assets\image-20241113202914470.png" alt="image-20241113202914470" style="zoom:67%;" />

当一个 `<Transition>` 组件中的元素被插入或移除时，会发生下面这些事情：

1. Vue 会自动检测目标元素是否应用了 CSS 过渡或动画。如果是，则一些 [CSS 过渡 class](https://cn.vuejs.org/guide/built-ins/transition.html#transition-classes) 会在适当的时机被添加和移除。
2. 如果有作为监听器的 [JavaScript 钩子](https://cn.vuejs.org/guide/built-ins/transition.html#javascript-hooks)，这些钩子函数会在适当时机被调用。
3. 如果没有探测到 CSS 过渡或动画、也没有提供 JavaScript 钩子，那么 DOM 的插入、删除操作将在浏览器的下一个动画帧后执行。



当触发动画的时候，会有以下**六个CSS**参与样式控制，他们分别的作用如下：

1. `v-enter-from`：进入动画的起始状态。在元素插入之前添加，在元素插入完成后的下一帧移除。
2. `v-enter-active`：进入动画的生效状态。应用于整个进入动画阶段。在元素被插入之前添加，在过渡或动画完成之后移除。这个 class 可以被用来定义进入动画的持续时间、延迟与速度曲线类型。
3. `v-enter-to`：进入动画的结束状态。在元素插入完成后的下一帧被添加 (也就是 `v-enter-from` 被移除的同时)，在过渡或动画完成之后移除。
4. `v-leave-from`：离开动画的起始状态。在离开过渡效果被触发时立即添加，在一帧后被移除。
5. `v-leave-active`：离开动画的生效状态。应用于整个离开动画阶段。在离开过渡效果被触发时立即添加，在过渡或动画完成之后移除。这个 class 可以被用来定义离开动画的持续时间、延迟与速度曲线类型。
6. `v-leave-to`：离开动画的结束状态。在一个离开动画被触发后的下一帧被添加 (也就是 `v-leave-from` 被移除的同时)，在过渡或动画完成之后移除。



### 1.进入与离开动画的定义

说白了就是让你看到和让你看不到的意思

1. **进入动画 (`v-enter`)**：
   - **进入**动画指的是一个元素被添加到 DOM 中并显示出来的过程。
   - 当某个元素从不可见状态（比如，`display: none` 或 `opacity: 0`）变成可见状态（比如，`display: block` 或 `opacity: 1`）时，就是进入动画。
   - 进入动画是由 Vue 在元素插入到 DOM 后触发的，通常是通过 `v-if` 或 `v-show` 来控制元素的显示与隐藏。
2. **离开动画 (`v-leave`)**：
   - **离开**动画指的是一个元素从可见状态变为不可见状态的过程。
   - 当一个元素被从 DOM 中移除（如通过 `v-if` 或 `v-show` 切换为 `false`）时，离开动画会被触发。
   - 离开动画是元素在被移除之前的过渡效果，通常会在元素被隐藏或删除前执行，给用户提供平滑的消失效果。

### 2.为过渡效果命名

​	使用`name`来添加名称这样在css里面就可以**有针对的使用选择器**

<img src="D:\Web学习\前端\assets\image-20241113214026871.png" alt="image-20241113214026871" style="zoom:67%;" />

### 3.CSS 的 transition

​	`<Transition>` 一般都会搭配[原生 CSS 过渡](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions)一起使用，也就是说你可以使用`transition`为上面六个每一个属性都定义一个过渡动画

### 4.CSS 的 animation

​	跟上面一样，都是结合css的某个东西，用了再学

### 5.自定义过渡 class

​	<img src="D:\Web学习\前端\assets\image-20241113215339090.png" alt="image-20241113215339090" style="zoom:67%;" />

```
enter-active-class="animate__animated animate__tada"
```

上面那个代码表示`enter-active-class`这个类匹配到`animate__animated`这个类名，并且应用`animate__tada`这个CSS效果

### 6.同时使用 transition 和 animation

​	为了防止css产生歧义，就要对<Transition>标签使用`type`属性，告诉css主要看哪个

### 7.深层级过渡与显式过渡时长

​	就是在父元素跟子元素采取不一样的过渡策略

​	<img src="D:\Web学习\前端\assets\image-20241113221004803.png" alt="image-20241113221004803" style="zoom:67%;" />

### 8.[过渡模式](https://cn.vuejs.org/guide/built-ins/transition.html#transition-modes)

​	在之前的例子中，进入和离开的元素都是在同时开始动画的，因此我们不得不将它们设为 `position: absolute` 以避免二者同时存在时出现的布局问题。

​	然而，很多情况下这可能并不符合需求。我们可能想要先执行离开动画，然后在其完成**之后**再执行元素的进入动画。手动编排这样的动画是非常复杂的，好在我们可以通过向 `<Transition>` 传入一个 `mode` prop 来实现这个行为：

```
<Transition mode="out-in">
```

​	这样就会强制，进入的元素在离开的元素动画结束之后再进来，还有一个是`“in-out”`就是先进后出，只有进入的那个组件进完了出去的组件才会出



还有很多以后再学

## 二、 [KeepAlive](https://cn.vuejs.org/guide/built-ins/keep-alive.html)

​	默认情况下，一个组件实例在被替换掉后会被销毁，那么就会导致已有的状态都被清除，比如：

<img src="D:\Web学习\前端\assets\image-20241113222813483.png" alt="image-20241113222813483" style="zoom:70%;" />

这里有一个计数器，初始值是0，现在是3，但是如果你切换到B再切换回来这个3就会被清除又变成0了。要解决这个问题，我们可以用 `<KeepAlive>` 内置组件将这些**动态组件包装起来**

```
<!-- 非活跃的组件将会被缓存！ -->
<KeepAlive>
  <component :is="activeComponent" />
</KeepAlive>
```

### 	1. 包含/排除

​		就是指定KeepAlive保存什么变量，不保存什么变量

```
<!-- 以英文逗号分隔的字符串 -->
<KeepAlive include="a,b">
  <component :is="view" />
</KeepAlive>

<!-- 正则表达式 (需使用 `v-bind`) -->
<KeepAlive :include="/a|b/">
  <component :is="view" />
</KeepAlive>

<!-- 数组 (需使用 `v-bind`) -->
<KeepAlive :include="['a', 'b']">
  <component :is="view" />
</KeepAlive>
```

### 	2. 最大缓存实例数

​		给出一个最大缓存的个数，如果超过了，那么最久没访问的那个实例被销毁，装进去新的

```
<KeepAlive :max="10">
  <component :is="activeComponent" />
</KeepAlive>
```

### 	3. 缓存实例的生命周期

​		当一个组件实例从 DOM 上移除但因为被 `<KeepAlive>` 缓存而仍作为组件树的一部分时，它将变为**不活跃**状态而不是被卸载一个持续存在的组件可以通过 [`onActivated()`](https://cn.vuejs.org/api/composition-api-lifecycle.html#onactivated) 和 [`onDeactivated()`](https://cn.vuejs.org/api/composition-api-lifecycle.html#ondeactivated) 注册相应的两个状态的生命周期钩子：

```
<script setup>
import { onActivated, onDeactivated } from 'vue'

onActivated(() => {
  // 调用时机为首次挂载
  // 以及每次从缓存中被重新插入时
})

onDeactivated(() => {
  // 在从 DOM 上移除、进入缓存
  // 以及组件卸载时调用
})
</script>
```

请注意：

- `onActivated` 在组件挂载时也会调用，并且 `onDeactivated` 在组件卸载时也会调用。
- 这两个钩子不仅适用于 `<KeepAlive>` 缓存的根组件，也适用于缓存树中的后代组件。

​	当一个组件被 `<KeepAlive>` 缓存时，不仅这个组件本身会被缓存，**它的嵌套的子组件（后代组件）也会继承 `<KeepAlive>` 的缓存机制**。如果这些子组件有状态，缓存它们也可以保留它们的状态。但是如果你没用他们，只是引入，那就需要放到include才会被一起缓存




------



# TS支持

## 三、defineSlots()宏

​	用来为插槽提供类型定义，确保父组件传回来的插槽props符合要求

子组件：

```
<template>
  <div class="card">
    <div v-if="$slots.header">
      <slot name="header" />
    </div>
    <div v-if="$slots.default">
      <slot />
    </div>
    <div v-if="$slots.footer">
      <slot name="footer" />
    </div>
  </div>
</template>

<script setup lang="ts">
// 定义插槽类型
const slots = defineSlots<{
  default(props: { title: string; content: string }): any
  header(props: { title: string }): any
  footer(props: { year: number }): any
}>()
</script>
```

解释：

- `default(props: { title: string; content: string })`：默认插槽接收 `title` 和 `content` 两个 `string` 类型的属性。
- `header(props: { title: string })`：`header` 插槽接收 `title` 属性，类型是 `string`。
- `footer(props: { year: number })`：`footer` 插槽接收 `year` 属性，类型是 `number`。

父组件：

```
<template>
  <Card>
    <template #header="{ title }">
      <h1>{{ title }}</h1>
    </template>
    
    <template #default="{ title, content }">
      <h2>{{ title }}</h2>
      <p>{{ content }}</p>
    </template>
    
    <template #footer="{ year }">
      <footer>© {{ year }}</footer>
    </template>
  </Card>
</template>

<script setup lang="ts">
import Card from './Card.vue'
</script>
```



- **对于简单的插槽**，你不需要 `defineSlots`，可以直接使用普通的插槽。
- **对于复杂的插槽，特别是作用域插槽**，使用 `defineSlots` 可以显著提高类型安全性，防止错误。
- `defineSlots` 主要是为了 **类型声明和类型检查**，并不是 Vue 插槽机制的必要部分，它更适用于需要高强度类型安全的场景。

但是当我们组合使用 `作用域插槽` 和 `defineSlots` 时，会发生**类型冲突**。因为：

- **作用域插槽**中的数据是**子组件**暴露给**父组件**的，而 `defineSlots` 用来声明父组件传递给子组件插槽的 `props`。
- 在上面的示例中，**子组件**既使用 `作用域插槽` 向父组件传递数据，又使用 `defineSlots` 作为声明父组件插槽类型的手段。**这两个机制互相冲突**。
- 这两个东西的**语法相同**

# 四、TransitionGroup

​	Transition应用单个元素，或者单个组件，TransitionGroup则应用于一堆元素以及一堆组件，当使用`TransitionGroup`的时候父级元素的样式可能会失效比如`display: flex`，原因如下：

​	当你使用 TransitionGroup 时，父级元素的样式可能会失效，因为 TransitionGroup 是通过动态地插入和删除子元素来控制动画效果的，而这些子元素的插入和删除是由 TransitionGroup 本身来控制的。因此，父级元素的某些样式（如 flex 布局等）可能会在子元素变动时丢失。



