# 一.props

## 1.声明props

声明props，直接用一个花括号括起来，然后使用的时候用单个花括号

```
export default function Clock({ color, time }) {
  return (
    <h1 style={{ color: color }}>
      {time}
    </h1>
  );
}
```

​	在**ts**中，强烈建议给props做一下**类型声明**比如，(可以用`type`、`interface`也可以用`React.FC`)

interface GallaryProps {
    headd: string;
    babytian: number;
}

```
type GallaryProps = {
    headd: string;
    babytian: number;
}

const Gallary = ({headd, babytian}: GallaryProps) =>{
    return (
        <>
            <h1 style={{color: 'red', backgroundColor: 'pink'}}>111</h1>
            <h1 className="bg-rose-50 text-blue-500 font-bold">111</h1>
            <h1>111{headd}</h1>
            <h1>111{babytian}</h1>
        </>
    )
}
export default Gallary

//也可以使用React.FC来自动推断类型，比如下面
import React from 'react';

const Gallary: React.FC<{ headd: string; babytian: number }> = (props) => {
  return (
    <>
      <h1>{props.headd}</h1>
      <h1>{props.babytian}</h1>
    </>
  );
};

export default Gallary;
```

##  2.把JSX作为子组件传递（vue的slot)

​	就是一定要按下面的写法，就可以实现两个组件的嵌套

```
import Avatar from './Avatar.js';

function Card({ children }) {
  return (
    <div className="card">  //这个div是为了方便控制穿进去的元素的样式，这样就能通过设置div。来控制一些样式
      {children}
    </div>
  );
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}
```

​	上面的例子就像是你在card声明了个vue的那种slot，然后放在<Card>标签内的东西，就会自己定位到children上面，**如果你不往标签内部写，完全不用写children**，就正常用就行比如：因为Gallary标签内部没有任何东西

```
function app(){
    return(
        <section>
            <h1>test</h1>
            <img src="src/assets/react.svg" alt="a picture1" />
            <Gallary headd={"abc"} babytian={41}/>
        </section>
    )
}
```

# 二.条件渲染

就是可以直接用if语句去判断，当然你要事先传入一个props去标记，来作为if的条件，比如

```
function Item({ name, isPacked }) {
  if (isPacked){                                     //这里就是条件渲染，用isPacked来判断
    return <li className="item">{name}✅</li>
  }
  return null;                                      //可以返回null
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride 的行李清单</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="宇航服" 
        />
        <Item 
          isPacked={true} 
          name="带金箔的头盔" 
        />
        <Item 
          isPacked={false} 
          name="Tam 的照片" 
        />
      </ul>
    </section>
  );
}
```

这里面的return也可以用三元运算符、与(&&)、赋值给变量来写，这样会更加方便后期想修改

```
//三元运算符
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {isPacked ? (<del>{name + ' ✅'}</del>) : ( name)}
    </li>
  );
}

//与(&&后面可以直接加HTML标签)
function Item({ name, importance }) {
  return (
    <li className="item">
      {name} {importance >0 && <span>(重要性: {importance})</span>}
    </li>
  );
}

//赋值给变量（最复杂但是最灵活）
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = name + " ✅";
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}
```

# 三.响应事件

## 1.使用onClick这种形式的

### 1.1直接在属性里定义执行的函数

​	**这个是传递props在事件中读取**

```
function AlertButton({ message, children }) {
  return (
    <button onClick={() => alert(message)}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <AlertButton message="正在播放！">
        播放电影
      </AlertButton>
      <AlertButton message="正在上传！">
        上传图片
      </AlertButton>
    </div>
  );
}
```

​	**这个是传递props在事件中读取**

​	如上所示`<button onClick={() => alert(message)}>`这里就是直接定义的例子，`onClick`就是告诉react，后面的函数要在改元素被点击的时候执行

### 1.2使用props传递事件

这个跟上面的有区别，这个是直接用props来传递事件

```
function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

function PlayButton({ movieName }) {
  function handlePlayClick() {
    alert(`正在播放 ${movieName}！`);
  }

  return (
    <Button onClick={handlePlayClick}>  //这里的onClick是那个props，而不是响应事件
      播放 "{movieName}"
    </Button>
  );
}

function UploadButton() {
  return (
    <Button onClick={() => alert('正在上传！')}>
      上传图片
    </Button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <PlayButton movieName="魔女宅急便" />
      <UploadButton />
    </div>
  );
}
```

​	当然，你也可以把这个props的名字改成其他的，不一定非得onClick，但是最好这个名字都是有"on"，然后后面一个大写开头的单词，比如下面的用法

