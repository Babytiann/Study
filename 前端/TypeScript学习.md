[访问ts文档](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)

### 一、静态类型检查

​	Ts语言会在代码运行前检查代码的潜在问题，但是js语言只会在运行之后才知道

### 二、tsc，TS语言的编译器

​		这玩意儿会在你编译一个ts语言的文件的时候，自动生成js语言的文件，但当有些时候遇到`js`允许但是`ts`**不允许**的语句时，比如下面的

```
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}

greet("Brendan"); // 错误：Expected 2 arguments, but got 1.
```

​	ts就会报错，**但是他还是会生成一个js文件**，如果你不想在ts报错的时候还生成js文件，可以使用[`noEmitOnError`](https://www.typescriptlang.org/tsconfig#noEmitOnError)

使用如下：

```ts
tsc --noEmitOnError hello.ts
```



### 三、函数可选参数

​	如果你希望 `date` 可以是可选的，你可以通过 `?` 来声明 `date` 为可选参数。这意味着 `date` 可以是 `string` 类型，也可以是 `undefined`。

```ts
function greet(person: string, date?: string) {
  console.log(`Hello ${person}, today is ${date ?? "today"}!`);
}

greet("Brendan"); // 输出: Hello Brendan, today is today!
greet("Brendan", "Monday"); // 输出: Hello Brendan, today is Monday!
```

这里，`date?` 表示 `date` 参数是可选的，可以是 `string` 或 `undefined`。在输出时，使用了 `??` 运算符，确保 `undefined` 时替换为 `"today"`。

### 四、对象类型 

​	js对象可作为函数形参，并且接受

```ts
// The parameter's type annotation is an object type
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });
```

### 五、联合类型

由两种或多种其他类型组成的类型，表示的值可以是其中*任何一种*类型。我们将这些类型中的每一个称为联合的*成员*，如下所示

​	`id: number | string`

<img src="D:\Web学习\前端\assets\image-20241110205947042.png" alt="image-20241110205947042" style="zoom:67%;" />

### 六、类型别名

​	使用`type`来进行声明，如下图所示

​	<img src="D:\Web学习\前端\assets\image-20241110210925303.png" alt="image-20241110210925303" style="zoom:67%;" />

​	如果要拓展类型别名就需要使用**交叉类型（`&`）**

```
type A = { x: number };
type B = A & { y: number };

const obj: B = { x: 1, y: 2 };
```



### 七、interface(相似于类型别名)

​	可以跟类型别名一样，命名一个对象类型：

```ts
 typeof Point {
  x: number;
  y: number;
}
//使用类型别名的方式如下，上面有
 interface Point {
  x: number;
  y: number;
}
 
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
 
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```

类型别名（type）跟接口（interface）极其相似，**关键区别**在于类型不能重新打开以添加新属性，而**接口始终可以扩展**

<img src="D:\Web学习\前端\assets\image-20241110211639560.png" alt="image-20241110211639560" style="zoom:60%;" />

如果别名（type）想要拓展就必须使用交叉类型 (`&`)但没有 `extends` 关键字

#### 1.接口与类的关系

​	类可以通过 `implements` 关键字来实现接口

​	接口里面给出函数名以及返回类型，交给实现这个接口的东西（比如类）来具体实现

```ts
interface Animal {
  name: string;
  makeSound(): void;
}

class Dog implements Animal {
  name: string;
  
  constructor(name: string) {
    this.name = name;
  }

  makeSound() {
    console.log('Woof!');
  }
}

const dog = new Dog('Rex');
dog.makeSound(); // 输出: Woof!
```

#### 2.接口的可拓展性

​	可以通过`extends`来拓展接口（在js中extends只能给类拿来继承）

```
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

const dog: Dog = {
  name: 'Rex',
  breed: 'German Shepherd'
};
```

只有接口可以这么用，type要拓展智能用交叉类型，当然接口也可以用交叉类型，见下面的例子：

```
interface Backpack<Type> {
  add: (obj: Type) => void;
  get: () => Type;
}
 
// 这一行是一个简写，可以告诉 TypeScript 有一个常量，叫做`backpack`，并且不用担心它是从哪
// 里来的。
declare const backpack: Backpack<string>;
 
// 对象是一个字符串，因为我们在上面声明了它作为 Backpack 的变量部分。
const object = backpack.get();
 
// 因为 backpack 变量是一个字符串，不能将数字传递给 add 函数。
backpack.add(23);
```



#### 3.接口的可选属性和只读属性

​	接口中的属性可以是 **可选的**，也可以是 **只读的**。

- **可选属性**：用 `?` 表示，表示该属性可以存在也可以不存在。

- **只读属性**：用 `readonly` 修饰，表示该属性只能在初始化时赋值，之后不能修改。

  ```
  interface Car {
    readonly brand: string;
    model: string;
    year?: number; // 可选属性
  }
  
  const myCar: Car = { brand: 'Toyota', model: 'Camry' };
  myCar.year = 2020; // 可以修改可选属性
  // myCar.brand = 'Honda'; // 错误：只读属性不能修改
  
  ```

#### 4.函数类型接口

```
interface Greet {
  (name: string): string; //形参类型，形参名以及返回值类型
}	//Greet是接口名

const greet: Greet = (name) => {
  return `Hello, ${name}!`;
}; // 其中，第一个等号是赋值，下面的=>是箭头函数，：后面的Greet是显式类型

console.log(greet('Alice')); // 输出: Hello, Alice!

```

`Greet` 接口定义了一个接受 `name`（`string` 类型）并返回 `string` 的函数签名，`greet` 函数实现了这个签名

### 八、类型断言

​	只是叫的好听，其实就是**强制类型转换**，跟`rust`的`as`一样

### 九、[缩小](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)

​	就是当你使用联合来定义一个形参的时候，使用`typeof`可以获取到传入形参的实际参数的类型，这样就可以对应不同的类型进行不同的操作，如下代码所示（ts的`typeof`比起js的优势在于它可以在运行的时候使用，比如你可以使用typeof来指定函数返回类型)

