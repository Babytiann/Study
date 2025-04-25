# 一、手动实现new

代码如下：

```js
function myNew(constructor, ...args) {
  // 1. 创建一个空对象
  const obj = {};

  // 2. 将空对象的 __proto__ 指向构造函数的 prototype
  Object.setPrototypeOf(obj, constructor.prototype);

  // 3. 使用构造函数初始化对象
  const result = constructor.apply(obj, args);  // 调用构造函数并传入参数

  // 4. 如果构造函数返回了一个对象，返回它；否则返回 obj
  return result instanceof Object ? result : obj;
}

// 构造函数
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const person = myNew(Person, 'Alice', 30);
console.log(person); // { name: 'Alice', age: 30 }
```

###  逐步解释：

1. `const obj = {};`：创建了一个空对象，它将在后续步骤中作为构造函数的 `this`。
2. `Object.setPrototypeOf(obj, constructor.prototype);`：将空对象的原型指向构造函数的 `prototype` 属性，使得该对象能够访问构造函数的原型方法。
3. `constructor.apply(obj, args);`：调用构造函数，并将 `obj` 作为构造函数内部的 `this`，同时传入额外的参数（`args`）。
4. 最后，如果构造函数返回了一个对象（而不是原始值），就返回该对象，否则返回新创建的 `obj`。

# 二、this指针

`this` 是 JavaScript 中非常重要的概念，它的指向取决于函数的调用方式和上下文。理解 `this` 和上下文，首先需要明确几个基本概念。

### 1. **上下文是什么？**

上下文（Execution Context）是代码执行时的环境，它定义了 `this` 的值。在 JavaScript 中，上下文通常指函数的调用环境，也就是执行该函数时的对象或作用域。

### 2. **普通函数中的 `this`**

普通函数中的 `this` 取决于函数是如何被调用的。以下是一些常见的场景：

- **全局调用**：当函数在全局作用域中调用时，`this` 通常指向全局对象（在浏览器中是 `window`，在 Node.js 中是 `global`）。
- **对象方法调用**：当函数作为对象的方法被调用时，`this` 指向调用该方法的对象。
- **构造函数调用**：当函数通过 `new` 调用时，`this` 指向新创建的对象。
- **事件处理函数**：在事件处理函数中，`this` 通常指向触发事件的元素。

#### 普通函数的例子：

```
// 1. 全局调用
function regularFunc() {
    console.log(this);  // 在浏览器中，this指向 window 对象
}

regularFunc();

// 2. 对象方法调用
const obj = {
    name: 'Alice',
    greet() {
        console.log(this.name);  // this指向obj对象
    }
};

obj.greet();  // 输出 'Alice'

// 3. 构造函数调用
function Person(name) {
    this.name = name;
}

const person = new Person('Bob');
console.log(person.name);  // 输出 'Bob'

// 4. 事件处理函数
document.querySelector('button').addEventListener('click', function() {
    console.log(this);  // this指向触发事件的元素，即button
});
```

### 3. **箭头函数中的 `this`**

箭头函数与普通函数的 `this` 有显著的区别。箭头函数没有自己的 `this`，它会捕获并继承定义时所在上下文的 `this`。这意味着箭头函数的 `this` 永远指向外部函数的 `this`，而不是调用它的对象。

#### 箭头函数的例子：

```
// 1. 箭头函数继承外部this
const obj = {
    name: 'Alice',
    greet: function() {
        setTimeout(() => {
            console.log(this.name);  // this指向外部greet方法的this，即obj对象
        }, 1000);
    }
};

obj.greet();  // 输出 'Alice'，因为箭头函数的this继承了greet方法的this

// 2. 箭头函数在回调中的this
function regularFunc() {
    console.log(this);  // this指向调用regularFunc的对象（在此例中是window或global）
}

setTimeout(regularFunc, 1000);  // 普通函数，this会指向全局对象

setTimeout(() => {
    console.log(this);  // 箭头函数，this会继承外部上下文的this
}, 1000);
```

#### 关键区别总结：