```
export default function App() {
  return (
    <Toolbar
      onPlayMovie={() => alert('正在播放！')}
      onUploadImage={() => alert('正在上传！')}
    />
  );
}

function Toolbar({ onPlayMovie, onUploadImage }) {
  return (
    <div>
      <Button onClick={onPlayMovie}>
        播放电影
      </Button>
      <Button onClick={onUploadImage}>
        上传图片
      </Button>
    </div>
  );
}

function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

### 1.3事件冒泡与阻止，阻止默认行为

​	因为使用的是JSX也就是JS的拓展，所以React里面也支持事件冒泡，也就是说当你在子元素点击`button`触发click的时候会冒泡到父组件上去，要阻止也很简单，只需要`e.stopPropagation()`使用一下事件对象来阻止一下就行。如下所示

```
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('你点击了 toolbar ！');
    }}>
      <Button onClick={() => alert('正在播放！')}>
        播放电影
      </Button>
      <Button onClick={() => alert('正在上传！')}>
        上传图片
      </Button>
    </div>
  );
}
```

​	还有一个问题就是，当你提交表单的时候，浏览器有默认行为会刷新一下表单，这个就是浏览器的默认行为，要解决也很简单(e.preventDefault)，示例代码如下

```
export default function Signup() {
  return (
    <form onSubmit={e => {
      e.preventDefault();
      alert('提交表单！');
    }}>
      <input />
      <button>发送</button>
    </form>
  );
}
```

# 四.State组件的记忆(点击按钮下一篇文章)

要添加 state 变量，先从文件顶部的 React 中导入 `useState`：

```
import { useState } from 'react';
```

然后，替换这一行：

```
let index = 0;
```

将其修改为

```
const [index, setIndex] = useState(0);
```

`index` 是一个 state 变量，`setIndex` 是对应的 setter 函数。

这里的 `[` 和 `]` 语法称为[数组解构](https://zh.javascript.info/destructuring-assignment)，它允许你从数组中读取值。 `useState` 返回的数组总是正好有两项。

比如有如下切换文章的代码：

```
import { useState } from 'react';
import { sculptureList } from './data.js';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);

  function handleNextClick() {
    setIndex(index + 1);
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleNextClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <button onClick={handleMoreClick}>
        {showMore ? 'Hide' : 'Show'} details
      </button>
      {showMore && <p>{sculpture.description}</p>}
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
    </>
  );
}
```

1. **组件进行第一次渲染。** 因为你将 `0` 作为 `index` 的初始值传递给 `useState`，它将返回 `[0, setIndex]`。 React 记住 `0` 是最新的 state 值。
2. **你更新了 state**。当用户点击按钮时，它会调用 `setIndex(index + 1)`。 `index` 是 `0`，所以它是 `setIndex(1)`。这告诉 React 现在记住 `index` 是 `1` 并触发下一次渲染。
3. **组件进行第二次渲染**。React 仍然看到 `useState(0)`，但是因为 React *记住* 了你将 `index` 设置为了 `1`，它将返回 `[1, setIndex]`。
4. 以此类推！

## 1.State在一个组件里面可以存在多个

​	就像上面那个代码一样，实现了detail是否显示以及当前显示的是哪篇文章

## 2.State是隔离并且私有的

​	也就是说，你对同一个组件，在父组件中多次复用，每个副本都不会影响彼此的State，[具体看这个实例](https://zh-hans.react.dev/learn/state-a-components-memory#state-is-isolated-and-private)





#  五.渲染

## 表单提交后隐藏表单

在React中，渲染只会在**初次渲染**和**状态更新(State)**的时候触发，根据这个特性，我们可以实现一些东西

```
import { useState } from 'react';

export default function Form() {
  const [isSent, setIsSent] = useState(false);
  const [message, setMessage] = useState('Hi!');
  if (isSent) {
    return <h1>Your message is on its way!</h1>
  }
  return (
    <form onSubmit={(e) => {
      e.preventDefault();
      setIsSent(true);
      sendMessage(message);
    }}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit">Send</button>
    </form>
  );
}

