

如果哪天要把sass这种预处理器跟Tailwind Css一起用的时候再看这个[文档](https://tailwindcss.com/docs/using-with-preprocessors)



# 一、在webstorm中使用

​	<img src="D:\Web学习\前端\assets\image-20241121144627215.png" alt="image-20241121144627215" style="zoom:60%;" />

去对应文件添加上那四个文件的Tailwind CSS支持，并且通过设置emment ——true来实现代码自动补全



# 预处理使用postCSS
### 1.插入

​	使用 PostCSS 处理此问题的标准插件是[postcss-import](https://github.com/postcss/postcss-import)。下面是导入方法

```
yarn add -D postcss-import
```

​	预处理器提供的最有用的功能之一是将 CSS 组织多个文件，并通过在构建时处理 `@import` 语句提前将它们合并，而不是在浏览器中合并，`postcss-import` 插件会将这些导入的文件合并到一个最终的 CSS 文件中，而无需你手动合并它们

```
// postcss.config.js

module.exports = {
  plugins: {
    'postcss-import': {},
    tailwindcss: {},
    autoprefixer: {},
  }
}
```

### 2.嵌套

 理解CSS嵌套：

```
/* example.css */
.container {
  background-color: lightgray;
  padding: 20px;

  .header {
    color: #333;
    font-size: 24px;
  }

  .content {
    margin-top: 10px;
    color: #666;

    .text {
      font-size: 16px;
    }
  }
}

//这会变成
/* 编译后的 CSS */
.container {
  background-color: lightgray;
  padding: 20px;
}

.container .header {
  color: #333;
  font-size: 24px;
}

.container .content {
  margin-top: 10px;
  color: #666;
}

.container .content .text {
  font-size: 16px;
}
```

启用postCSS的嵌套功能也要装一个插件，跟前面的那个一起用

```
yarn add -D postcss-nesting
```

```
// postcss.config.js

module.exports = {
  plugins: {
    'postcss-import': {},
    'tailwindcss/nesting': 'postcss-nesting',
    tailwindcss: {},
    autoprefixer: {},
  }

```

**请注意**，如果您在项目中使用[`postcss-preset-env`](https://github.com/csstools/postcss-plugins/tree/main/plugin-packs/postcss-preset-env)，则应确保禁用嵌套并让它`tailwindcss/nesting`为您处理：

```js
// postcss.config.js

module.exports = {
  plugins: {
    'postcss-import': {},
    'tailwindcss/nesting': 'postcss-nesting',
    tailwindcss: {},
    'postcss-preset-env': {
      features: { 'nesting-rules': false },
    },
  }
}
```

# 二、处理悬停、焦点和其他状态

​	Tailwind 中的每个实用程序类都可以通过在类名开头添加修饰符来*有条件地*应用，该修饰符描述你想要针对的条件

Tailwind 几乎涵盖了您需要的一切修饰符，其中包括：

- [伪类](https://tailwindcss.com/docs/hover-focus-and-other-states#pseudo-classes)，如`:hover`、`:focus`、`:first-child`和`:required`
- [伪元素](https://tailwindcss.com/docs/hover-focus-and-other-states#pseudo-elements)，如`::before`、`::after`、`::placeholder`和`::selection`
- [媒体和功能查询](https://tailwindcss.com/docs/hover-focus-and-other-states#media-and-feature-queries)，例如响应断点、暗模式和`prefers-reduced-motion`
- [属性选择器](https://tailwindcss.com/docs/hover-focus-and-other-states#attribute-selectors)，例如`[dir="rtl"]`和`[open]`

可以使用堆叠的方式，让一个类应用到多个伪类中，例如在暗模式下、在中等断点处、在悬停时更改背景颜色：

```html
<button class="dark:md:hover:bg-fuchsia-600 ...">
  Save changes
</button>
```

## **Ⅰ**.伪类

### [Quick reference](https://tailwindcss.com/docs/hover-focus-and-other-states#quick-reference)伪类列表

| Modifier(写在class里面的，加个" : ")                         | CSS                                              |
| ------------------------------------------------------------ | ------------------------------------------------ |
| [hover](https://tailwindcss.com/docs/hover-focus-and-other-states#hover) | `&:hover`                                        |
| [focus](https://tailwindcss.com/docs/hover-focus-and-other-states#focus) | `&:focus`                                        |
| [focus-within](https://tailwindcss.com/docs/hover-focus-and-other-states#focus-within) | `&:focus-within`                                 |
| [focus-visible](https://tailwindcss.com/docs/hover-focus-and-other-states#focus-visible) | `&:focus-visible`                                |
| [active](https://tailwindcss.com/docs/hover-focus-and-other-states#active) | `&:active`                                       |
| [visited](https://tailwindcss.com/docs/hover-focus-and-other-states#visited) | `&:visited`                                      |
| [target](https://tailwindcss.com/docs/hover-focus-and-other-states#target) | `&:target`                                       |
| [*](https://tailwindcss.com/docs/hover-focus-and-other-states#styling-direct-children) | `& > *`                                          |
| [has](https://tailwindcss.com/docs/hover-focus-and-other-states#styling-based-on-descendants) | `&:has`                                          |
| [first](https://tailwindcss.com/docs/hover-focus-and-other-states#first) | `&:first-child`                                  |
| [last](https://tailwindcss.com/docs/hover-focus-and-other-states#last) | `&:last-child`                                   |
| [only](https://tailwindcss.com/docs/hover-focus-and-other-states#only) | `&:only-child`                                   |
| [odd](https://tailwindcss.com/docs/hover-focus-and-other-states#odd) | `&:nth-child(odd)`                               |
| [even](https://tailwindcss.com/docs/hover-focus-and-other-states#even) | `&:nth-child(even)`                              |
| [first-of-type](https://tailwindcss.com/docs/hover-focus-and-other-states#first-of-type) | `&:first-of-type`                                |
| [last-of-type](https://tailwindcss.com/docs/hover-focus-and-other-states#last-of-type) | `&:last-of-type`                                 |
| [only-of-type](https://tailwindcss.com/docs/hover-focus-and-other-states#only-of-type) | `&:only-of-type`                                 |
| [empty](https://tailwindcss.com/docs/hover-focus-and-other-states#empty) | `&:empty`                                        |
| [disabled](https://tailwindcss.com/docs/hover-focus-and-other-states#disabled) | `&:disabled`                                     |
| [enabled](https://tailwindcss.com/docs/hover-focus-and-other-states#enabled) | `&:enabled`                                      |
| [checked](https://tailwindcss.com/docs/hover-focus-and-other-states#checked) | `&:checked`                                      |
| [indeterminate](https://tailwindcss.com/docs/hover-focus-and-other-states#indeterminate) | `&:indeterminate`                                |
| [default](https://tailwindcss.com/docs/hover-focus-and-other-states#default) | `&:default`                                      |
| [required](https://tailwindcss.com/docs/hover-focus-and-other-states#required) | `&:required`                                     |
| [valid](https://tailwindcss.com/docs/hover-focus-and-other-states#valid) | `&:valid`                                        |
| [invalid](https://tailwindcss.com/docs/hover-focus-and-other-states#invalid) | `&:invalid`                                      |
| [in-range](https://tailwindcss.com/docs/hover-focus-and-other-states#in-range) | `&:in-range`                                     |
| [out-of-range](https://tailwindcss.com/docs/hover-focus-and-other-states#out-of-range) | `&:out-of-range`                                 |
| [placeholder-shown](https://tailwindcss.com/docs/hover-focus-and-other-states#placeholder-shown) | `&:placeholder-shown`                            |
| [autofill](https://tailwindcss.com/docs/hover-focus-and-other-states#autofill) | `&:autofill`                                     |
| [read-only](https://tailwindcss.com/docs/hover-focus-and-other-states#read-only) | `&:read-only`                                    |
| [before](https://tailwindcss.com/docs/hover-focus-and-other-states#before-and-after) | `&::before`                                      |
| [after](https://tailwindcss.com/docs/hover-focus-and-other-states#before-and-after) | `&::after`                                       |
| [first-letter](https://tailwindcss.com/docs/hover-focus-and-other-states#first-line-and-first-letter) | `&::first-letter`                                |
| [first-line](https://tailwindcss.com/docs/hover-focus-and-other-states#first-line-and-first-letter) | `&::first-line`                                  |
| [marker](https://tailwindcss.com/docs/hover-focus-and-other-states#highlighted-text) | `&::marker`                                      |
| [selection](https://tailwindcss.com/docs/hover-focus-and-other-states#selection) | `&::selection`                                   |
| [file](https://tailwindcss.com/docs/hover-focus-and-other-states#file-input-buttons) | `&::file-selector-button`                        |
| [backdrop](https://tailwindcss.com/docs/hover-focus-and-other-states#dialog-backdrops) | `&::backdrop`                                    |
| [placeholder](https://tailwindcss.com/docs/hover-focus-and-other-states#placeholder) | `&::placeholder`                                 |
| [sm](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media (min-width: 640px)`                      |
| [md](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media (min-width: 768px)`                      |
| [lg](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media (min-width: 1024px)`                     |
| [xl](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media (min-width: 1280px)`                     |
| [2xl](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media (min-width: 1536px)`                     |
| [min-[…\]](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media (min-width: …)`                          |
| [max-sm](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media not all and (min-width: 640px)`          |
| [max-md](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media not all and (min-width: 768px)`          |
| [max-lg](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media not all and (min-width: 1024px)`         |
| [max-xl](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media not all and (min-width: 1280px)`         |
| [max-2xl](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media not all and (min-width: 1536px)`         |
| [max-[…\]](https://tailwindcss.com/docs/hover-focus-and-other-states#responsive-breakpoints) | `@media (max-width: …)`                          |
| [dark](https://tailwindcss.com/docs/hover-focus-and-other-states#prefers-color-scheme) | `@media (prefers-color-scheme: dark)`            |
| [portrait](https://tailwindcss.com/docs/hover-focus-and-other-states#viewport-orientation) | `@media (orientation: portrait)`                 |
| [landscape](https://tailwindcss.com/docs/hover-focus-and-other-states#viewport-orientation) | `@media (orientation: landscape)`                |
| [motion-safe](https://tailwindcss.com/docs/hover-focus-and-other-states#prefers-reduced-motion) | `@media (prefers-reduced-motion: no-preference)` |
| [motion-reduce](https://tailwindcss.com/docs/hover-focus-and-other-states#prefers-reduced-motion) | `@media (prefers-reduced-motion: reduce)`        |
| [contrast-more](https://tailwindcss.com/docs/hover-focus-and-other-states#prefers-contrast) | `@media (prefers-contrast: more)`                |
| [contrast-less](https://tailwindcss.com/docs/hover-focus-and-other-states#prefers-contrast) | `@media (prefers-contrast: less)`                |
| [print](https://tailwindcss.com/docs/hover-focus-and-other-states#print-styles) | `@media print`                                   |
| [supports-[…\]](https://tailwindcss.com/docs/hover-focus-and-other-states#supports-rules) | `@supports (…)`                                  |
| [aria-checked](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-checked=“true”]`                         |
| [aria-disabled](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-disabled=“true”]`                        |
| [aria-expanded](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-expanded=“true”]`                        |
| [aria-hidden](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-hidden=“true”]`                          |
| [aria-pressed](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-pressed=“true”]`                         |
| [aria-readonly](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-readonly=“true”]`                        |
| [aria-required](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-required=“true”]`                        |
| [aria-selected](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-selected=“true”]`                        |
| [aria-[…\]](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states) | `&[aria-…]`                                      |
| [data-[…\]](https://tailwindcss.com/docs/hover-focus-and-other-states#data-attributes) | `&[data-…]`                                      |
| [rtl](https://tailwindcss.com/docs/hover-focus-and-other-states#rtl-support) | `[dir=“rtl”] &`                                  |
| [ltr](https://tailwindcss.com/docs/hover-focus-and-other-states#rtl-support) | `[dir=“ltr”] &`                                  |
| [open](https://tailwindcss.com/docs/hover-focus-and-other-states#open-closed-state) | `&[open]`                                        |

### 1.嵌套组(group)

<img src="D:\Web学习\前端\assets\image-20241123201733920.png" alt="image-20241123201733920" style="zoom:67%;" />

​	多见根据父元素是否有指针悬浮来改变颜色这种

#### 1.1[根据父状态进行样式设置(group- {modifier} )](https://tailwindcss.com/docs/hover-focus-and-other-states#styling-based-on-parent-state)

​	有些时候要用到父元素的状态来设置元素的样式，给父元素一个class="group"，并使用`group-*`修饰符，比如：`group-hover`来设置目标元素的样式：

​	此模式适用于每个伪类修饰符，例如`group-focus`，`group-active`甚至`group-odd`。

```
<a href="#" class="group block max-w-xs mx-auto rounded-lg p-6 bg-white ring-1 ring-slate-900/5 shadow-lg space-y-3 hover:bg-sky-500 hover:ring-sky-500">
  <div class="flex items-center space-x-3">
    <svg class="h-6 w-6 stroke-sky-500 group-hover:stroke-white" fill="none" viewBox="0 0 24 24"><!-- ... --></svg>
    <h3 class="text-slate-900 group-hover:text-white text-sm font-semibold">New project</h3>
  </div>
  <p class="text-slate-500 group-hover:text-white text-sm">Create a new project from a variety of starting templates.</p>
</a>
```

​	然后**子元素**就可以**根据父元素**现在的状态进行样式变化

#### 1.2[区分嵌套组](https://tailwindcss.com/docs/hover-focus-and-other-states#differentiating-nested-groups)(group/{name})

​	嵌套组时，您可以根据**特定**父组的状态设置样式，方法是使用类为该父组赋予唯一的组名`group/{name}`，不同的组名表示不同的组，如果就单独一个组名，就说明这个标签新开了一个组，如果是`group-hover/{name}`就说明进入了某个组：

```
<ul role="list">
  {#each people as person}
    <li class="group/item hover:bg-slate-100 ..."> //这个<li>也是新开了一个组
      <img src="{person.imageUrl}" alt="" />
      <div>
        <a href="{person.url}">{person.name}</a>
        <p>{person.title}</p>
      </div>
      <a class="group/edit invisible hover:bg-slate-200 group-hover/item:visible ..." href="tel:{person.phone}"> //这个<a>就是新开了一个组
        <span class="group-hover/edit:text-gray-700 ...">Call</span>
        <svg class="group-hover/edit:translate-x-0.5 group-hover/edit:text-slate-500 ...">
          <!-- ... -->
        </svg>
      </a>
    </li>
  {/each}
</ul>
```

#### 1.3[任意组](https://tailwindcss.com/docs/hover-focus-and-other-states#arbitrary-groups)(group-*)

​	您可以通过在方括号之间提供自己的选择器作为[任意值](https://tailwindcss.com/docs/adding-custom-styles#using-arbitrary-values)`group-*`来动态创建一次性修饰符，可以选择**父元素的任何一个类作为组名**，因为vue可以通过`ref`等方法改变一个类是否生效，那么也就是说可以结合这个东西实现一个子元素**多套样式**，可以方便的切换

```
<div class="group is-published">
  <div class="hidden group-[.is-published]:block">
    Published
  </div>
</div>
```

用" & "，来表示引用父元素的组，方便与父组件的状态进行交互，比如如下代码：

```
<div class="group">
  <div class="group-[:nth-of-type(3)_&]:block">
    <!-- ... -->
  </div>
</div>
```

​	也就是说<div class="group-[:nth-of-type(3)_&]:block"> 可以有一堆子元素，只有出现`.group`类的时候才算一个，知道出现第三个.group的时候才生效

### 2.同级(peer)



多见于提示框，当表单输入在某一条件下给出的提示

<img src="D:\Web学习\前端\assets\image-20241123201918389.png" alt="image-20241123201918389" style="zoom:67%;" />

<img src="D:\Web学习\前端\assets\image-20241123201845787.png" alt="image-20241123201845787" style="zoom:67%;" />

#### 2.1[根据同级状态进行样式设置(peer- {modifier} )](https://tailwindcss.com/docs/hover-focus-and-other-states#styling-based-on-sibling-state)

​	使用类标记同级元素`peer`，并使用`peer-*`修饰符`peer-invalid`来设置目标元素的样式：

```
<form>
  <label class="block">
    <span class="block text-sm font-medium text-slate-700">Email</span>
    <input type="email" class="peer ..."/>
    <p class="mt-2 invisible peer-invalid:visible text-pink-600 text-sm">
      Please provide a valid email address.
    </p>
  </label>
</form>
```

​	我认为在表单中很好用，因为当用户输入不合法的时候，提示消息才应当被展示，然而这个用`v-model`+正则表达式+ `v-if`来写的话会有点复杂。

​	注意，只有先声明peer，后面才可以用`peer-invalid`这种，但凡顺序反了都是错的

#### 2.2[区分同级](https://tailwindcss.com/docs/hover-focus-and-other-states#differentiating-peers)(peer/{name})

​	使用类为该对等体赋予一个唯一的名称`peer/{name}`，并使用如下类在修饰符中包含该名称`peer-checked/{name}`

```
<fieldset>
  <legend>Published status</legend>

  <input id="draft" class="peer/draft" type="radio" name="status" checked />
  <label for="draft" class="peer-checked/draft:text-sky-500">Draft</label>

  <input id="published" class="peer/published" type="radio" name="status" />
  <label for="published" class="peer-checked/published:text-sky-500">Published</label>

  <div class="hidden peer-checked/draft:block">Drafts are only visible to administrators.</div>
  <div class="hidden peer-checked/published:block">Your post will be publicly visible on your site.</div>
</fieldset>
```

#### 2.3[任意同级](https://tailwindcss.com/docs/hover-focus-and-other-states#arbitrary-peers)(peer-*)

```
<form>
  <label for="email">Email:</label>
  <input id="email" name="email" type="email" class="is-dirty peer" required />
  <div class="peer-[.is-dirty]:peer-required:block hidden">This field is required.</div>
  <!-- ... -->
</form>
```

这里就涉及到**同时使用两个条件**的语法了：

​	`peer-[.is-dirty]:peer-required:block`这个是检测同级元素有没有`.is-dirty`类以及`required`属性的

跟前面嵌套组一样，也可以使用`&`来标记`.peer`

```
<div>
  <input type="text" class="peer" />
  <div class="hidden peer-[:nth-of-type(3)_&]:block">
    <!-- ... -->
  </div>
</div>
```

### 3.直接给子元素设置(*)

需要批量应用样式的：
<img src="D:\Web学习\前端\assets\image-20241123201954777.png" alt="image-20241123201954777" style="zoom:67%;" />

#### 设置方法[(   *-{modifier} )](https://tailwindcss.com/docs/hover-focus-and-other-states#styling-direct-children)

```
<div>
  <h2>Categories<h2>
  <ul class="*:rounded-full *:border *:border-sky-100 *:bg-sky-50 *:px-2 *:py-0.5 dark:text-sky-300 dark:*:border-sky-500/15 dark:*:bg-sky-500/10 ...">
    <li>Sales</li>
    <li>Marketing</li>
    <li>SEO</li>
    <!-- ... -->
  </ul>
</div>
```

### 4.根据后代样式设置(has-*) 

跟group不一样的是这个多用于表单，选中之后一直维持这个颜色（group可能多用于瞬时的那种）

<img src="D:\Web学习\前端\assets\image-20241123202023703.png" alt="image-20241123202023703" style="zoom:67%;" />



​	可以使用`has-*`伪类（例如`has-[:focus]`）根据元素的后代状态来设置元素的样式。您还可以使用**元素选择器**（例如`has-[img]`或`has-[a]`）根据元素的后代元素的有无来设置元素的样式，比如`has-[a]`就是后代中有<a>才会把样式应用到当前这个元素身上。

​	这个例子是根据后代的状态的，比如`:focus`，`:checked`

```
<label class="has-[:checked]:bg-indigo-50 has-[:checked]:text-indigo-900 has-[:checked]:ring-indigo-200 ..">
  <svg fill="currentColor">
    <!-- ... -->
  </svg>
  Google Pay
  <input type="radio" class="checked:border-indigo-500 ..." />
</label>
```

#### 4.1结合group(group-has-*)

​	就是检测组内后代的状态或者是否存在（这个例子是是否存在），如果有就应用这个样式（就是使用**元素选择器**）

```
<div class="group ...">
  <img src="..." />
  <h4>Spencer Sharp</h4>
  <svg class="hidden group-has-[a]:block ...">
    <!-- ... -->
  </svg>
  <p>Product Designer at <a href="...">planeteria.tech</a></p>
</div>
```

#### 4.2结合peer(peer-has-*)

​	这里是检测**同级的后代的元素**是否checked，也就是是否被选中

```
<fieldset>
  <legend>Today</legend>

  <div>
    <label class="peer ...">
      <input type="checkbox" name="todo[1]" checked />
      Create a to do list
    </label>
    <svg class="peer-has-[:checked]:hidden ...">
      <!-- ... -->
    </svg>
  </div>

  <!-- ... -->
</fieldset>
```

## Ⅱ、伪元素

### 1.::before & ::after

​	使用before:  after:

​	他们不会出现在DOM上所以用户点不到，一般不会用因为可以直接添加<span>除非有特殊需求

```html
<label class="block">
  <span class="after:content-['*'] after:ml-0.5 after:text-red-500 block text-sm font-medium text-slate-700">
    Email
  </span>
  <input type="email" name="email" class="mt-1 px-3 py-2 bg-white border shadow-sm border-slate-300 placeholder-slate-400 focus:outline-none focus:border-sky-500 focus:ring-sky-500 block w-full rounded-md sm:text-sm focus:ring-1" placeholder="you@example.com" />
</label>
```

​	当使用这些修饰符时，Tailwind 会自动默认添加`content: ''`，因此您不必指定它，除非您想要不同的值：

### 2.placeholder

​	使用`placeholder：`

<img src="D:\Web学习\前端\assets\image-20241123204558566.png" alt="image-20241123204558566" style="zoom:67%;" />

```
<label class="relative block">
  <span class="sr-only">Search</span>
  <span class="absolute inset-y-0 left-0 flex items-center pl-2">
    <svg class="h-5 w-5 fill-slate-300" viewBox="0 0 20 20"><!-- ... --></svg>
  </span>
  <input class="placeholder:italic placeholder:text-slate-400 block bg-white w-full border border-slate-300 rounded-md py-2 pl-9 pr-3 shadow-sm focus:outline-none focus:border-sky-500 focus:ring-sky-500 focus:ring-1 sm:text-sm" placeholder="Search for anything..." type="text" name="search"/>
</label>
```

### 3.file(必须自己加边框)

​	使用`file:`专门为那个按钮提供样式

```
<form class="flex items-center space-x-6">
  <div class="shrink-0">
    <img class="h-16 w-16 object-cover rounded-full" src="https://images.unsplash.com/photo-1580489944761-15a19d654956?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1361&q=80" alt="Current profile photo" />
  </div>
  <label class="block">
    <span class="sr-only">Choose profile photo</span>
    <input type="file" class="block w-full text-sm text-slate-500
      file:mr-4 file:py-2 file:px-4
      file:rounded-full file:border-0
      file:text-sm file:font-semibold
      file:bg-violet-50 file:text-violet-700
      hover:file:bg-violet-100
    "/>
  </label>
</form>
```

​	请注意，Tailwind 的[边框重置](https://tailwindcss.com/docs/preflight#border-styles-are-reset-globally)不适用于文件输入按钮。也就是说tailwind css不会自己给你个边框，你要自己用`file:border-solid`设置[边框样式](https://tailwindcss.com/docs/border-style)

```
<input type="file" class="file:border file:border-solid ..." /> //file: border是开启边框的意思
```

​	上面那个代码没有`file: border`是因为直接把边框设置成了`file: border-0`所以开不开启边框一点关系都没有

### 4.marker(写在ul里面)

​	使用`marker:`设置列表中的计数器或项目符号的样式，就比如无序列表前面的圆圈样式

<img src="D:\Web学习\前端\assets\image-20241123220010152.png" alt="image-20241123220010152" style="zoom:67%;" />

```
<ul role="list" class="marker:text-sky-400 list-disc pl-5 space-y-3 text-slate-500">
  <li>5 cups chopped Porcini mushrooms</li>
  <li>1/2 cup of olive oil</li>
  <li>3lb of celery</li>
</ul>
```

​	marker在Tailwind CSS中是可继承的，所以不用在<li>中多次重复的写同一个

### 5.selection

​	使用`selection:`表示被选中的文本，就是你长按鼠标在文字上拖动的那个框框，下面是一个例子：

<img src="D:\Web学习\前端\assets\image-20241123220618553.png" alt="image-20241123220618553" style="zoom:67%;" />

```
<div class="selection:bg-fuchsia-300 selection:text-fuchsia-900">
  <p>
    So I started to walk into the water. I won't lie to you boys, I was
    terrified. But I pressed on, and as I made my way past the breakers
    a strange calm came over me. I don't know if it was divine intervention
    or the kinship of all living things but I tell you Jerry at that moment,
    I <em>was</em> a marine biologist.
  </p>
</div>
```

​	`selection`修饰符也是可继承的，所以按下面那种写法就可以让整个网页都是这个颜色

```
<html>
<head>
  <!-- ... -->
</head>
<body class="selection:bg-pink-300">
  <!-- ... -->
</body>
</html>
```

### 6.first-line & first-letter

​	设置内容块的第一行以及内容块的第一个单词

<img src="D:\Web学习\前端\assets\image-20241123221419095.png" alt="image-20241123221419095" style="zoom:67%;" />

```
<p class="first-line:uppercase first-line:tracking-widest
  first-letter:text-7xl first-letter:font-bold first-letter:text-slate-900
  first-letter:mr-3 first-letter:float-left
">
  Well, let me tell you something, funny boy. Y'know that little stamp, the one
  that says "New York Public Library"? Well that may not mean anything to you,
  but that means a lot to me. One whole hell of a lot.
</p>
```

### 7.dialog元素的背景

直接用`backdrop`就行

```
<dialog class="backdrop:bg-gray-50">
  <form method="dialog">
    <!-- ... -->
  </form>
</dialog>
```

## **Ⅲ**、[媒体和功能查询](https://tailwindcss.com/docs/hover-focus-and-other-states#media-and-feature-queries)

这个媒体查询，比如强对比度，减少动画模式，深色模式，都是用户在系统中等地方设置的

### 1.响应式设计

​	`md`  `lg`

### 2.配色方案(暗黑模式)

​	媒体查询**`prefers-color-scheme`**可以返回用户是使用欢浅色还是深色，在tailwindCSS中可以使用`dark`来覆盖暗模式，不用去管这个`prefers-color-scheme`返回的是什么

​	使用`dark`修饰符

### 3.偏好少动画

​	媒体**`prefers-reduced-motion`**查询会告诉您用户是否要求您尽量减少非必要的动作

​	使用`motion-safe`修饰符

### 4.偏好对比度

​	媒体查询`prefers-contrast`会告诉您用户是否要求增加或减少对比度

​	使用`contrast-more`修饰符

### 5.强制颜色模式

​	媒体`forced-colors`查询指示用户是否使用强制颜色模式。

​	使用`forced-colors`修饰符，就是让用户自己选他要的颜色方案，然后强制覆盖我们设置好的颜色

### 6.视口方向

​	使用`portrait`和`landscape`修饰符，分别表示用户视口是垂直和水平的时候

```
<div>
  <div class="portrait:hidden">
    <!-- ... -->
  </div>
  <div class="landscape:hidden">
    <p>
      This experience is designed to be viewed in landscape. Please rotate your
      device to view the site.
    </p>
  </div>
</div>
```

### 7.打印时的样式

​	使用`print`修饰符

### 8.是否支持(supports)

​	根据用户浏览器是否支持某个样式来决定是否应用某些样式

​	使用`supports-[...]`	修饰符

```
<div class="flex supports-[display:grid]:grid ...">
  <!-- ... -->
</div>
```

当然，你可以在预设一下，然后批量使用：

```
tailwind.config.js中

/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    supports: {
      grid: 'display: grid',
    },
  },
}
```

`supports-*`然后就可以在项目中使用这些自定义修饰符：

```html
<div class="supports-grid:grid">
  <!-- ... -->
</div>
```

## **IV**、属性选择器

根据有没有在HTML标签里面设置某个属性以及属性值，来在class中决定是否应用类

### 1.[ARIA 状态](https://tailwindcss.com/docs/hover-focus-and-other-states#aria-states)可(搭配group peer)

​	有点多，要用了再看就是

```
<div aria-checked="true" class="bg-gray-600 aria-checked:bg-sky-700">
  <!-- ... -->
</div
```



​	还支持自定义aria的值，依旧是在那个配置文件里面设置一下


```
tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    extend: {
      aria: {
        asc: 'sort="ascending"',
        desc: 'sort="descending"',
      },
    },
  },
};
```

​	还可以根据值来使用

```
<table>
  <thead>
    <tr>
      <th
        aria-sort="ascending"
        class="aria-[sort=ascending]:bg-[url('/img/down-arrow.svg')] aria-[sort=descending]:bg-[url('/img/up-arrow.svg')]"
      >
        Invoice #
      </th>
      <!-- ... -->
    </tr>
  </thead>
  <!-- ... -->
</table>
```

​	ARIA 状态修饰符还可以使用`group-aria-*`and`peer-aria-*`修饰符来定位父元素和同级元素

### 2.[数据属性](https://tailwindcss.com/docs/hover-focus-and-other-states#data-attributes)(data-*)

使用**`data-*`**修饰符（HTML的自定义属性就是data-开头，所以用这个修饰符）

```
<!-- Will apply -->
<div data-size="large" class="data-[size=large]:p-8">
  <!-- ... -->
</div>

<!-- Will not apply -->
<div data-size="medium" class="data-[size=large]:p-8"> //所以这里就很好玩，我们是否可以使用vue的ref来实现该样式的开关 
  <!-- ... -->                                         //呢？
</div>
```

​	这个东西也可以在tailwind.config.js里面自定义

```
tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    data: {
      checked: 'ui~="checked"',
    },
  },
}
```

`~=` 是 CSS 的属性选择器操作符之一，表示 **属性值中包含指定单词的元素**。然后我们就可以使用这个自定义的修饰符

```
<div data-ui="checked active" class="data-checked:underline">
  <!-- ... -->
</div>
```

### 3.RTL支持

RTL支持就是文本方向的`right to left` 以及 `left to right`

用`rtl` & `ltr`修饰符就行

```
<div class="group flex items-center">
  <img class="shrink-0 h-12 w-12 rounded-full" src="..." alt="" />
  <div class="ltr:ml-3 rtl:mr-3">
    <p class="text-sm font-medium text-slate-700 group-hover:text-slate-900">...</p>
    <p class="text-sm font-medium text-slate-500 group-hover:text-slate-700">...</p>
  </div>
</div>
```

### 4.open & close

​	这个就是专门给<detail>用的，因为他默认会给出一个三角形然后你打开就能看更多内容，这个就是管打开与关闭的时候的样式的

<img src="D:\Web学习\前端\assets\image-20241125185000304.png" alt="image-20241125185000304" style="zoom:67%;" />

```
<div class="max-w-lg mx-auto p-8">
  <details class="open:bg-white dark:open:bg-slate-900 open:ring-1 open:ring-black/5 dark:open:ring-white/10 open:shadow-lg p-6 rounded-lg" open>
    <summary class="text-sm leading-6 text-slate-900 dark:text-white font-semibold select-none">
      Why do they call it Ovaltine?
    </summary>
    <div class="mt-3 text-sm leading-6 text-slate-600 dark:text-slate-400">
      <p>The mug is round. The jar is round. They should call it Roundtine.</p>
    </div>
  </details>
</div>
```

## **V**、自定义修饰符

### 1.任意变量(&)

​	就像[任意值](https://tailwindcss.com/docs/adding-custom-styles#using-arbitrary-values)允许您在实用程序类中使用自定义值一样，任意变体允许您直接在 HTML 中编写自定义选择器修饰符。

​	&的意思就是代表当前标签，下面的&就代表`li` 

​	任意变体只是表示选择器的格式字符串，括在方括号中。例如，这个任意修饰符仅当元素是第三个子元素时才选择该元素：

```html
<ul role="list">
  {#each items as item}
    <li class="[&:nth-child(3)]:underline">{item}</li>
  {/each}
</ul>


//生成的CSS
li:nth-child(3) {
  text-decoration: underline;
}

:nth-child的选择是当前元素在父元素中的位置
```

[`addVariant`格式字符串与您使用插件 API](https://tailwindcss.com/docs/plugins#adding-variants)时使用的格式字符串相同，代表`&`要修改的选择器。

#### 1.1.堆叠写法

任意变体可以叠罗汉，一个一个:接起来，跟其他tailwind的类名都一样：

```html
<ul role="list">
  {#each items as item}
    <li class="lg:[&:nth-child(3)]:hover:underline">{item}</li>
  {/each}
</ul>
```

#### 1.2.空格写法

​	如果选择器中需要空格，以此生成包含选择器，则可以使用下划线。下面的例子会选择`div`内的所有`p`元素：

```html
<div class="[&_p]:mt-4">
  <p>Lorem ipsum...</p>
  <ul>
    <li>
      <p>Lorem ipsum...</p>
    </li>
    <!-- ... -->
  </ul>
</div>

//生成的CSS
div p {
  margin-top: 1rem; /* mt-4 的效果 */
}
```

#### 1.3.@media @supports

```html
div class="flex [@supports(display:grid)]:grid">
  <!-- ... -->
</div>
```

使用 @-rule 自定义修饰符时，`&`占位符不是必需的，就像使用预处理器嵌套时一样。

您甚至可以将 @ 规则和常规选择器修饰符结合起来，直接在@后面加个{}，写在花括号里面就行，花括号在中括号里面：

```html
<button type="button" class="[@media(any-hover:hover){&:hover}]:opacity-100">
  <!-- ... -->
</button>

/*条件是：
设备支持悬浮（比如鼠标悬浮），并且
当前按钮被鼠标悬浮。*/
```

### 2.创建变体插件

这玩意儿可以封装大量麻烦的语句以供简单的使用

[具体看这里](https://tailwindcss.com/docs/plugins)

## **VI**、高级

### 1.自己设置class

```
main.css //只要是最主要的那个CSS文件就行，具体是个啥名字管不着

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer utilities {
  .content-auto {
    content-visibility: auto;
  }
}
```

这样就可以把代码简化成

```
<div class="lg:content-auto"> //当屏幕满足lg的时候就启用content-auto类，所以就会跟上面一段代码的原生css配上
  <!-- ... -->
</div>

否则你可能要写成
<div class="lg:content-visibility-auto">
  <!-- ... -->
</div>
```

tailwind会自动判断冒号后面是**堆叠的类**还是你要**应用的样式**所以上面第二个中的`lg:content-visibility-auto`会把lg认为是修饰符，

`content-visibility-auto`这个当做是样式

### 2.[堆叠修饰符的详细说明](https://tailwindcss.com/docs/hover-focus-and-other-states#ordering-stacked-modifiers)

​	堆叠修饰符时，它们是从内到外应用的，就像嵌套函数调用一样

# 三、响应式设计

## 1.响应屏幕大小

根据屏幕宽度来调整你显示出来的图片等东西的宽度高度，前提是你要在<head>中有

```
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

以下就是一个根据视口大小来调整图片大小的例子


```
<!-- Width of 16 by default, 32 on medium screens, and 48 on large screens -->
<img class="w-16 md:w-32 lg:w-48" src="...">

//也可以这样写
<div class="w-16 md:w-32 lg:w-48 h-16 md:h-32 lg:h-48"> //默认宽度、高度w-16 h-16
  <img class="w-full h-auto" object-contain src="..." alt="example image">
</div>

// w-full就是width: 100%
//h-auto就是height: auto
```

默认情况下有五个断点，灵感来自于常见的设备分辨率:

| 断点前缀 | 最小宽度 | CSS                                  |
| -------- | -------- | ------------------------------------ |
| `sm`     | 640px    | `@media (min-width: 640px) { ... }`  |
| `md`     | 768px    | `@media (min-width: 768px) { ... }`  |
| `lg`     | 1024px   | `@media (min-width: 1024px) { ... }` |
| `xl`     | 1280px   | `@media (min-width: 1280px) { ... }` |
| `2xl`    | 1536px   | `@media (min-width: 1536px) { ... }` |

这几个断点前缀不一定要媒体查询的时候才能用，也是可以做为值来使用的，比如

```
//表示无论屏幕大小如何，这个div的大小最大宽度就是2xl（也就是1536px）

<div class="max-w-2xl bg-gray-200">
  <!-- 内容 -->
</div>



//在小屏幕上：max-w-full 会使元素的宽度最大为其父容器的宽度，元素可以自适应。
//在大屏幕（md 或更大）上：md:max-w-2xl 会将元素的最大宽度限制为 42rem。

<div class="max-w-full md:max-w-2xl bg-gray-200">
  <!-- 内容 -->
</div>
```

## 2.[移动优先](https://tailwindcss.com/docs/responsive-design#working-mobile-first)(适配移动设备)

不要`sm:`用于定位移动设备

```html
<!-- This will only center text on screens 640px and wider, not on small screens -->
<div class="sm:text-center"></div>
```

使用不带前缀的实用程序来定位移动设备，并在更大的断点处覆盖它们

```html
<!-- This will center text on mobile, and left align it on screens 640px and wider -->
<div class="text-center sm:text-left"></div>
```

​	说白了就是，先给移动设备写样式，然后在一点点适配大屏幕的样式，用md lg xl 2xl这种去覆盖。

​	因为手机屏幕通常很小，sm是640px，也会比大多数手机大，所以先写移动设备的样式

### 2.1断点范围

```
<div class="md:max-xl:flex">
  <!-- ... -->
</div>
```

​	Tailwind 为每个断点生成相应的`max-*`修饰符，因此开箱即用，可以使用以下修饰符：

| modifier  | 媒体查询                                         |
| --------- | ------------------------------------------------ |
| `max-sm`  | `@media not all and (min-width: 640px) { ... }`  |
| `max-md`  | `@media not all and (min-width: 768px) { ... }`  |
| `max-lg`  | `@media not all and (min-width: 1024px) { ... }` |
| `max-xl`  | `@media not all and (min-width: 1280px) { ... }` |
| `max-2xl` | `@media not all and (min-width: 1536px) { ... }` |

### 2.2.自定义断点

在tailwind.config.js里面设置

```
tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    screens: {
      'tablet': '640px',
      // => @media (min-width: 640px) { ... }

      'laptop': '1024px',
      // => @media (min-width: 1024px) { ... }

      'desktop': '1280px',
      // => @media (min-width: 1280px) { ... }
    },
  }
}
```

### 2.3.一次性断点

就是添加个任意值，但是你得用max或者min

```
<div class="min-[320px]:text-center max-[600px]:bg-sky-300">
  <!-- ... -->
</div>
```

# 四、暗模式

有`darkMode: 'media'`或 `darkMode: 'class‘`还可以自定义暗模式

## 1.darkMode: media:

​	这个就是纯全自动，自动判断用户系统的颜色然后决定是否使用dark：的样式

## 2.darkMode: 'class‘

​	这个有点不智能，就是一定要在class里面设置了dark，然后才会让dark：的样式生效

## 3.切换暗模式

这里讲的是，实现 **`darkMode: 'media'` 的同时允许用户手动切换暗模式**

### 3.1. 修改配置

将 `darkMode` 设置为 `'class'`：

```
// tailwind.config.js

module.exports = {
  darkMode: 'class', // 手动控制切换，但仍会结合媒体查询
  content: ['./src/**/*.{html,js}'], // Tailwind 样式路径
  theme: {
    extend: {},
  },
}
```

配置为 `'class'` 是为了让我们可以动态控制 `dark` 类，而非完全依赖系统的 `prefers-color-scheme`。

------

### 3.2. HTML 结构

准备一个按钮，用于切换亮/暗模式：

```
App.vue

<template>
  <div class="min-h-screen" :class="{ dark: isDarkMode }">
    <!-- 页面内容 -->
    <div class="bg-white dark:bg-gray-800 text-black dark:text-white min-h-screen flex flex-col items-center justify-center">
      <h1 class="text-2xl">Tailwind Dark Mode</h1>
      <button
        @click="toggleTheme"
        class="mt-4 px-4 py-2 bg-gray-300 dark:bg-gray-700 text-black dark:text-white rounded"
      >
        {{ isDarkMode ? 'Switch to Light Mode' : 'Switch to Dark Mode' }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// 定义响应式变量
const isDarkMode = ref(false);

// 切换主题模式
const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value;
  document.documentElement.classList.toggle('dark', isDarkMode.value); //只有ref不行，还要用这个来删除DOM里面的类                                                                        //dark
  localStorage.setItem('theme', isDarkMode.value ? 'dark' : 'light');
};

// 初始化主题模式
const applyInitialTheme = () => {
  const userPreference = localStorage.getItem('theme');//为什么这里要用webStorage的知识呢，是因为防止用户刷新页面之                                                        //后，保存的变量没了，所以用这个API存起来
  const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;

  // 根据用户选择或系统偏好设置主题，如果是暗模式，则 systemPrefersDark 会为 true，否则为 false。
  if (userPreference === 'dark' || (!userPreference && systemPrefersDark)) {
    isDarkMode.value = true;
    document.documentElement.classList.add('dark');
  } else {
    isDarkMode.value = false;
    document.documentElement.classList.remove('dark');
  }
};

// 在组件挂载时设置初始主题
onMounted(() => {
  applyInitialTheme();
});
</script>

<style>
/* 你可以在这里添加全局样式 */
</style>

```

####  `.classList`

​	`classList` 是一个 JavaScript 的 DOM 属性，用于操作元素的 `class` 属性。它返回一个 `DOMTokenList` 对象，可以方便地进行类的添加、删除、切换和检查等操作。

**常见用法**:

- **`element.classList.add(className)`**：添加一个类名，如果该类名已存在则不会重复添加。
- **`element.classList.remove(className)`**：移除指定的类名，如果该类名不存在也不会报错。
- **`element.classList.contains(className)`**：检查元素是否包含指定的类，返回 `true` 或 `false`。
- **`element.classList.toggle(className)`**：如果类名存在则移除，如果类名不存在则添加。

#### `toggle`:

​	`toggle` 是 `classList` 对象的方法，用于切换类。它可以根据当前类是否存在来决定是添加类还是移除类。这个方法有两个参数：

- **`toggle(className)`**：如果 `className` 类名存在于元素的 `class` 属性中，则移除该类；如果不存在，则添加该类。
- **`toggle(className, force)`**：如果 `force` 为 `true`，则始终添加该类；如果 `force` 为 `false`，则始终移除该类

 `document.documentElement`：是元素<html>的引用

#### `window.matchMedia()`

​	 返回一个 `MediaQueryList` 对象，它有以下几个重要属性和方法：

- **`matches`**：返回一个布尔值，表示当前的文档是否匹配指定的媒体查询。
- **`media`**：返回与媒体查询相关的字符串，通常用于调试。
- **`addListener()`**（旧版方法）：用于添加监听器，以便在媒体查询状态变化时执行回调函数。
- **`addEventListener()`**（现代方法）：与 `addListener()` 类似，用于添加事件监听器，监听媒体查询的变化。

### 3.3.自定义暗模式

```
tailwindcss.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ['variant', [
    '@media (prefers-color-scheme: dark) { &:not(.light *) }',
    '&:is(.dark *)',
  ]],
  // ...
}
```

就是使用`variant`，数组里的意思是

1.既要用户的系统设置是暗模式，也要这个父元素没有.light的元素

2.所有具有 `.dark` 类的元素

# 五、添加自定义样式

## 1.自定义主题

就是直接把你自定义的东西，按照标准添加到tailwind.config.js文件的theme属性里面去

```
tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    screens: {
      sm: '480px',
      md: '768px',
      lg: '976px',
      xl: '1440px',
    },
    colors: {
      'blue': '#1fb6ff',
      'pink': '#ff49db',
      'orange': '#ff7849',
      'green': '#13ce66',
      'gray-dark': '#273444',
      'gray': '#8492a6',
      'gray-light': '#d3dce6',
    },
    fontFamily: {
      sans: ['Graphik', 'sans-serif'],
      serif: ['Merriweather', 'serif'],
    },
    extend: {
      spacing: {
        '128': '32rem',
        '144': '36rem',
      },
      borderRadius: {
        '4xl': '2rem',
      }
    }
  }
}
```

然后直接在class里面使用这些键值对的键就可以

## 2.使用任意值

​	任意值就是用个`[]`装起来的，下面直接看例子就能懂

```
//直接干用
<div class="top-[117px]">
  <!-- ... -->
</div>

//搭配响应式修饰符
<div class="top-[117px] lg:top-[344px]">
  <!-- ... -->
</div>

//适用于框架中的所有内容，包括背景颜色、字体大小、伪元素内容等
<div class="bg-[#bada55] text-[22px] before:content-['Festivus']">
  <!-- ... -->
</div>

//甚至可以使用theme函数来引用tailwind.config.js文件中的设计标记：
<div class="grid grid-cols-[fit-content(theme(spacing.32))]">
  <!-- ... -->
</div>

//使用CSS变量，就不用再写var()了
<div class="bg-[--my-color]">
  <!-- ... -->
</div>
```

### 2.1.任意属性、任意变体、处理空格、解决歧义

[直接看文档](https://tailwindcss.com/docs/adding-custom-styles)

```
<ul role="list">
  {#each items as item}
    <li class="lg:[&:nth-child(3)]:hover:underline">{item}</li>
  {/each}
</ul>


<div class="[--scroll-offset:56px] lg:[--scroll-offset:44px]">
  <!-- ... -->    //这里就是直接给CSS变量按照媒体查询条件重新赋值
</div>
```

在值前面添加数据类型来**解决歧义**

```
<!-- Will generate a font-size utility -->
<div class="text-[length:var(--my-var)]">...</div>

<!-- Will generate a color utility -->
<div class="text-[color:var(--my-var)]">...</div>
```

## 3.[使用 CSS 和 @layer](https://tailwindcss.com/docs/adding-custom-styles#using-css-and-layer)

下面这个代码有些时候还会出现在`index.css`，不管是哪个文件，反正一定要出现在根级css文件里面

```css
main.css

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .my-custom-style {
    /* ... */
  }
}
```

base components utilities是tailwind的三个层，这个是默认的声明顺序，一般别改，所以优先级是

 base < components < utilities

 使用**@layer**可以向目标层添加样式，使用**@apply**可以在css文件中使用**tailwind的工具类**比如：

```
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    @apply text-2xl;
  }
  h2 {
    @apply text-xl;
  }
  /* ... */
}
```

### 不可在组件中使用@layer(除非用插件系统)

这是因为在底层，Vue 和 Svelte 等框架正在`<style>`独立处理每个块，并针对每个块单独运行 PostCSS 插件链。

这意味着如果您有 10 个组件，每个组件都有一个`<style>`块，那么 Tailwind 将被单独运行 10 次，并且每次运行都对其他运行一无所知。因此，Tailwind 无法获取您在 a 中定义的样式`@layer`并将其移动到相应的`@tailwind`指令，因为据 Tailwind 所知，没有`@tailwind`指令可以将其移动到。解决方法用**插件系统**，下面会讲

# 六、功能和指令

下面几个是tailwind默认的层，最后一个不建议使用在3.X版本往上

```
/**
 * This injects Tailwind's base styles and any base styles registered by
 * plugins.
 */
@tailwind base;

/**
 * This injects Tailwind's component classes and any component classes
 * registered by plugins.
 */
@tailwind components;

/**
 * This injects Tailwind's utility classes and any utility classes registered
 * by plugins.
 */
@tailwind utilities;

/**
 * Use this directive to control where Tailwind injects the hover, focus,
 * responsive, dark mode, and other variants of each class.
 *
 * If omitted, Tailwind will append these classes to the very end of
 * your stylesheet by default.
 */
@tailwind variants;
```

## 1.@layer

@layer告诉tailwind你写的CSS样式代码是属于哪个层的。有效的层为`base`、`components`和`utilities`

```
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    @apply text-2xl;
  }
  h2 {
    @apply text-xl;
  }
}

@layer components {
  .btn-blue {
    @apply bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded;
  }
}

@layer utilities {
  .filter-none {
    filter: none;
  }
  .filter-grayscale {
    filter: grayscale(100%);
  }
}
```

## 2.@apply

就是把tailwind的写在class里面的东西，写到CSS文件里面去

```
.select2-dropdown {
  @apply rounded-b-lg shadow-md;
}
.select2-search {
  @apply border border-gray-300 rounded;
}
.select2-results__group {
  @apply text-lg font-bold text-gray-900;
}
```

​	注意，如果在**同一个文件**里面创建了一个选择器，然后使用**@apply使用**的时候如果加上了**!impotant**，**!impotant**也会被@apply给删除，因为要防止发生特殊性的问题，如下所示

```
/* Input */
.foo {
  color: blue !important; /* 定义了一个具有 !important 的规则 */
}

.bar {
  @apply foo; /* 想通过 @apply 引入 .foo 的样式 */
}
// 他会被渲染成
/* Output */
.foo {
  color: blue !important; /* 原始规则中的 !important 保留 */
}

.bar {
  color: blue; /* 应用 .foo 样式时，!important 被移除 */
}
```

​	但是你又很想用`!imoprtant`这个时候就可以在@apply语句的最后面写，就像下面这样：

```
/* Input */
.btn {
  @apply font-bold py-2 px-4 rounded !important;
}


//会变成
/* Output */
.btn {
  font-weight: 700 !important;
  padding-top: .5rem !important;
  padding-bottom: .5rem !important;
  padding-right: 1rem !important;
  padding-left: 1rem !important;
  border-radius: .25rem !important;
}
```

### 2.1解决vue等框架组件内不能使用@layer

使用插件系统

```
tailwind.config.js

const plugin = require('tailwindcss/plugin')  //这个是必须写的，就是模板一样的东西，如果是ESM的导入方式就要用import

module.exports = {
  // ...
  plugins: [
    plugin(function ({ addComponents, theme }) {
      addComponents({
        '.card': {
          backgroundColor: theme('colors.white'),
          borderRadius: theme('borderRadius.lg'),
          padding: theme('spacing.6'),
          boxShadow: theme('boxShadow.xl'),
        }
      })
    })
  ]
}

//vue单文件组件中的使用方法，直接使用".card"类就行
<template>
  <div class="card">
    <h2 class="text-xl font-bold">Card Title</h2>
    <p>Card content goes here.</p>
  </div>
</template>

<script setup>

//或者直接在<style>标签里面加进去样式配置也可以，建议别用
<div>
  <slot></slot>
</div>

<style>
  div {
    background-color: theme('colors.white');
    border-radius: theme('borderRadius.lg');
    padding: theme('spacing.6');
    box-shadow: theme('boxShadow.xl');
  }
</style>

```

这个插件的function函数的({})可以写一下几个参数（要用再看，知道就行）：

- **`addComponents`**：用于添加组件样式，通常是比较复杂和可复用的类。

- **`addBase`**：用于添加基础样式，通常是针对 HTML 元素的全局样式。

- **`addUtilities`**：用于添加工具类样式，通常是单一功能的 CSS 类。

- **`theme`**：用于获取 Tailwind 配置中的主题值（如颜色、间距等）。

- **`e`**：用于转义类名，确保动态类名有效。

- **`variants`**：用于访问并控制 Tailwind 的变体生成。

## 3.@config

`@config`指令指定 Tailwind 在编译该 CSS 文件时应使用哪个配置文件。这对于需要为不同的 CSS 入口点使用不同配置文件的项目很有用

比如下面的例子，可以给**站点**跟**管理员**使用不一样的配置文件

```
//site.css

@config "./tailwind.site.config.js";

@tailwind base;
@tailwind components;
@tailwind utilities;

//admin.css
@config "./tailwind.admin.config.js";

@tailwind base;
@tailwind components;
@tailwind utilities;
```



# 七、文件配置详解(tailwind.config.js)

## 1.content

`content`部分是您配置所有 HTML 模板、JS 组件以及任何其他包含 Tailwind 类名的文件的路径的地方

你那里用了tailwind类，你就要把哪个位置放到这个里面去

## 2.theme

`theme`部分中，您可以定义调色板、字体、类型比例、边框大小、断点 — — 任何与您网站的视觉设计相关的内容

当你在原生CSS语句中想要用theme里面设置的内容的时候你可以直接

```
.content-area {
  height: calc(100vh - theme(spacing.12));
}
```

就是用个theme()然后按js对象的方法使用就行

## 3.plugins

`plugins`部分允许您向 Tailwind 注册插件，可用于生成额外的实用程序、组件、基本样式或自定义变体。

官方插件都有下面这些，特别是容器查询，感觉用到的概率还是非常大的

<img src="D:\Web学习\前端\assets\image-20241129211424935.png" alt="image-20241129211424935" style="zoom:67%;" />

### 3.1.添加自定义插件

####     (1)静态

```
const plugin = require('tailwindcss/plugin')

module.exports = {
  plugins: [
    plugin(function({ addUtilities }) {
      addUtilities({
        '.content-auto': {
          'content-visibility': 'auto',
        },
        '.content-hidden': {
          'content-visibility': 'hidden',
        },
        '.content-visible': {
          'content-visibility': 'visible',  //这样在类中使用.content-visible就等于这个
        },
      })
    })
  ]
}
```

#### (2)动态

动态就是可以传进去值，真的可以改变的

```
const plugin = require('tailwindcss/plugin')

module.exports = {
  theme: {
    tabSize: {    //它指定了 matchUtilities 要使用的值来源
      1: '1',
      2: '2',
      4: '4',
      8: '8',
    }
  },
  plugins: [
    plugin(function({ matchUtilities, theme }) {  //函数导入theme，方便后续指定值的来源
      matchUtilities(
        {
          tab: (value) => ({
            tabSize: value
          }),
        },
        { values: theme('tabSize') } //使用上面theme里面的值
      )
    })
  ]
}

//用的时候就像下面这样
<div class="tab-[13]">
  <!-- ... -->
</div>
```



## 4.presets

`presets`节允许您指定自己的自定义基本配置，而不是使用 Tailwind 的默认基本配置

## 5.前缀

前缀只能有一个，这个前缀**不是**前面的浏览器前缀（供应商前缀）

```
export default{
	prefix: 'tw-',
}
```

​	这样你就要在所有tailwind类前面加上tw-，这样才能正常使用，但是有些不需要，此前缀是在任何变体修饰符*之后*`sm:`添加的。这意味着具有 responsive 或 state 修饰符（如或 ）的类比如`hover:`仍将*首先*具有 responsive 或 state 修饰符，而您的自定义前缀将出现在冒号之后

如果后面的值用了负数，要把负号放在**前缀之前**

```
//-8
<div class="-tw-mt-8">
  <!-- -->
</div>

// +8
<div class="tw-mt-8">
  <!-- -->
</div>
```

前缀仅添加到 Tailwind 生成的类；**不会将前缀添加到您自己的自定义类**

## 6.!important

```
export default {
  important: true,
}
```

在配置文件中启用，当由tailwind类生成css样式的时候，会自动给他们加上`!important`

### 选择器策略

​	在整合第三方 JS 库（这些库会向您的元素添加内联样式）时，设置`important`为可能会出问题，这个时候就可以用点特别的方法，实现部分使用

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  // ...
  important: '#app',
}

<html>
<!-- ... -->
<style>
  .high-specificity .nested .selector {
    color: blue;
  }
</style>
<body id="app">
  <div class="high-specificity">
    <div class="nested">
      <!-- Will be red-500 -->
      <div class="selector text-red-500"><!-- ... --></div>
    </div>
  </div>

  <!-- Will be #bada55 -->
  <div class="text-red-500" style="color: #bada55;"><!-- ... --></div>
</body>
</html>
```

使用上面的示例，我们需要将根元素的`id`属性设置为，`app`以使样式正常工作。因为生成的CSS如下：

```
如果对某个元素使用的话

#app .text-red-500 {
  color: #ef4444 !important; /* Tailwind 的红色 */
}

```

你会发现中间多个空格，这就导致你必须给父级元素使用这个id才可以

## 7.分割符

tailwind默认用:来分割，比如：

```
<!-- 默认分隔符为冒号 : -->
<div class="sm:text-center md:text-left hover:text-red-500 focus:text-green-500">
  示例内容
</div>
```

你可以使用，下面的修改：

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  separator: '_',
}
```

## 8.媒体查询之容器查询

https://github.com/tailwindlabs/tailwindcss-container-queries

使用@container给元素标记成容器，然后就能根据该容器的大小应用样式：`@md:``@lg:``@xl:`

详细使用看上面的链接

![image-20241129215128760](D:\Web学习\前端\assets\image-20241129215128760.png)

# 八、布局

## 1.容器

就是类container，但是他很神奇，他自动做以下工作

在 Tailwind 中，`container` 类会：

- 默认具有 `max-width: 100%`，即容器的宽度会填满父元素的宽度。

- 随着屏幕大小变化，`container` 会根据 Tailwind 的断点（如 `sm`, `md`, `lg`, `xl`）调整宽度。

- 例如，默认情况下它的宽度是 100%，但在大屏幕上（如 `lg` 或 `xl`），它的宽度会被限制在某个最大值。

  你可以设置响应式的容器，居中用**`mx-auto`**，或者设置一下让他默认居中，水平填充用**`px-*`**（可自定义）

<img src="D:\Web学习\前端\assets\image-20241129223601837.png" alt="image-20241129223601837" style="zoom:67%;" />

## 2.上右下左

首先记住这个转换公式

| top-4 | top: 1rem; /* 16px */ |
| ----- | --------------------- |
|       |                       |

### 2.1[放置定位元素](https://tailwindcss.com/docs/top-right-bottom-left#placing-a-positioned-element)

使用`top-*`、`right-*`、`bottom-*`、`left-*`和`inset-*`实用程序设置[定位元素](https://tailwindcss.com/docs/position)的水平或垂直位置。

<img src="D:\Web学习\前端\assets\image-20241130145941613.png" alt="image-20241130145941613" style="zoom:67%;" />

```html
<!-- Pin to top left corner -->
<div class="relative h-32 w-32 ...">
  <div class="absolute left-0 top-0 h-16 w-16 ...">01</div>
</div>

<!-- Span top edge -->
<div class="relative h-32 w-32 ...">
  <div class="absolute inset-x-0 top-0 h-16 ...">02</div>
</div>

<!-- Pin to top right corner -->
<div class="relative h-32 w-32 ...">
  <div class="absolute top-0 right-0 h-16 w-16 ...">03</div>
</div>

<!-- Span left edge -->
<div class="relative h-32 w-32 ...">
  <div class="absolute inset-y-0 left-0 w-16 ...">04</div>
</div>

<!-- Fill entire parent -->
<div class="relative h-32 w-32 ...">
  <div class="absolute inset-0 ...">05</div>
</div>

<!-- Span right edge -->
<div class="relative h-32 w-32 ...">
  <div class="absolute inset-y-0 right-0 w-16 ...">06</div>
</div>

<!-- Pin to bottom left corner -->
<div class="relative h-32 w-32 ...">
  <div class="absolute bottom-0 left-0 h-16 w-16 ...">07</div>
</div>

<!-- Span bottom edge -->
<div class="relative h-32 w-32 ...">
  <div class="absolute inset-x-0 bottom-0 h-16 ...">08</div>
</div>

<!-- Pin to bottom right corner -->
<div class="relative h-32 w-32 ...">
  <div class="absolute bottom-0 right-0 h-16 w-16 ...">09</div>
</div>
```

### 2.2[使用负值](https://tailwindcss.com/docs/top-right-bottom-left#using-negative-values)

要使用负的顶部/右侧/底部/左侧值，请在类名前加上破折号以将其转换为负值。

```html
<div class="relative h-32 w-32 ...">
  <div class="absolute h-14 w-14 -left-4 -top-4 ..."></div>
</div>
```

### [2.3使用逻辑属性](https://tailwindcss.com/docs/top-right-bottom-left#using-logical-properties)

使用`start-*`和`end-*`实用程序设置`inset-inline-start`和`inset-inline-end` [逻辑属性](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Logical_Properties/Basic_concepts)，它们根据文本方向映射到左侧或右侧。

从左到右

从右到左

```html
<div dir="ltr">
  <div class="relative h-32 w-32 ...">
    <div class="absolute h-14 w-14 top-0 start-0 ..."></div>
  </div>
<div>

<div dir="rtl">
  <div class="relative h-32 w-32 ...">
    <div class="absolute h-14 w-14 top-0 start-0 ..."></div>
  </div>
<div>
```

为了获得更多控制，您还可以使用[LTR 和 RTL 修饰符](https://tailwindcss.com/docs/hover-focus-and-other-states#rtl-support)根据当前文本方向有条件地应用特定样式。

## 3.[间距](https://tailwindcss.com/docs/space)

用`space`，用于控制子元素之间空间，他的原理是使用margin（但是只有top和bottom)，下面是跟gap的对比

### **区别总结**

| 特性             | `space-*`                    | `gap`                         |
| ---------------- | ---------------------------- | ----------------------------- |
| **实现方式**     | 通过 `margin`                | 内建布局属性，非 `margin`     |
| **适用场景**     | 通常适用于非弹性或非网格布局 | 适用于 `grid` 和 `flex` 布局  |
| **子项间的间距** | 仅兄弟元素之间有效           | 所有子项之间的间距均一致      |
| **浏览器兼容性** | 与普通 CSS 属性一致          | `gap` for `flex` 需现代浏览器 |
| **父元素的作用** | 直接作用于子元素的 `margin`  | 作用于布局的整体模型          |

# 九、theme

`theme` 对象包含 `screens`、`colors` 和 `spacing` 的键，以及每个可自定义核心插件的键。

## 1.screen

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    screens: {
      'sm': '640px',
      'md': '768px',
      'lg': '1024px',
      'xl': '1280px',
      '2xl': '1536px',
    }
  }

```

## 2.color

## 3.spacing

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    spacing: {
      px: '1px',
      0: '0',
      0.5: '0.125rem',
      1: '0.25rem',
      1.5: '0.375rem',
      2: '0.5rem',
      2.5: '0.625rem',
      3: '0.75rem',
      3.5: '0.875rem',
      4: '1rem',
      5: '1.25rem',
      6: '1.5rem',
      7: '1.75rem',
      8: '2rem',
      9: '2.25rem',
      10: '2.5rem',
      11: '2.75rem',
      12: '3rem',
      14: '3.5rem',
      16: '4rem',
      20: '5rem',
      24: '6rem',
      28: '7rem',
      32: '8rem',
      36: '9rem',
      40: '10rem',
      44: '11rem',
      48: '12rem',
      52: '13rem',
      56: '14rem',
      60: '15rem',
      64: '16rem',
      72: '18rem',
      80: '20rem',
      96: '24rem',
    },
  }
}
```

## 4.主要的插件

例如，该`borderRadius`键可让您自定义将生成哪些边框半径实用程序：

```js
module.exports = {
  theme: {
    borderRadius: {
      'none': '0',
      'sm': '.125rem',
      DEFAULT: '.25rem',   //这里就表示，使用单独rounded类，不加任何修饰符的时候的样式
      'lg': '.5rem',
      'full': '9999px',
    },
  }
}
```

键决定生成的类的后缀，值决定实际 CSS 声明的值。

上面的示例`borderRadius`配置将生成以下 CSS 类：

```css
.rounded-none { border-radius: 0 }
.rounded-sm   { border-radius: .125rem }
.rounded      { border-radius: .25rem }   //就是上面的  DEFAULT: '.25rem'
.rounded-lg   { border-radius: .5rem }
.rounded-full { border-radius: 9999px }
```

## 5.拓展主题

写在extend属性内，此键下的值将与现有`theme`值合并，并自动变为可供您使用的新类。

作为示例，这里我们扩展`fontFamily`属性以添加`font-display`可以改变元素上使用的字体的类：

```js
tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        display: 'Oswald, ui-serif', // Adds a new `font-display` class
      }
    }
  }
}
```

## 6.自定义默认主题

如果你要拓展，而不是覆盖，就要把所有的属性写在extend属性里面，比如：

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        display: 'Oswald, ui-serif', // Adds a new `font-display` class
      }
    }
  }
}
```

如果是要覆盖，就直接不写在extend里面

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    // Replaces all of the default `opacity` values
    opacity: {
      '0': '0',
      '20': '0.2',
      '40': '0.4',
      '60': '0.6',
      '80': '0.8',
      '100': '1',
    }
  }
}
```

### 6.1.引用theme里面的属性

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    spacing: {
      1: '0.25rem',
  	  2: '0.5rem',
  	  3: '0.75rem',
  	  4: '1rem',
	  5: '1.25rem',
  	  // ...
    },
    backgroundSize: ({ theme }) => ({
      auto: 'auto',
      cover: 'cover',
      contain: 'contain',
      ...theme('spacing')
    })
  }
}
```

使用`...theme('spacing')`和箭头函数，来使用theme中的`spacing`，然后会生成以下类

```
.bg-auto: 设置背景大小为 auto。
.bg-cover: 设置背景大小为 cover。
.bg-contain: 设置背景大小为 contain。
.bg-1: 设置背景大小为 0.25rem（假设 spacing 中包含 1）。
.bg-2: 设置背景大小为 0.5rem（假设 spacing 中包含 2）。
.bg-3: 设置背景大小为 0.75rem（假设 spacing 中包含 3）。
.bg-4: 设置背景大小为 1rem（假设 spacing 中包含 4）。
.bg-5: 设置背景大小为 1.25rem（假设 spacing 中包含 5）
```

### 6.2引用默认主题值

```
const defaultTheme = require('tailwindcss/defaultTheme')

module.exports = {
  theme: {
    extend: {
      fontFamily: {
        sans: [
          'Lato',   //让浏览器优先尝试Lato字体，然后在用tailwind的所有默认字体尝试
          ...defaultTheme.fontFamily.sans,
        ]
      }
    }
  }
}
```

​	是不是觉得很鸡肋？是的我一开始也这么觉得，但是这个用法有个好处就是，不会影响tailwind类原来的任何东西，就如上例子，这么用不会改变tailwind使用字体的顺序，也就是当第一个字体不支持他回去找第二个字体，这样一直下去，你采用这个方式不会影响他的顺序

### 6.3.禁用主题的某个属性

**不要用**空对象：

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    opacity: {},
  }
}
```

要用**false**，写在`corePlugins`里面

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  corePlugins: {
    opacity: false,
  }
}
```

### [配置参考](https://tailwindcss.com/docs/theme#configuration-reference)













# 拓展：

## 1.使用[]的几大情况

### 1.1**自定义值（例如：宽度、颜色、边距等）**

您可以使用 `[]` 来直接定义自定义的值，而不需要在 `tailwind.config.js` 中配置。示例：

- **宽度、长度、间距、边框等：**

```
html复制代码<div class="w-[320px] h-[200px] p-[15px]">
  自定义宽度、高度和内边距
</div>
```

这里，`w-[320px]` 和 `h-[200px]` 就是使用了自定义的宽度和高度。

- **颜色：**

```
html复制代码<div class="bg-[#ff6347] text-[#4b0082]">
  自定义颜色
</div>
```

这里，`bg-[#ff6347]` 是自定义的背景颜色（番茄色），`text-[#4b0082]` 是自定义的文本颜色（靛青色）。

### 1.2. **动态计算值（例如：百分比或变量）**

您可以在 `[]` 中使用动态计算的值，例如百分比、CSS 变量、甚至是动态的 Tailwind 类。示例：

- **百分比宽度：**

```
html复制代码<div class="w-[calc(100%-20px)]">
  宽度是 100% 减去 20px
</div>
```

- **使用 CSS 变量：**

```
html复制代码<div class="bg-[var(--my-color)]">
  使用 CSS 变量作为背景色
</div>
```

### 1.3. **背景图片和背景位置**

Tailwind 允许通过 `[]` 设置复杂的背景图片、背景位置等 CSS 属性。示例：

- **背景图片：**

```
html复制代码<div class="bg-[url('https://example.com/image.jpg')]">
  背景是图片
</div>
```

- **背景位置：**

```
<div class="bg-[position:top_right]">
  背景位置在右上角
</div>
```

### 1.4. **字体大小和行高等单位（如 rem, px）**

可以通过 `[]` 来设置精确的单位，适用于字体大小、行高等。示例：

```
<div class="text-[24px] leading-[30px]">
  字体大小 24px，行高 30px
</div>
```

### 1.5. **自定义旋转和变换**

通过 `[]` 可以传递自定义的旋转角度或变换值。示例：

```
<div class="rotate-[45deg] scale-[1.2]">
  自定义旋转角度 45 度和缩放 1.2倍
</div>
```

### 1.6. **阴影**

使用 `[]` 来定义自定义的阴影效果。示例：

```
<div class="shadow-[0_4px_6px_rgba(0,0,0,0.1)]">
  自定义阴影效果
</div>
```

### 1.7. **边框样式**

通过 `[]` 你可以为边框设置具体的样式，比如边框宽度、颜色、样式等。示例：

```
<div class="border-[5px_solid_#333]">
  自定义边框宽度、样式和颜色
</div>
```

### 1.8. **动画与过渡**

`[]` 还可以用于设置自定义的动画或过渡效果。示例：

```
<div class="transition-[all_0.5s_ease-in-out]">
  自定义过渡效果
</div>
```

### 1.9. **媒体查询中的自定义断点**

在使用 JIT 模式时，您也可以通过 `[]` 来为特定的媒体查询指定自定义的断点。示例：

```
<div class="sm:[min-width:600px] lg:[min-width:1024px]">
  自定义媒体查询
</div>
```

## 2.tailwindCSS自动补transition-all

​	Tailwind CSS 默认会对几乎所有的状态变化（如 `hover`、`focus` 等）应用平滑过渡效果，尤其是对于可以过渡的属性（如 `background-color`、`box-shadow`、`transform` 等）。因此，当你指定了 `hover:scale-[1.12]` 和 `hover:shadow-button-shadow` 时，Tailwind 会自动推断出这些属性需要进行平滑过渡，默认情况下会应用 `transition-all`，意味着所有支持过渡的属性都会过渡。

- **`transition-all`** 会影响所有支持过渡的 CSS 属性，包括阴影和缩放。
- `duration-[350ms]` 为这个过渡设置了 350 毫秒的持续时间。

也就是说，如果你有如下语句

```
              shadow-lg
              hover:shadow-button-shadow
              hover:scale-[1.12]
              duration-[350ms]
```

即使你没有给出`transition`要应用到的样式，tailwind会自动给你补上`transition-all`























































main.css

tailwind.config.js