- 普通函数：`this` 的指向取决于函数的调用方式。
  - 在全局调用时，`this` 指向全局对象（浏览器中是 `window`）。
  - 在对象方法调用时，`this` 指向调用该方法的对象。
  - 在构造函数中，`this` 指向新创建的对象。
  - 在事件处理函数中，`this` 指向触发事件的元素。
- **箭头函数**：箭头函数没有自己的 `this`，它会继承定义时上下文中的 `this`。箭头函数中的 `this` 总是指向外部函数的 `this`，并且不会因为调用方式的不同而发生改变。

# 三、React与Vue的更新机制

## 1.渲染

### 1.1. **初始化渲染**

- **构建虚拟 DOM**：当 React 或 Vue 应用程序开始加载时，框架会根据组件的定义、状态和数据生成一个 **虚拟 DOM 树**，这是一种轻量级的 JavaScript 对象，表示组件的结构和视图。
- **渲染虚拟 DOM 到页面**：框架通过将虚拟 DOM 转换为真实的 DOM 元素，最终渲染到浏览器页面中。这个过程包括 DOM 的创建、样式计算等，就像传统渲染中的布局、绘制和合成一样。

### 1.2. **组件状态更新**

当用户与应用程序交互时，例如点击按钮、输入内容，组件的 **状态** 或 **数据** 可能会发生变化。此时，虚拟 DOM 会重新发挥作用，下面是更新流程的详细步骤：

#### a. **触发状态变化**

- 当 **组件的状态**（例如 React 中的 `setState`，Vue 中的 `data` 或 `ref`）发生变化时，框架会标记组件为需要更新。
- 这时，React 或 Vue 不会直接修改真实 DOM，而是会根据新的状态生成一个新的 **虚拟 DOM 树**。

#### b. **虚拟 DOM 更新**

- 新的虚拟 DOM 树与 **旧的虚拟 DOM 树** 进行 **比较**，这就是所谓的 **diffing**（差异化比较）过程。这个过程会找出哪些部分发生了变化，即 **最小化更新** 的区域。
- 虚拟 DOM 的比较是通过算法高效地完成的，React 和 Vue 都有优化过的 diff 算法，用来减少不必要的 DOM 更新操作。

#### c. **生成补丁（Patch）**

- 通过 diffing 算法，React 或 Vue 会生成一个 **补丁**，这个补丁描述了虚拟 DOM 树与真实 DOM 之间的差异。
- 这个补丁包含了需要更新、删除或新增的部分。

### 1.3. **应用补丁到真实 DOM**

- 一旦计算出补丁，React 或 Vue 会将这些更新应用到真实的 DOM 中。这个过程会触发布局（Reflow）和绘制（Repaint），但由于虚拟 DOM 已经尽量优化了最小化更新，所以相比直接操作真实 DOM，这个过程更高效。
- 更新的内容通常只会影响那些发生变化的部分，而不是重新渲染整个页面或大量的 DOM 元素，从而减少了性能开销。

### 1.4. **更新完成后的渲染**

- 真实 DOM 更新后，浏览器会继续进行 **布局（Reflow）** 和 **绘制（Repaint）**，这些步骤与传统渲染流程相同，最终呈现出更新后的 UI。
- React 或 Vue 会监听一些浏览器的变动，如用户输入、窗口尺寸变化等，并通过虚拟 DOM 机制进行高效的重新渲染。

### 1.5. **异步渲染（React Fiber/Vue）**

React 和 Vue 都对渲染过程进行了异步化优化，减少了长时间的阻塞渲染：

- **React Fiber**：React 使用了一个称为 **Fiber** 的架构，它使得 React 的渲染变得更加异步化和分段化。这样可以将渲染任务分割成多个小块，以便在浏览器空闲时完成渲染，减少界面卡顿的现象。
- **Vue 响应式系统**：Vue 的响应式系统会检测数据变化，并通过异步更新队列机制，在下次事件循环中批量更新 DOM，避免重复渲染。

### 1.6. **优化与性能**

框架通过以下方法优化性能：