function sendMessage(message) {
  // ...
}
```

​	上面的代码就是在表单提交的时候隐藏表单并展示“Your message is on its way!”，因为state的变化会让React重新渲染，所以当React重新渲染的时候发现isSent已经变成true了，那么就会进入上面的return，那么表单的那个return就不执行了，所以实现了表单的隐藏

# 六.State是一张快照

通俗易懂的讲就是State不会实时改变，只会在你重新渲染的时候才会改变，示例如下

```
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}
```

​	当你点击按钮的时候，将会调用三次setNumber给number这个State+1，但是最终结果他只会加一次，所以哪怕你加一个计时器，等待一段时间再输出这个state的时候，你会发现他依旧输出的是之前的那个state，如下例子

```
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setTimeout(() => {
          alert(number);
        }, 3000);
      }}>+5</button>
    </>
  )
}
```

​	当你点击button让state+5之后，给了一个计时器，3s之后输出state的值，因为state是一张快照，所以就算这个组件刷新之后，这个计时器输出的还是这个state更新之前的值

​	**一个 state 变量的值永远不会在一次渲染的内部发生变化，** 即使其事件处理函数的代码是异步的。在 **那次渲染的** `onClick` 内部，`number` 的值即使在调用 `setNumber(number + 5)` 之后也还是 `0`。它的值在 React 通过调用你的组件“获取 UI 的快照”时就被“固定”了。

**React 会使 state 的值始终“固定”在一次渲染的各个事件处理函数内部**。你无需担心代码运行时 state 是否发生了变化。

# 七.更新多个state(同时、队列)

​	这就要让set函数使用一种不寻常的办法，也就是不是让他对state做什么，而是告诉他对state如何使用

​	你可以像 `setNumber(n => n + 1)` 这样传入一个根据队列中的前一个 state 计算下一个 state 的 **函数**，而不是像 `setNumber(number + 1)` 这样传入 **下一个 state 值**。这是一种告诉 React “用 state 值做某事”而不是仅仅替换它的方法。

​	如下示例，这就可以实现点击一个按钮，然后让一个state连续加三个1的例子

```
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
      }}>+3</button>
    </>
  )
}

```

下面是 React 在执行事件处理函数时处理这几行代码的过程：

1. `setNumber(n => n + 1)`：`n => n + 1` 是一个函数。React 将它加入队列。
2. `setNumber(n => n + 1)`：`n => n + 1` 是一个函数。React 将它加入队列。
3. `setNumber(n => n + 1)`：`n => n + 1` 是一个函数。React 将它加入队列。

当你在下次渲染期间调用 `useState` 时，React 会遍历队列。之前的 `number` state 的值是 `0`，所以这就是 React 作为参数 `n` 传递给第一个更新函数的值。然后 React 会获取你上一个更新函数的返回值，并将其作为 `n` 传递给下一个更新函数，以此类推：

| 更新队列     | `n`  | 返回值      |
| ------------ | ---- | ----------- |
| `n => n + 1` | `0`  | `0 + 1 = 1` |
| `n => n + 1` | `1`  | `1 + 1 = 2` |
| `n => n + 1` | `2`  | `2 + 1 = 3` |

React 会保存 `3` 为最终结果并从 `useState` 中返回。

这就是为什么在上面的示例中点击“+3”正确地将值增加“+3”。

## 命名惯例 

通常可以通过相应 state 变量的第一个字母来命名更新函数的参数：

```
setEnabled(e => !e);

setLastName(ln => ln.reverse());

setFriendCount(fc => fc * 2);
```

如果你喜欢更冗长的代码，另一个常见的惯例是重复使用完整的 state 变量名称，如 `setEnabled(enabled => !enabled)`，或使用前缀，如 `setEnabled(prevEnabled => !prevEnabled)`。

# 八.更新state中的对象

state不仅可以装数字，字符串，布尔值这些不可变量(immutable)，还可以放js对象，但是你放的js对象其实就是一个变量(mutable)

```
const [x, setX] = useState(0);
setX(5);
```

​	上例中只是把x的值从0变成了5，但是数字 `0` 本身并没有发生改变。在 JavaScript 中，无法对内置的原始值，如数字、字符串和布尔值，进行任何更改。

​	现在考虑 state 中存放对象的情况：

```
const [position, setPosition] = useState({ x: 0, y: 0 });
```

​	从技术上来讲，可以改变对象自身的内容。**当你这样做时，就制造了一个 mutation**：

```
position.x = 5;
```

​	然而，虽然严格来说 React state 中存放的对象是可变的，但你应该像处理数字、布尔值、字符串一样将它们视为不可变的。因此你应该替换它们的值，而不是对它们进行修改。这就是改变对象自身的值。

## 1.将state视为只读的

​	但是，虽然我们将js对象的state看做是**mutation**但是也应该将其视为只读的，也就是说必须通过set函数来修改，而不是直接对本身进行修改，示例如下

```
//以下是正确的修改方法
onPointerMove={e => {
  setPosition({
    x: e.clientX,
    y: e.clientY
  });
}}

//以下是错误的修改方法
onPointerMove={e => {
  position.x = e.clientX;
  position.y = e.clientY;
}}
```

直接修改state可能带来的问题，有如下代码：

```
import { useState } from 'react';
import Background from './Background.js';
import Box from './Box.js';

const initialPosition = {
  x: 0,
  y: 0
};