```
function padLeft(padding: number | string, input: string): string {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```

**typeof**返回的是类型的字符串比如

<img src="D:\Web学习\前端\assets\image-20241111163112119.png" alt="image-20241111163112119" style="zoom:67%;" />

​	在**js**中存在一个历史遗留的问题就是null的`typeof`返回的是object，所以当你的联合里面出现了null，那么最好同时给出object，并且优先检查null

​	缩小有真实性缩小、平等缩小、in运算符缩小、实例缩小、instanceof缩小、assign、类型谓词、区分联合类型、

#### 	1.在vue中的使用

​		可以检验父组件是否给子组件传递值（props）没有传递指的话默认值将会是undefined

```
//子组件
<template>
  <div>
    <p>Prop a 的值: {{ a }}</p>
    <button @click="testPrintAll">Test printAll Function</button>
  </div>
</template>

<script setup lang="ts">
import { defineProps } from 'vue';

// 定义 prop
const props = defineProps<{ a?: string }>(); // a 是可选的，没有默认值时会是 undefined

// 定义 printAll 函数
function printAll(strs: string | string[] | null) {
  if (strs) { // 真值检查
    if (typeof strs === "object") {
      for (const s of strs) {
        console.log(s);
      }
    } else if (typeof strs === "string") {
      console.log(strs);
    }
  }
}

// 测试函数调用
function testPrintAll() {
  printAll(props.a); // 使用 props.a 作为参数
}
</script>


//父组件
<template>
  <div>
    <h2>父组件</h2>
    <!-- 不传递 a，默认 a 是 undefined -->
    <ChildComponent />

    <!-- 传递一个字符串 -->
    <ChildComponent a="Hello, World!" />
  </div>
</template>

<script setup lang="ts">
import ChildComponent from './ChildComponent.vue';
</script>
```

​	**第一次**：当 `a` 是 `undefined` 时，`printAll(props.a)` 中的 `strs` 为 `undefined`。由于 `if (strs)` 为 `false`，`printAll` 不会输出内容。

​	**第二次**：当 `a` 是 `"Hello, World!"` 时，`printAll(props.a)` 中的 `strs` 为 `true`，会输出 `"Hello, World!"`。

#### 2.in运算符

​	用于确定对象或其原型链是否具某个属性，比如

```
type Fish = { swim: () => void };
type Bird = { fly: () => void };
type Human = { swim?: () => void; fly?: () => void };
 
function move(animal: Fish | Bird | Human) {
  if ("swim" in animal) {
    animal;
  } else {
    animal;
  }
}
```