- **虚拟 DOM diffing**：减少不必要的 DOM 更新，只有发生变化的部分才会更新。
- **批量更新**：例如在 Vue 中，数据更新是异步执行的，Vue 会把多个数据变更合并在一起，减少 DOM 更新的次数。
- **异步渲染**：React Fiber 和 Vue 响应式系统会将更新任务分片执行，以避免卡顿。

### 总结

1. **初始化渲染**：构建虚拟 DOM 并渲染到浏览器。
2. **状态更新**：当组件的状态变化时，生成新的虚拟 DOM。
3. **虚拟 DOM 比较（diffing）**：框架通过对比新旧虚拟 DOM，找出变化的部分。
4. **应用补丁**：将差异应用到真实 DOM，减少不必要的更新。
5. **渲染完成**：浏览器进行布局、绘制等操作，将页面渲染出来。
6. **优化**：通过异步渲染、批量更新、虚拟 DOM diffing 等方式，提高渲染性能。

## 2. React Fiber对比Vue响应式系统

### 2.1. **React Fiber：分片渲染**

React Fiber 是 React 16 引入的一个渲染引擎，它的主要目标是解决 **渲染阻塞** 和 **更新优先级** 问题。React Fiber 将渲染过程分成了多个小任务，使得 React 能够在浏览器的空闲时间里分片执行渲染工作。这样可以避免长时间的渲染阻塞，提升响应速度。

#### **核心概念**：

- **任务分片**：React 将渲染任务分成多个小任务，每个任务执行一个小部分的工作，然后在浏览器空闲时继续执行剩余部分。任务执行会中断和恢复，这样可以避免长时间阻塞主线程。
- **优先级管理**：React Fiber 能够根据任务的优先级决定渲染的顺序。例如，用户输入和交互相关的更新会有更高的优先级，而不那么重要的更新（比如网络请求后的数据更新）会被推迟。
- **事件循环调度**：React 使用一种事件循环机制，控制每个小任务的执行时间，并且可以中断长时间运行的任务，将其他重要的任务优先处理。

#### **优势**：

- **高效的任务调度**：通过优先级和任务分片，React 可以灵活地调度和中断任务，确保页面在较低的渲染负载下响应更快。
- **避免界面卡顿**：长时间渲染操作不会导致页面的卡顿，用户交互更为流畅。

### 2.2. **Vue 响应式系统：异步更新队列**

Vue 使用响应式数据绑定，通过 **异步更新队列** 来优化 DOM 更新。当组件的状态或数据发生变化时，Vue 并不会立即更新 DOM，而是将所有的更新操作放入一个 **异步队列**，在下一个事件循环（宏任务）时统一处理。这避免了每次状态更新时都立即触发 DOM 更新，从而减少了多次渲染和不必要的重复操作。

#### **核心概念**：

- **响应式数据**：Vue 的数据是响应式的，当数据发生变化时，Vue 会记录依赖并将其标记为需要更新的部分。
- **异步队列**：Vue 将多个数据更新合并成一个更新队列，并延迟执行 DOM 更新。这意味着如果在同一个事件循环中多次触发数据变化，Vue 只会触发一次 DOM 更新，避免了不必要的渲染。
- **批量更新**：Vue 的更新是批量处理的，这意味着 Vue 会在下一个事件循环中对 DOM 进行一次性更新，避免每次数据变化时都进行重排和重绘。

#### **优势**：

- **减少重复渲染**：通过异步队列机制，Vue 能够避免在一个事件循环中对 DOM 进行多次更新，减少不必要的重绘和重排。
- **更简洁的实现**：Vue 的响应式系统和队列机制相对简单，代码更易于理解和实现。

### 2.3. **React Fiber 和 Vue 的区别**

虽然 **React Fiber** 和 **Vue 的异步更新机制** 都实现了异步渲染，但它们的实现方式和关注点有所不同：