export default function Canvas() {
  const [shape, setShape] = useState({
    color: 'orange',
    position: initialPosition
  });

  function handleMove(dx, dy) {
    shape.position.x += dx;
    shape.position.y += dy;
  }

  function handleColorChange(e) {
    setShape({
      ...shape,
      color: e.target.value
    });
  }

  return (
    <>
      <select
        value={shape.color}
        onChange={handleColorChange}
      >
        <option value="orange">orange</option>
        <option value="lightpink">lightpink</option>
        <option value="aliceblue">aliceblue</option>
      </select>
      <Background
        position={initialPosition}
      />
      <Box
        color={shape.color}
        position={shape.position}
        onMove={handleMove}
      >
        Drag me!
      </Box>
    </>
  );
}

```

​	因为在这里，他直接修改了state

```
  function handleMove(dx, dy) {
    shape.position.x += dx;
    shape.position.y += dy;
  }
```

​	因为这个state的值是由`initialposition`来设置的，所以直接修改state会导致initialposition也跟着改变

## 2.使用展开语法复制对象

​	你的state是个js对象，但你指向改变其中一个属性的值，就可以使用展开语法，想要实现你的需求，最可靠的办法就是创建一个新的对象并将它传递给 `setPerson`。但是在这里，你还需要 **把当前的数据复制到新对象中**，因为你只改变了其中一个字段：

```
setPerson({

  firstName: e.target.value, // 从 input 中获取新的 first name

  lastName: person.lastName,

  email: person.email

});
```

你可以使用 `...` [对象展开](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_object_literals) 语法，这样你就不需要单独复制每个属性。

```
setPerson({

  ...person, // 复制上一个 person 中的所有字段

  firstName: e.target.value // 但是覆盖 firstName 字段 

});
```

加上**动态命名**会更加方便：

```
  function handleChange(e) {
    setPerson({
      ...person,
      [e.target.name]: e.target.value
    });
  }
```

就是使用e.target.name来代替那个属性名，当然动态命名是因为`[]`

## 3.更新一个嵌套对象 

​	就是一个js对象嵌套一个js对象，这就有点烦，需要用两次展开语法（图方便的话）

```
setPerson({
  ...person, // 复制其它字段的数据 
  artwork: { // 替换 artwork 字段 
    ...person.artwork, // 复制之前 person.artwork 中的数据
    city: 'New Delhi' // 但是将 city 的值替换为 New Delhi！
  }
});
```

## 4.使用第三方库immer

他会让你state的js对象更新起来非常方便

尝试使用 Immer:

1. 运行 `npm install use-immer` 添加 Immer 依赖

2. 用 `import { useImmer } from 'use-immer'` 替换掉 `import { useState } from 'react'`

   ```
   const [person, updatePerson] = useImmer({
       name: 'Niki de Saint Phalle',
       artwork: {
         title: 'Blue Nana',
         city: 'Hamburg',
         image: 'https://i.imgur.com/Sd1AgUOm.jpg',
       }
     });
   
    function handleNameChange(e) {
       updatePerson(draft => {
         draft.name = e.target.value;
       });
     }
   ```
   
   就像上面那样直接传一个箭头函数，然后你要改的键值放进去就可以

### Immer 是如何运行的？

​	由 Immer 提供的 `draft` 是一种特殊类型的对象，被称为 [Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，它会记录你用它所进行的操作。这就是你能够随心所欲地直接修改对象的原因所在！从原理上说，Immer 会弄清楚 `draft` 对象的哪些部分被改变了，并会依照你的修改创建出一个全新的对象。



## 5.使用state来实现Vue的v-model

```
import { useState } from 'react';