​	这个`if`就会通过**in**来筛选出实现了这个方法的type

#### 3.instanceof

​	判断某个东西是否是某个对象的实例

```
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString()); // (parameter) x: Date
  } else {
    console.log(x.toUpperCase()); // (parameter) x: string
  }
}
```

在这个 `logValue` 函数中：

1. `if (x instanceof Date)`

    检查 x是否是 Date 的实例。如果判断为 `true`，则 `x` 被缩小为 `Date` 类型，TypeScript 知道在这个分支中可以调用 `Date` 特有的 `toUTCString()` 方法。[原型链图文讲解](https://blog.csdn.net/m0_46983722/article/details/135309406)

2. `else` 分支中，由于 `x` 不是 `Date`，则被推断为 `string` 类型，这样我们可以安全地调用 `string` 特有的 `toUpperCase()` 方法。

#### 4.assign

​	比如你通过一个三元表达式来给一个变量赋值，那么他会自动推断可能的类型，并且以后你要是想修改这个变量的值，你只能修改成ts推断出来的可能的类型

<img src="D:\Web学习\前端\assets\image-20241111192951713.png" alt="image-20241111192951713" style="zoom:60%;" />

#### 5.类型谓词

​	基本形式`parameterName is Type`，类型谓词就是一个帮助ts语言更好的推断类型用的，如下所示：

```
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

// Both calls to 'swim' and 'fly' are now okay.
let pet = getSmallPet();
 
if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}
```

`pet is Fish` 就是一个类型谓词

#### 6.区分联合类型

​	通过联合类型来区分两个大类型：

```ts
interface Shape {
  kind: "circle" | "square";
  radius?: number;
  sideLength?: number;
}
```

这个接口就是使用了联合来区分"circle"以及"square"，但是有些时候因为我们**在接口中使用了联合**的缘故，ts会产生如下问题

<img src="D:\Web学习\前端\assets\image-20241111204016123.png" alt="image-20241111204016123" style="zoom:67%;" />

​	**解决方法一：**因为radius没有给出，被默认定义成了undefined，但是我们可以使用**非空断言(!)**来告诉ts，这个值肯定存在

```ts
function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius! ** 2;
  }
}
```

​	**解决方法二**：不在接口内使用联合，在外面使用，然后当你使用kind缩小联合成员的范围之后，就可接触问题（如果没有缩小范围，那么问题依旧）

```
interface Circle {
  kind: "circle";
  radius: number;
}
 
interface Square {
  kind: "square";
  sideLength: number;
}
 
type Shape = Circle | Square;
```

​	<img src="D:\Web学习\前端\assets\image-20241111204636004.png" alt="image-20241111204636004" style="zoom:67%;" />

#### 7.详尽式检查(switch)

​	就是使用switch，来匹配所有可能出现的类型，其他你不想管的类型直接一个default就行

```
type Shape = Circle | Square;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```

### 十、泛型

​	只是rust用T，这个ts用Type而已，直接上例子

```
interface Backpack<Type> {
  add: (obj: Type) => void;
  get: () => Type;
}
 
// 这一行是一个简写，可以告诉 TypeScript 有一个常量，叫做`backpack`，并且不用担心它是从哪
// 里来的。
declare const backpack: Backpack<string>;
 
// 对象是一个字符串，因为我们在上面声明了它作为 Backpack 的变量部分。
const object = backpack.get();
 
// 因为 backpack 变量是一个字符串，不能将数字传递给 add 函数。
backpack.add(23);
```



### 十一、显式类型

​	使用  `：和 类型`来实现（就跟rust那样，你可以写可以不写，不写他就自己推断）

```ts
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
 
greet("Maddison", new Date());
```

可以指定函数的输入类型，以及返回值类型，都是用 ：

<img src="D:\Web学习\前端\assets\image-20241110204041604.png" alt="image-20241110204041604" style="zoom:50%;" />

也可以返回一个promise

```
async function getFavoriteNumber(): Promise<number> {
  return 26;
}
```

### 十二、指定tsc编译出来的js版本

​	使用`--target`比如：

```
tsc --target es2015 hello.ts
```

这样tsc会把ts文件编译成es2015版本(es6)，因为tsc会默认把ts文件编译成比较老的版本（例如 ECMAScript 3 或 ECMAScript 5（又名 ES5），绝大多数浏览器都支持es6，所以放心使用

### 十三、严格性

​	ts语言允许用户自己设置对代码检查的严格程度，在ts的配置文件中添加`"strict": true`即可

#### 1.更宽松的检查（默认模式）

当没有启用严格模式（或部分启用）时，TypeScript 会在以下方面变得宽松：

- 不会要求为每个变量显式指定类型，缺少类型的变量会默认推断为 `any`。
- `null` 和 `undefined` 可以分配给任何类型，因此在使用时不必每次都显式检查它们的存在。

这种默认的宽松模式对从 JavaScript 迁移的项目非常友好，可以作为类型系统的入门，帮助你逐步适应 TypeScript 的类型系统。这样，你可以在需要时添加类型，而不必一开始就严格检查所有内容。

#### 2.更严格的检查（启用 `strict` 模式）

对于新的 TypeScript 项目或想要实现更高代码质量的项目，可以启用 `strict` 模式，或者单独开启以下关键选项：

1. **`noImplicitAny`**：防止任何变量默认为 `any` 类型，要求在没有显式类型的地方触发错误。这能强制你明确所有变量的类型，提高代码的可读性和安全性。
2. **`strictNullChecks`**：要求在代码中明确处理 `null` 和 `undefined`，防止因未检查的 `null` 或 `undefined` 导致潜在的错误。

### 十四、结构化的类型系统

​	在结构化的类型系统当中，如果两个对象具有相同的结构，则认为它们是相同类型的。

```
interface Point {
  x: number;
  y: number;
}
 
function logPoint(p: Point) {
  console.log(`${p.x}, ${p.y}`);
}
 
// 打印 "12, 26"
const point = { x: 12, y: 26 };
logPoint(point);
```

`point` 变量从未声明为 `Point` 类型。 但是，在类型检查中，TypeScript 将 `point` 的结构与 `Point`的结构进行比较。它们的结构相同，所以代码通过了，就因为长得是一样的，所以就自己被推断成了就是这个类型

### 十五、模块

不一定你非得导出什么，你直接一个`export {}`也是可以的，因为这会把写了这行的ts文件视为是一个模块，这样他就会有如下几个优点：

1. 所有的变量、函数、类和类型声明都是局部的，**不会污染全局作用域**。

 	2. 更加**模块化**
 	3. 提供更好的类型安全
 	4. 避免全局命名冲突，不会影响全局作用域

#### 	1.导出导入

##### 		1.默认导出：`export default`

```
// @filename: hello.ts
export default function helloWorld() {
  console.log("Hello, world!");
}
```

​		这样导出之后，import的那个文件可以**自定义**名称，然后使用这个导出的东西，自定义如下

```
import helloWorld from "./hello.js";
helloWorld();
```

##### 	2.直接导出	

​	当然也可以**直接导出**，导出什么引入的时候就需要是什么

```
// @filename: maths.ts
export var pi = 3.14;
export let squareTwo = 1.41;
export const phi = 1.61;
 
export class RandomNumberGenerator {}
 
export function absolute(num: number) {
  if (num < 0) return num * -1;
  return num;
}
```

##### 	3.附加导入

​		就是使用`as`重命名一下而已

```
import { pi as π } from "./maths.js";
 
console.log(π);
```

##### 	4.导入所有别的文件导出的

​		可以获取所有导出的对象并将它们放入单个命名空间中`* as name`

```
// @filename: app.ts
import * as math from "./maths.js";
 
console.log(math.pi);
const positivePhi = math.absolute(math.phi);
```

​		实际上你获取的是一个包含所有之前文件导出的内容的一个js对象

##### 	5.仅仅导入文件	

​		也可以导入文件但*不*将任何变量包含到当前模块中：`import "./file"`

```
// @filename: app.ts
import "./maths.js";
 
console.log("3.14");
```

##### 	6.TS特定的ES模块语法

```
// @filename: animal.ts
export type Cat = { breed: string; yearOfBirth: number };
 
export interface Dog {
  breeds: string[];
  yearOfBirth: number;
}
 
// @filename: app.ts
import { Cat, Dog } from "./animal.js";
type Animals = Cat | Dog;
```

可以直接在定义一个别名、接口等东西的时候直接导出

###### 		1.只能导入类型的语法

​			`import type`： import type { Cat, Dog } from "./animal.js";

​			当你尝试导入一个函数等不是类型的东西的时候就会报错：

​	<img src="D:\Web学习\前端\assets\image-20241111230556699.png" alt="image-20241111230556699" style="zoom:50%;" />



​			ts4.5支持加入前缀：

```
// @filename: app.ts
import { createCatName, type Cat, type Dog } from "./animal.js";
 
export type Animals = Cat | Dog;
const name = createCatName();
```

​		使用type表明后面的东西是一种类型

##### 		7. CommandJS语法

​			[直接看文档得了](https://www.typescriptlang.org/docs/handbook/2/modules.html)

##### 8. declare module

​	这个东西用来在另一个模块里面给一个模块添加东西，有如下代码

```
// 扩展 vue-router 模块的类型声明
declare module 'vue-router' {
  interface RouteMeta {
    // 添加新的元数据字段
    isAdmin?: boolean;  // 可选的字段，表示是否需要管理员权限
    requiresAuth: boolean;  // 必需的字段，表示是否需要身份验证
  }
}
```

​	就是给vue-router模块添加一个接口

# 所有导出方法：

在 TypeScript 中，`export` 的方式有以下几种常见用法，可以分为 **直接导出**、**命名导出** 和 **默认导出** 三大类。

------

## 1. **直接导出（Inline Export）**

直接在定义时使用 `export`，适用于变量、函数、类、类型、接口等。

示例：

```
// 导出变量
export const name = "TypeScript";

// 导出函数
export function greet() {
  return "Hello!";
}

// 导出类
export class Animal {
  constructor(public name: string) {}
}

// 导出类型和接口
export type Cat = { breed: string; yearOfBirth: number };
export interface Dog {
  breeds: string[];
  yearOfBirth: number;
}
```

那个export type也算是直接导出，但是他会被解释成专门导出类型的一种导出

特点：

- 导出的内容可以在其他模块中通过命名导入。

------

## 2. **批量导出（Export List）**

先定义内容，再通过 `export` 语句一次性导出。

示例：

```
const name = "TypeScript";
function greet() {
  return "Hello!";
}
class Animal {
  constructor(public name: string) {}
}
type Cat = { breed: string; yearOfBirth: number };
interface Dog {
  breeds: string[];
  yearOfBirth: number;
}

// 批量导出
export { name, greet, Animal, Cat, Dog };
```

特点：

- 更适合多个内容的模块化导出。
- 方便控制导出内容。

## 3. **默认导出（Default Export）**

每个模块只能有一个默认导出，适合模块的主功能。

示例：

```
// 导出变量
export default "Default Export Example";

// 导出函数
export default function greet() {
  return "Hello, Default!";
}

// 导出类
export default class Animal {
  constructor(public name: string) {}
}
```

特点：

- 默认导出时，导入方可以随意命名：

  ```
  typescript
  
  
  复制代码
  import AnyNameYouWant from "./module";
  ```

------

## 4. **重命名导出（Export with Alias）**

在导出时重命名，适合避免命名冲突。

示例：

```
const myName = "TypeScript";
function greet() {
  return "Hello!";
}

export { myName as nameAlias, greet as greetAlias };
```

特点：

- 导入时使用别名：

  ```
  import { nameAlias, greetAlias } from "./module";
  ```

------

## 5. **导出所有内容（Re-export 或 Export \*）**

将其他模块的内容重新导出。

示例 1：直接重新导出

```
// 从另一个模块导入后直接导出
export * from "./otherModule";
```

示例 2：带选择性的重新导出

```
export { name, greet } from "./otherModule";
```

示例 3：重新导出并重命名

```
export { name as newName, greet as newGreet } from "./otherModule";
```

------

## 6. **混合导出**

可以在一个模块中同时使用命名导出和默认导出。

示例：

```
export const name = "TypeScript";
export function greet() {
  return "Hello!";
}

export default class Animal {
  constructor(public name: string) {}
}
```

特点：

- 导入时可以同时使用：

  ```
  import Animal, { name, greet } from "./module";
  ```