| **特性**         | **React Fiber**                                              | **Vue 响应式系统**                                         |
| ---------------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| **任务分片**     | 将渲染任务分片，每个小任务在浏览器空闲时执行。               | 无显式任务分片机制，主要通过异步更新队列延迟 DOM 更新。    |
| **优先级管理**   | 支持动态的任务优先级调整，用户交互等任务可以优先执行。       | 优先处理当前事件循环中的所有变化，无法动态调整任务优先级。 |
| **事件循环调度** | 使用事件循环调度机制，可以中断和恢复任务，允许任务在多个事件循环中分片执行。 | 使用宏任务队列批量更新，避免不必要的 DOM 更新。            |
| **交互流畅度**   | React Fiber 更强调动态任务调度和分片执行，响应用户交互更流畅。 | Vue 通过异步队列和批量更新减少重复渲染，性能优化简单有效。 |
| **实现复杂度**   | 相对较复杂，涉及任务调度、优先级、分片等多个方面。           | 实现较为简洁，主要通过响应式系统和异步队列机制完成。       |

### 2.4. **总结**

- **React Fiber** 通过将渲染过程分为多个小任务并使用任务优先级管理和事件循环调度来确保渲染的高效性。它能够更加灵活地处理渲染任务，确保用户交互时的响应速度，适用于复杂的、需要高效调度的场景。

- **Vue 响应式系统** 通过使用异步队列机制，延迟 DOM 更新，避免重复渲染和不必要的重排。它的设计理念更简单，优化手段直接，适合中小型项目或不需要复杂渲染调度的场景。

  

# 四、React比Vue更适配第三方库

###  **虚拟 DOM 和实际 DOM 解绑**

- React 的虚拟 DOM（Virtual DOM）机制，使得 React 本身能够通过差异化更新 DOM 来提高性能。React 可以很好地与操作实际 DOM 的第三方库兼容（例如 jQuery、D3.js 等），这些库不会直接影响 React 的渲染过程。
- 比较 Vue，Vue 在 DOM 更新时会直接修改实际 DOM，这就可能导致一些与外部库的兼容性问题。比如，当 Vue 和 jQuery 一起使用时，jQuery 操作 DOM 的时候，可能会破坏 Vue 的响应式数据流，从而导致渲染不一致。

###  **响应式系统的影响**

- **React：** React 的状态管理是基于组件内部的状态（`useState` 等），其更新过程比较简单、明确，不会干扰外部库的操作。即使外部库改变了 DOM，React 仍然会根据其组件的状态来重新渲染界面。
- **Vue：** Vue 的响应式系统基于 getter/setter，任何数据变化都会引起视图的更新。当 Vue 管理的数据发生变化时，它会通过虚拟 DOM 和响应式机制来重新渲染 DOM。如果此时你使用第三方库（如 jQuery）直接修改 DOM，可能会导致 Vue 认为数据没有发生变化，从而不会触发视图更新，造成视图和数据的不同步。

# 五、使用JS的API实现纯HTML的懒加载

代码如下：

```
//监控元素在视口上出现百分之五十的时候进行对应的操作。 window.innerHeight获取视口高度		//document.documentElement.clientHeight获取Html元素的高度（clientHeight就是高度，documentElement就是<html>元素）

const rect = element.getBoundingClientRect();
const viewportHeight = window.innerHeight || document.documentElement.clientHeight;
const threshold = viewportHeight * 0.5;  // 50% of the viewport height

if (rect.top >= 0 && rect.top <= threshold) {
    // 这里可以开始相应的操作，例如懒加载或动画
}
```

其中`getBoundingClientRect()`返回的就是这个元素对于视口的一些相对位置，如下所示：

```
//getBoundingClientRect()会返回如下数据

{
  top: 100,   // 元素的顶部距离视口顶部 100px
  left: 50,   // 元素的左侧距离视口左侧 50px
  bottom: 300, // 元素的底部距离视口顶部 300px
  right: 150,  // 元素的右侧距离视口左侧 150px
  width: 100,  // 元素的宽度 100px
  height: 200  // 元素的高度 200px
}
```

# 六、ES6新增哪些特性

**块级作用域**（let, const）

**箭头函数**

模板字符串

**解构赋值**

默认参数值

**类**

**模块化**（import , export）

Promise

Symbol

Set和Map

Map和Set的forEach方法

# 七、JS的事件循环机制

## 1.事件循环的工作流程