export default function Scoreboard() {
  const [player, setPlayer] = useState({
    firstName: 'Ranjani',
    lastName: 'Shettar',
    score: 10,
  });

  function handlePlusClick() {
    setPlayer({
      ...player,
      score: player.score + 1,
    });
  }

  function handleFirstNameChange(e) {
    setPlayer({
      ...player,
      firstName: e.target.value,
    });
  }

  function handleLastNameChange(e) {
    setPlayer({
      ...player,
      lastName: e.target.value
    });
  }

  return (
    <>
      <label>
        Score: <b>{player.score}</b>
        {' '}
        <button onClick={handlePlusClick}>
          +1
        </button>
      </label>
      <label>
        First name:
        <input
          value={player.firstName}
          onChange={handleFirstNameChange}
        />
      </label>
      <label>
        Last name:
        <input
          value={player.lastName}
          onChange={handleLastNameChange}
        />
      </label>
    </>
  );
}
```

​	就是使用响应事件onChange来监听输入框内容的改变然后调用封装完的函数来修改State

# 九.用State响应输入

## 1.命令式UI和声明式UI

​	首先是命令式UI和声明式UI，最大区别就是。命令式UI根据用户的操作来执行某些代码，实现UI的改变比如:

- 命令式 UI 的核心是 **开发者明确地指定每个 UI 元素的行为和状态变化**。也就是说，开发者需要直接操作 DOM 元素，并告知它们该怎么做。例如，你提到的代码：

```
async function handleFormSubmit(e) {
  e.preventDefault();
  disable(textarea);
  disable(button);
  show(loadingMessage);
  hide(errorMessage);
  try {
    await submitForm(textarea.value);
    show(successMessage);
    hide(form);
  } catch (err) {
    show(errorMessage);
    errorMessage.textContent = err.message;
  } finally {
    hide(loadingMessage);
    enable(textarea);
    enable(button);
  }
}
```

在这段代码中，开发者显式地控制了 DOM 的每个细节：

1. **`disable()` 和 `enable()`**：禁用或启用表单元素（如按钮和文本框）。
2. **`show()` 和 `hide()`**：显式控制元素的显示与隐藏。
3. **通过 `await submitForm()` 处理网络请求**：根据结果动态更新 UI 元素的状态。

这就是命令式的特点：开发者通过修改 DOM 和手动管理每个组件的行为，来实现 UI 的变化。

- 声明式 UI 关注 **根据状态描述组件应该是什么样子**。开发者声明 UI 组件的结构和状态变化，而框架（如 React）会根据这些声明自动更新 UI，而不需要开发者直接去操作 DOM 元素。

  ```
  import React, { useState } from 'react';
  
  function Form() {
    const [inputValue, setInputValue] = useState('');
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    const [success, setSuccess] = useState(false);
  
    const handleSubmit = async () => {
      setLoading(true);
      try {
        await submitForm(inputValue);
        setSuccess(true);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
  
    return (
      <form onSubmit={handleSubmit}>
        <textarea
          value={inputValue}
          onChange={(e) => setInputValue(e.target.value)}
          disabled={loading}
        />
        <button type="submit" disabled={!inputValue || loading}>
          {loading ? 'Submitting...' : 'Submit'}
        </button>
  
        {loading && <div>Loading...</div>}
        {error && <div>{error}</div>}
        {success && <div>Success!</div>}
      </form>
    );
  }
  
  async function submitForm(answer) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (answer.toLowerCase() === 'istanbul') {
          resolve();
        } else {
          reject(new Error('Wrong answer'));
        }
      }, 1500);
    });
  }
  ```

  在这个声明式的代码中，你并不直接去操作 DOM 元素，而是声明了 UI 应该如何根据 `inputValue`、`loading`、`error`、`success` 等状态来更新。例如：

  - `disabled={loading}` 让按钮在加载时禁用。
  - `{loading && <div>Loading...</div>}` 会在 `loading` 状态为 `true` 时显示加载消息。
  - `setLoading(true)`、`setError`、`setSuccess` 等更新状态，React 会根据这些状态自动重新渲染组件，显示不同的 UI 内容。

### 总结

- **命令式 UI**：开发者明确地控制 DOM 元素的状态和操作（如通过 `show()`、`hide()` 来显示或隐藏元素，手动禁用和启用按钮等）。
- **声明式 UI**：开发者声明组件的状态，框架会根据状态自动更新 UI，而不需要手动操作 DOM。

## 2.声明式地考虑 UI 

1. **定位**你的组件中不同的视图状态
2. **确定**是什么触发了这些 state 的改变
3. **表示**内存中的 state（需要使用 `useState`）
4. **删除**任何不必要的 state 变量
5. **连接**事件处理函数去设置 state

这是创建声明式UI的五个大步骤，可以看[原文档](https://zh-hans.react.dev/learn/reacting-to-input-with-state#)

​	其实就是使用不同的return(也可以只有一个return，但是要加上**if以及使用State**，来决定不同state的时候return的是哪一块，实现展现不同的组件

​	下面是**声明式UI的示例**，实现了一个表单，[源代码](https://zh-hans.react.dev/learn/reacting-to-input-with-state#step-5-connect-the-event-handlers-to-set-state)

```
import { useState } from 'react';

export default function Form() {
  const [answer, setAnswer] = useState('');
  const [error, setError] = useState(null);
  const [status, setStatus] = useState('typing');

  if (status === 'success') {
    return <h1>That's right!</h1>
  }

  async function handleSubmit(e) {
    e.preventDefault();
    setStatus('submitting');
    try {
      await submitForm(answer);
      setStatus('success');
    } catch (err) {
      setStatus('typing');
      setError(err);
    }
  }

  function handleTextareaChange(e) {
    setAnswer(e.target.value);
  }

  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form onSubmit={handleSubmit}>
        <textarea
          value={answer}
          onChange={handleTextareaChange}
          disabled={status === 'submitting'}
        />
        <br />
        <button disabled={
          answer.length === 0 ||
          status === 'submitting'
        }>
          Submit
        </button>
        {error !== null &&
          <p className="Error">
            {error.message}
          </p>
        }
      </form>
    </>
  );
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let shouldError = answer.toLowerCase() !== 'lima'
      if (shouldError) {
        reject(new Error('Good guess but a wrong answer. Try again!'));
      } else {
        resolve();
      }
    }, 1500);
  });
}
```

# 十.Reducer

​	这个东西像是一个对State的整合，当你要修改多个State的时候不需要去调用每一个State的set，而是直接使用Reducer统一改变所有的State，这样大大降低了复杂程度

Reducer 是处理状态的另一种方式。你可以通过三个步骤将 `useState` 迁移到 `useReducer`：

1. 将设置状态的逻辑 **修改** 成 dispatch 的一个 action；

2. **编写** 一个 reducer 函数；

3. 在你的组件中 **使用** reducer。



​	**dispatch就相当于原先State的set函数**，每次调用dispatch都会被Reducer函数捕获（就是useReducer的第一个参数所对应的函数），捕获之后然后根据dispatch中的type来匹配进行什么操作

​	Reducer函数有两个参数，第一个参数是state，第二个参数就是action，**当触发dispatch的时候会把dispatch括号中的那个对象传进action参数**

比如：

## 1.创建不同action

这是一个创建多个action的例子，每一个action的类型由type属性区分，每一个dispatch就是一个action

```
function handleAddTask(text) {
  dispatch({
    type: 'added',
    id: nextId++,
    text: text,
  });
}

function handleChangeTask(task) {
  dispatch({
    type: 'changed',
    task: task,
  });
}

function handleDeleteTask(taskId) {
  dispatch({
    type: 'deleted',
    id: taskId,
  });
}
```

## 2.创建Reducer函数

​	Reducer函数才是对每个不同action进行对应处理的地方

```
function tasksReducer(tasks, action) {
  if (action.type === 'added') {
    return [
      ...tasks,
      {
        id: action.id,
        text: action.text,
        done: false,
      },
    ];
  } else if (action.type === 'changed') {
    return tasks.map((t) => {
      if (t.id === action.task.id) {
        return action.task;
      } else {
        return t;
      }
    });
  } else if (action.type === 'deleted') {
    return tasks.filter((t) => t.id !== action.id);
  } else {
    throw Error('未知 action: ' + action.type);
  }
}
```

​	其中tasks就是那个原来的State，action就是传进去的触发的那个dispatch

## 3.使用Reducer

```
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
    //这个解构的数组，第二个参数dispatch就是这个useReducer返回的钩子，所以下面的函数用的都是dispatch来触发
    //如果你把dispatch换成其他单词那么后面dispatch也要换一下
    
  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>布拉格的行程安排</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('未知 action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: '参观卡夫卡博物馆', done: true},
  {id: 1, text: '看木偶戏', done: false},
  {id: 2, text: '打卡列侬墙', done: false}
];
```

​	其实跟使用State的set没什么区别，就是创建一个Reducer函数以及把调用原来的set的函数给换成dispatch就行，也可以把Reducer**单独放一个文件夹**然后导入过来就可以。Reducer返回的也是一整个State

## 4.使用Immer简化Reducer

​	与在平常的 state 中 [修改对象](https://zh-hans.react.dev/learn/updating-objects-in-state#write-concise-update-logic-with-immer) 和 [数组](https://zh-hans.react.dev/learn/updating-arrays-in-state#write-concise-update-logic-with-immer) 一样，你可以使用 `Immer` 这个库来简化 `reducer`。在这里，[`useImmerReducer`](https://github.com/immerjs/use-immer#useimmerreducer) 让你**可以通过 `push` 或 `arr[i] =` 来修改 state** ：

```
import { useImmerReducer } from 'use-immer';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

function tasksReducer(draft, action) {
  switch (action.type) {
    case 'added': {
      draft.push({
        id: action.id,
        text: action.text,
        done: false,
      });
      break;
    }
    case 'changed': {
      const index = draft.findIndex((t) => t.id === action.task.id);
      draft[index] = action.task;
      break;
    }
    case 'deleted': {
      return draft.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('未知 action：' + action.type);
    }
  }
}