**1.执行栈（Call Stack）**： JavaScript 代码的执行是在执行栈中进行的，栈中的任务是同步的。执行栈上的任务会按照从上到下的顺序依次执行。同步操作直接进入执行栈，异步操作直接进入任务队列。

**2.任务队列（Task Queue）**： 异步操作的回调函数会被放入任务队列中，等待被执行。任务队列分为两类：

- **宏任务队列（Macro Task Queue）**：如 `setTimeout`、`setInterval`、I/O 操作等。
- **微任务队列（Micro Task Queue）**：如 `Promise` 的回调、`MutationObserver` 等。微任务队列中的任务优先级高于宏任务队列。

**3.事件循环（Event Loop）**： 事件循环的核心就是不断从执行栈中取出任务执行，并在合适的时机将任务从队列中移动到执行栈。具体步骤如下：

- 执行栈为空时，事件循环会查看微任务队列。
- 如果微任务队列不为空，会依次执行微任务队列中的任务，直到队列为空。
- 然后，事件循环会从宏任务队列中取出任务执行，执行一个宏任务后，再次检查微任务队列。
- 这个过程会不断循环。

## 2.任务优先级

**微任务**（如 `Promise` 回调）优先级高于宏任务。即使宏任务队列有任务等待执行，只要微任务队列非空，微任务会优先执行完。

​	微任务是指优先级高于宏任务的异步操作，通常在当前任务执行完毕后立即执行。常见的微任务包括：

1. `Promise` 的 `.then()`、`.catch()`、`.finally()` 回调：
   - `Promise` 的回调函数会被放入微任务队列，在当前宏任务结束后执行。
2. `MutationObserver`：
   - 用于监听 DOM 结构的变化，作为微任务处理。
3. `queueMicrotask()`：
   - 可以手动将一个函数添加到微任务队列，确保该任务会在当前宏任务后立即执行。

**宏任务**包括常见的异步操作（如 `setTimeout`、I/O 操作等），这些任务的执行优先级低于微任务。

​	宏任务队列主要包含以下内容：

1. `setTimeout` 和 `setInterval`：

   - 用于延迟执行某些代码，它们的回调会被加入宏任务队列。

2. `I/O 操作`：

   - 包括文件读取、数据库查询、网络请求等操作。

3. UI 渲染：

   - 浏览器的渲染机制也是一个宏任务，渲染过程中的布局、绘制、合成等都会占用宏任务。

4. 事件回调：

   - 用户触发的事件（如 `click`、`keydown` 等）会被放入宏任务队列。

5. `setImmediate`

   （Node.js）：

   - Node.js 特有的，表示当前事件循环阶段完成后立即执行回调函数，属于宏任务。

6. `requestAnimationFrame`：

   - 用于浏览器的动画相关操作，每次浏览器重绘时会调用回调函数

# 八、React的设计理念都有哪些

### 1. **单向数据流（One-Way Data Flow）**

- 数据在React中是单向流动的。父组件可以通过`props`向子组件传递数据，但子组件不能直接修改父组件的数据。
- 如果子组件需要改变父组件的数据，必须通过触发父组件传递的回调函数来实现。这种方式确保了数据的可预测性和管理的清晰性。

### 2. **声明式编程（Declarative UI）**

- React采用声明式编程，即描述应用的外观，而不是去手动操作DOM。你通过组件的状态（state）和props来描述UI如何根据数据进行渲染，React会根据状态变化自动更新UI。
- 例如，在React中，当状态改变时，React会自动重新渲染UI，而不需要手动调用`document.getElementById()`等方法来更新DOM。

### 3. **组件化（Component-Based Architecture）**

- React的UI由组件组成，组件是可复用的、独立的、封装好的UI单元。每个组件负责自己的一部分UI，并且可以通过`props`与其他组件进行交互。
- 这种方式有助于构建更清晰、可维护的代码结构，并且可以实现UI的复用。

### 4. **虚拟DOM（Virtual DOM）**

- React通过虚拟DOM优化UI更新过程。虚拟DOM是对实际DOM的轻量级表示。每次组件的状态或属性（props）发生变化时，React会先更新虚拟DOM，再将虚拟DOM与实际DOM进行比较（称为“Diff”过程），最后最小化DOM的更新操作。
- 这种方式提高了性能，因为减少了直接操作DOM的次数，避免了不必要的重新渲染。

### 5. **不可变数据（Immutability）**

- React提倡不可变数据，即一旦数据被创建，它不能被修改。这使得React能够更加高效地进行变化检测（如`shouldComponentUpdate`），并减少副作用。
- 在React中，状态变化应该通过创建新的对象或数组来实现，而不是直接修改原来的数据结构。

### 6. **函数式编程（Functional Programming）**

- React鼓励使用函数式编程的方式来编写组件，尤其是函数组件（Function Components）。在函数组件中，UI的渲染逻辑和状态管理通常是声明式的，且组件是纯函数。
- 这使得代码更易于理解、测试和维护。

### 7. **React Hooks（钩子）**

- React 16.8引入了Hooks，使得函数组件也能够使用状态和生命周期等功能，而无需编写类组件。
- 常用的Hooks有`useState`、`useEffect`、`useContext`等，它们使得组件的逻辑更加清晰、简洁。

### 8. **JSX（JavaScript XML）**

- JSX是一种将HTML结构和JavaScript代码混合的语法，它使得React组件的渲染逻辑更具可读性。
- 虽然JSX看起来像是HTML，但它在背后最终会被编译成React的`createElement`调用，并且可以在其中使用JavaScript表达式。

### 9. **纯JavaScript（Pure JavaScript）**

- React本身是用纯JavaScript编写的，且其API可以通过JavaScript的特性（如函数、对象、类等）进行扩展。React不依赖于特定的模板语言，允许开发者用JavaScript编写所有的UI逻辑。

# 九、Map, Set, weakMap, weakSet区别

## 1. **Map**

`Map` 是一个键值对集合，其中每个键（key）和值（value）是有序的。`Map` 可以使用任何类型的值作为键（包括对象、函数等），而不像 `Object` 只能使用字符串或 Symbol 作为键。

**特点：**

- 键值对有顺序（插入顺序）。
- 键可以是任何类型（例如对象、数组、函数等）。
- `Map` 的大小（`size`）是可获取的。
- 有一组内置方法，如 `.set()`, `.get()`, `.has()`, `.delete()` 等。

**示例：**

```
const map = new Map();
map.set('name', 'John');
map.set(1, 'number');
map.set(true, 'boolean');

console.log(map.get('name')); // 'John'
console.log(map.size); // 3
```

## 2. **Set**

`Set` 是一个集合，它只能包含唯一的值。与 `Map` 不同，`Set` 只有值，没有键。

**特点：**

- 只能包含唯一的值，自动去重。
- 值是有序的（插入顺序）。
- 可以包含任何类型的值（对象、函数等）。
- 提供 `.add()`, `.has()`, `.delete()` 等方法。

**示例：**

```
const set = new Set();
set.add(1);
set.add(2);
set.add(2); // 不会添加重复的值
set.add('hello');

console.log(set.has(1)); // true
console.log(set.size); // 3
```

## 3. **WeakMap**

`WeakMap` 与 `Map` 很相似，区别在于它的键是弱引用，这意味着如果某个键没有其他引用指向它，那么它会被垃圾回收机制回收。

**特点：**

- 键必须是对象（不能是基本数据类型）。
- 键是弱引用，即如果没有其他地方引用该键，垃圾回收时会自动清除该键值对。
- `WeakMap` 不支持 `.clear()` 方法。

**示例：**

```
const weakMap = new WeakMap();
let obj = {name: 'John'};
weakMap.set(obj, 'some value');

console.log(weakMap.get(obj)); // 'some value'

obj = null; // `obj` 被垃圾回收
// `weakMap` 中的键值对将被自动清除
```

## 4. **WeakSet**

`WeakSet` 与 `Set` 类似，区别在于它的元素是弱引用。`WeakSet` 只能包含对象，并且这些对象的引用会在没有其他引用时被垃圾回收。