export default function TaskApp() {
  const [tasks, dispatch] = useImmerReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>布拉格的行程安排</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: '参观卡夫卡博物馆', done: true},
  {id: 1, text: '看木偶戏', done: false},
  {id: 2, text: '打卡列侬墙', done: false},
];
```

[例子](https://zh-hans.react.dev/learn/extracting-state-logic-into-a-reducer#challenges)

# 十一.Ref

​	这玩意儿底层是**用State写**的，只是他跟State不一样的点就是，他的值要用.current访问以及他更新的时候是事实更新并且**不会触发DOM重新渲染**

## 1.使用Ref操作DOM

​	`useRef` Hook 返回一个对象，该对象有一个名为 `current` 的属性。最初，`myRef.current` 是 `null`。当 React 为这个 `<div>` 创建一个 DOM 节点时，React 会把对该节点的引用放入 `myRef.current`。然后，你可以从 [事件处理器](https://zh-hans.react.dev/learn/responding-to-events) 访问此 DOM 节点，并使用在其上定义的内置[浏览器 API](https://developer.mozilla.org/docs/Web/API/Element)。e.g. `focus()`   [`scrollIntoView()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollIntoView) 

```
// 你可以使用任意浏览器 API，例如：
myRef.current.scrollIntoView();

//当用户点击那个按钮的时候就会聚焦输入框，这个例子就是用ref去操作的DOM
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        聚焦输入框
      </button>
    </>
  );
}

```

这个是在当前页面滚动到另一个元素的例子，API: scrollIntoView()

```
import { useRef } from 'react';

export default function CatFriends() {
  const firstCatRef = useRef(null);
  const secondCatRef = useRef(null);
  const thirdCatRef = useRef(null);

  function handleScrollToFirstCat() {
    firstCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToSecondCat() {
    secondCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToThirdCat() {
    thirdCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  return (
    <>
      <nav>
        <button onClick={handleScrollToFirstCat}>
          Neo
        </button>
        <button onClick={handleScrollToSecondCat}>
          Millie
        </button>
        <button onClick={handleScrollToThirdCat}>
          Bella
        </button>
      </nav>
      <div>
        <ul>
          <li>
            <img
              src="https://placecats.com/neo/300/200"
              alt="Neo"
              ref={firstCatRef}
            />
          </li>
          <li>
            <img
              src="https://placecats.com/millie/200/200"
              alt="Millie"
              ref={secondCatRef}
            />
          </li>
          <li>
            <img
              src="https://placecats.com/bella/199/200"
              alt="Bella"
              ref={thirdCatRef}
            />
          </li>
        </ul>
      </div>
    </>
  );
}
```

## 2.访问另一个组件的DOM节点