**特点：**

- 只能包含对象。
- 键是弱引用，垃圾回收机制会自动清除。
- 不支持 `.clear()` 方法。

示例：

```
const weakSet = new WeakSet();
let obj1 = {name: 'John'};
let obj2 = {name: 'Jane'};

weakSet.add(obj1);
weakSet.add(obj2);

console.log(weakSet.has(obj1)); // true
obj1 = null; // `obj1` 被垃圾回收，`weakSet` 中的值也会被自动删除
```

# 十、CommonJs和ESModule的区别

## 1. **导入导出方式**

- **CommonJS**：

  - 使用 `require` 来导入模块。
  - 使用 `module.exports` 或 `exports` 来导出模块。

  示例：

  ```
  // 导出模块
  module.exports = function() {
    console.log("Hello from CommonJS");
  };
  
  // 导入模块
  const myModule = require('./myModule');
  myModule();
  ```

- **ESModule**：

  - 使用 `import` 来导入模块。
  - 使用 `export` 或 `export default` 来导出模块。

  示例：

  ```
  // 导出模块
  export function greet() {
    console.log("Hello from ESModule");
  }
  
  // 导入模块
  import { greet } from './myModule';
  greet();
  ```

## 2. **模块加载时机**

- **CommonJS**：模块是**同步加载**的。`require` 会在程序运行时被执行，直到模块加载完毕后才会继续执行后面的代码。
- **ESModule**：模块是**异步加载**的。ESM 设计时考虑到了浏览器环境，采用了异步加载的方式，因此更适合现代的浏览器和前端构建工具（如 Webpack、Vite 等）。

## 3. **使用场景**

- **CommonJS**：最初是为了 Node.js 环境而设计的，专门用于服务器端。CommonJS 模块的同步加载方式很适合服务器端的模块加载。
- **ESModule**：原生支持浏览器，也逐步被引入到 Node.js 中，支持更加灵活的静态分析和优化。随着 JavaScript 模块化的普及，ESM 已经成为现代 JavaScript 标准。

## 4. **兼容性和标准**

- **CommonJS**：是 Node.js 的默认模块格式，但在浏览器中不直接支持。
- **ESModule**：是 ECMAScript 标准的一部分，支持现代浏览器，且自 Node.js 12 版本起也逐渐支持 ESM（通过 `.mjs` 文件扩展名或设置 `type: module`）。

## 5. **导入行为的差异**

- **CommonJS**：
  - 模块是动态加载的，每次 `require` 都会执行模块中的代码，并返回模块的导出。
  - 任何导入的模块都是一个值的副本，后续修改并不会影响导入模块的值。
- **ESModule**：
  - 模块是静态的，ESM 允许静态分析，能优化导入的部分（如树摇优化 Tree Shaking）。
  - 导入的模块是**只读**的，不会被修改（类似于导入的绑定）。如果模块在导出时使用了 `export default` 或 `export`，可以进行按需导入。

## 6. **互操作性**

- CommonJS 和 ESModule 互操作：
  - 在 Node.js 中，CommonJS 和 ESM 模块之间的互操作性比较麻烦。比如，`require` 可以加载 CommonJS 模块，但无法直接加载 ESM 模块（除非使用 `import` 语法或者 `.mjs` 扩展名）。
  - 反之，ESM 模块可以导入 CommonJS 模块，但需要通过 `import` 导入时，`default` 导出会包含 `module.exports` 的内容。

## 7. **文件扩展名**

- CommonJS：
  - 默认使用 `.js` 扩展名。
- ESModule：
  - 在 Node.js 中，使用 `.mjs` 扩展名来区分 ESM 模块，或者在 `package.json` 文件中设置 `type: module` 来标识项目是基于 ESM 的。
  - 浏览器和其他环境中，`.js` 就是 ES 模块的默认扩展名。

------

## 总结：

- **CommonJS**：适用于同步模块加载，主要用于 Node.js，适合传统的后端开发。
- **ESModule**：现代 JavaScript 标准，支持静态分析，适合前端开发和优化，逐步在 Node.js 中得到支持。