​	就是直接 [像其它 prop 一样](https://zh-hans.react.dev/learn/passing-props-to-a-component) 将 ref 从父组件传递给子组件。然后子组件拿去用就可以了

```
import { useRef } from 'react';

function MyInput({ ref }) {

  return <input ref={ref} />;

}

function MyForm() {

  const inputRef = useRef(null);

  return <MyInput ref={inputRef} />

}
```



























# 拓展：

## 1.React的Hook都有哪些

### 基础 Hook

1. **`useState`**
   用于声明组件状态。

   ```
   const [state, setState] = useState(initialState);
   ```

2. **`useEffect`**
   用于执行副作用操作（如数据获取、DOM操作等），类似于类组件中的`componentDidMount`、`componentDidUpdate`、`componentWillUnmount`。

   ```
   useEffect(() => {
     // 执行副作用操作
     return () => {
       // 清理副作用
     };
   }, [dependencies]);
   ```
   
   Vue的`onMounted`：相当于React的`useEffect`（不带依赖数组或空数组）。
   
   Vue的`onUpdated`：相当于React的`useEffect`（带有依赖数组）。
   
   Vue的`onUnmounted`：相当于React的`useEffect`返回的清理函数。
   
3. **`useContext`**
   用于订阅React上下文，获取上下文中的值。

   ```
   const value = useContext(MyContext);
   ```

### 高级 Hook

1. **`useReducer`**
   用于管理复杂状态，类似于Redux的`reducer`。

   ```
   const [state, dispatch] = useReducer(reducer, initialState);
   ```

2. **`useCallback`**
   用于返回一个缓存的回调函数，避免因组件重渲染而导致回调函数重新创建。

   ```
   const memoizedCallback = useCallback(() => {
     // 回调函数
   }, [dependencies]);
   ```

3. **`useMemo`**
   用于缓存计算结果，避免在每次渲染时进行重复计算。

   ```
   const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
   ```

4. **`useRef`**
   用于访问DOM元素或持有可变数据，且不会引起组件重新渲染。

   ```
   const myRef = useRef(initialValue);
   ```

5. **`useImperativeHandle`**
   自定义子组件暴露给父组件的实例值，通常与`forwardRef`一起使用。

   ```
   useImperativeHandle(ref, () => ({
     // 父组件可以访问的值
   }));
   ```

6. **`useLayoutEffect`**
   与`useEffect`类似，但它会在DOM更新后同步执行，适用于需要直接操作DOM或测量布局的情况。

   ```
   useLayoutEffect(() => {
     // 操作DOM
   }, [dependencies]);
   ```

### 自定义 Hook

1. 自定义 Hook

   你可以创建自己的Hooks，封装逻辑以提高代码复用性。

   ```
   function useCustomHook() {
     // 自定义逻辑
     return value;
   }
   ```

## 2.React的所有事件

​	在 React 中，事件处理函数以 `on` 开头，后面跟随相应的事件名。除了 `onPointerMove`、`onClick` 和 `onChange`，常见的 React 响应事件包括：

### 1. **鼠标事件**

- `onMouseDown`: 鼠标按下时触发。
- `onMouseUp`: 鼠标松开时触发。
- `onMouseMove`: 鼠标移动时触发。
- `onMouseEnter`: 鼠标进入元素时触发。
- `onMouseLeave`: 鼠标离开元素时触发。
- `onMouseOver`: 鼠标经过元素时触发（包含子元素）。
- `onMouseOut`: 鼠标离开元素时触发（包含子元素）。

### 2. **键盘事件**

- `onKeyDown`: 按键按下时触发。
- `onKeyPress`: 按键按下并松开时触发（已被弃用，建议使用 `onKeyDown` 或 `onKeyUp`）。
- `onKeyUp`: 按键松开时触发。

### 3. **输入事件**

- `onInput`: 当输入框的内容发生变化时触发。
- `onChange`: 当输入框的值发生变化时触发（用于表单元素，如 `input`, `textarea`）。
- `onBlur`: 元素失去焦点时触发。
- `onFocus`: 元素获得焦点时触发。
- `onInvalid`: 表单输入无效时触发。

### 4. **表单事件**

- `onSubmit`: 提交表单时触发。
- `onReset`: 重置表单时触发。
- `onSelect`: 用户选择文本时触发。

### 5. **触摸事件（Touch events）**

- `onTouchStart`: 触摸屏幕时触发。
- `onTouchMove`: 在触摸屏幕时移动时触发。
- `onTouchEnd`: 触摸结束时触发。
- `onTouchCancel`: 触摸事件被系统取消时触发。

### 6. **拖放事件**

- `onDrag`: 拖动时触发。
- `onDragStart`: 开始拖动时触发。
- `onDragEnd`: 拖动结束时触发。
- `onDragEnter`: 拖动元素进入目标区域时触发。
- `onDragLeave`: 拖动元素离开目标区域时触发。
- `onDragOver`: 拖动元素悬停在目标区域时触发。
- `onDrop`: 在目标区域释放拖动元素时触发。

### 7. **其他常见事件**

- `onContextMenu`: 右键菜单触发时触发。
- `onFocus`: 元素获得焦点时触发。
- `onScroll`: 当元素滚动时触发。
- `onResize`: 当元素的尺寸发生变化时触发（主要是对于窗口或可调节的元素）。

## 3.useEffect对比useLayoutEffect

### 3.1. **`useEffect` 执行流程**

`useEffect` 是 React 中用于处理副作用的钩子，它的执行流程大致如下：

1. **组件渲染**：React 会先渲染组件（包括计算虚拟 DOM 和 diff 算法）。此时，`useEffect` 内的副作用函数不会立即执行。
2. **DOM 更新**：React 会根据渲染结果更新真实 DOM。
3. **浏览器绘制**：浏览器会根据更新后的 DOM 执行布局和绘制（绘制到屏幕上）。这时用户可以看到界面上的变化。
4. **`useEffect` 执行**：在 DOM 完成渲染和浏览器绘制之后，`useEffect` 中的副作用函数才会异步执行。

### 3.2.**`useLayoutEffect` 执行流程**

`useLayoutEffect` 和 `useEffect` 很相似，但它的执行时机不同。它的执行流程如下：

1. **组件渲染**：React 会先渲染组件（计算虚拟 DOM 和 diff 算法）。此时，`useLayoutEffect` 内的副作用函数也不会立即执行。
2. **DOM 更新**：React 会根据渲染结果更新真实 DOM。
3. **`useLayoutEffect` 执行**：`useLayoutEffect` 中的副作用函数会在 DOM 更新后、浏览器绘制前同步执行。此时你可以安全地操作或测量 DOM，因为 DOM 已经更新，但浏览器还没有绘制页面。
4. **浏览器绘制**：浏览器会根据更新后的 DOM 执行布局和绘制（绘制到屏幕上）。这时用户看到界面上的变化。
