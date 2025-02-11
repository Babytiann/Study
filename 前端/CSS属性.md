规则应用的顺序如下：

1. **层级顺序**（最后定义的规则优先）
2. **选择器特异性**（特异性高的规则优先）
3. **`!important`**（`!important` 的规则优先覆盖非 `!important` 的规则）

# 一.transition

​	`transition`属性可以被指定为一个或多个 CSS 属性的过渡效果，多个属性之间用逗号进行分隔。

​	以下是使用示例：

```
/* Apply to 1 property */
/* property name | duration */
transition: margin-right 4s;

/* property name | duration | delay */
transition: margin-right 4s 1s;

/* property name | duration | timing function */
transition: margin-right 4s ease-in-out;

/* property name | duration | timing function | delay */
transition: margin-right 4s ease-in-out 1s;

/* Apply to 2 properties */
transition:
  margin-right 4s,
  color 1s;

/* Apply to all changed properties */
transition: all 0.5s ease-out;

/* Global values */
transition: inherit;
transition: initial;
transition: unset;
```

**transition只是确定过渡动画，至于变多少可以由伪类甚至是js来确定**，也就是说上面的`margin-right`只是告诉CSS，过度动画应用在这个东西改变的时候，但是具体怎么变可能要用伪类比如`:hover`

对于第四句代码，每一块都干啥的分析：

1.**`margin-right`**(transition-property)

- 这是你希望应用过渡效果的 CSS 属性。在这里，指定的是 `margin-right`，也就是元素的右外边距。
- **可以填的内容**：任何有效的 CSS 属性名。常见的有 `color`、`opacity`、`transform`、`height` 等。

2.**`4s`**(transition-duration)

- 这是过渡的持续时间，也就是属性值变化所需的时间。

- 可以填的内容

  ：任何合法的时间单位（s 表示秒，ms 表示毫秒）。例如：

  - `1s`：1秒
  - `500ms`：500毫秒
  - `0.25s`：0.25秒

3.**`ease-in-out`**(transition-timing-function)

- 这是过渡的 timing-function（时间函数），控制属性变化的速度曲线。

- 可以填的内容

  ：常见的时间函数有：

  - `linear`：匀速变化，开始和结束的速度一样。
  - `ease`：先慢后快，再慢。默认值。
  - `ease-in`：开始慢，最后快。
  - `ease-out`：开始快，最后慢。
  - `ease-in-out`：先慢后快，再慢，具有平滑的加速与减速效果。
  - `cubic-bezier(n,n,n,n)`：使用自定义的贝塞尔曲线进行控制。
  - `steps()`：使用阶梯函数。

4.**`1s`**(transition-delay)

- 这是过渡的延迟时间，指定在开始变化之前等待的时间。

- 可以填的内容

  ：任何合法的时间单位，通常使用 

  s或 ms

  。例如：

  - `0s`：不延迟，立即开始。
  - `500ms`：延迟 500 毫秒后开始过渡。
  - `2s`：延迟 2 秒后开始过渡。

# 二.可见性有关的属性

| 属性/类名   | 作用                                     | 是否占用空间         |
| ----------- | ---------------------------------------- | -------------------- |
| `hidden`    | 元素不可见，`display: none`              | 否                   |
| `block`     | 元素可见且为块级，`display: block`       | 是                   |
| `invisible` | 元素不可见，`visibility: hidden`         | 是                   |
| `visible`   | 元素可见，`visibility: visible`          | 是                   |
| `collapse`  | 元素不可见，适用于表格元素隐藏并移除占位 | 仅表格元素不占用空间 |

还有以下几个，最好知道：

1. **`opacity`**

   - 控制元素的透明度，范围是 `0`（完全透明）到 `1`（完全不透明）。
   - 和 `invisible` 不同，`opacity: 0` 会让内容不可见，但元素仍然占据空间且可以交互（例如点击、拖动等）。

   **示例：**

   ```
   .hidden-opacity {
     opacity: 0;
   }
   ```

2. **`z-index`**

   - 控制元素的堆叠顺序，某些情况下会影响元素是否被“遮盖”，从而看不到。
   - 低的 `z-index` 值可能使元素被其他内容挡住，但它本身仍然可见。

3. **`clip-path`**

   - 定义元素的可视区域，超出该区域的内容会被裁剪。
   - 通过设置为 `clip-path: polygon(0 0, 0 0, 0 0, 0 0)` 可以让整个元素看起来“消失”，但它仍然占据布局空间。

4. **`overflow`**

   - 决定超出容器边界的内容是否显示，比如 `overflow: hidden;` 会隐藏超出的部分。
   - 虽然它影响的是内容可见性，但不会直接作用于容器。

# 三.break-*

这里一共有三个`break-after 、break-before、break-inside`他们用于**多列布局**等这种可能会分割本应该在一起的内容的布局中

（分页、列布局或分隔容器中的“断开行为”的 CSS 属性）

## 3.1. **属性功能比较**

| 属性               | 作用范围                         | 控制点                     | 常见值                                         |
| ------------------ | -------------------------------- | -------------------------- | ---------------------------------------------- |
| **`break-before`** | 控制元素**前面**是否插入分隔符   | 控制元素开始之前的断开行为 | `auto`、`avoid`、`always`、`page`、`column` 等 |
| **`break-after`**  | 控制元素**后面**是否插入分隔符   | 控制元素结束之后的断开行为 | `auto`、`avoid`、`always`、`page`、`column` 等 |
| **`break-inside`** | 控制元素**内部**是否允许内容断开 | 控制元素内内容分裂行为     | `auto`、`avoid`、`avoid-page`、`avoid-column`  |

`break-after` 的行为是 **在当前容器中尽可能放置该元素**，如果容器无法容纳，具体结果取决于设置的值



`break-before` 的核心是 **控制该元素是否必须在新容器（如页面或列）开始**，具体行为取决于值：

- 如果是 `auto`：默认行为，尽量利用当前空间。如果当前页面容不下，会尝试放入下一页/列。

- 如果是 `always` 或 `page`：无论当前页面/列是否有空间，都强制换到下一页/列。

- 如果是 `avoid`：尽量避免换列或分页，内容不足时会尝试容纳。

  

`break-inside` 是 **防止一个元素的内容被分裂到不同容器**（如分页或列中），行为取决于值：

- 如果是 `auto`：允许内容分裂（例如一个段落分布在两页上）。
- 如果是 `avoid`：尽量避免内容分裂。如果当前容器空间不够，会将整个内容放到下一个容器中。

但是after和before都有把盒子分裂的可能性，只有inside不会

## 3.2. **属性值含义对比**

以下值是这三个属性的常见选项：

| 值           | 含义                   | 示例场景                                             |
| ------------ | ---------------------- | ---------------------------------------------------- |
| **`auto`**   | 默认行为，允许自由断开 | 常用于不需要特别处理断开行为的情况。                 |
| **`avoid`**  | 尽量避免断开           | 防止段落、图片组、表格等重要内容被分裂。             |
| **`always`** | 强制插入断开           | 需要明确分页、换列时使用，比如章节开头、强制分列等。 |
| **`page`**   | 强制分页（打印时生效） | 章节打印时确保章节分隔为独立页面。                   |
| **`column`** | 强制换列（多列布局中） | 在多列布局中要求某些内容单独占据新的一列。           |

# 四.box-decoration-break

有两个值，一个是`slice`一个是`clone`，就是决定当盒子被分割的时候，样式如何设置

`slice`就是下面这个样断开的

<img src="D:\Web学习\前端\assets\image-20241130103309773.png" alt="image-20241130103309773" style="zoom:67%;" />

但是克隆，是直接克隆大盒子的样式，所以就会看起来都一样

<img src="D:\Web学习\前端\assets\image-20241130103338035.png" alt="image-20241130103338035" style="zoom:67%;" />

# 五. isolation

该属性与 [`mix-blend-mode`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mix-blend-mode) 和 [`z-index`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/z-index) 结合使用时尤其有用，决定了元素是否必须创建一个新的[层叠上下文](https://developer.mozilla.org/zh-CN/docs/Glossary/Stacking_context)

下面是一个例子，展示了使用`isolation`解决堆叠出现的问题

假设有两个图片，并且你使用了 `z-index` 来调整它们的堆叠顺序，但没有设置 `isolation`，如果父元素的某些属性导致了重叠，它们可能会显示不正确。

```
<div class="container">
  <img src="image1.jpg" class="img1" />
  <img src="image2.jpg" class="img2" />
</div>
css复制代码.container {
  position: relative;
}

.img1 {
  position: absolute;
  z-index: 1;
}

.img2 {
  position: absolute;
  z-index: 2;
}
```

如果 `.container` 的某些属性（比如 `opacity`）影响了它的子元素的层级，图片之间的颜色可能会不正确地混合。此时，你可以通过设置 `isolation: isolate` 来避免这种情况：

```
.container {
  position: relative;
  isolation: isolate;
}
```

这样，`.container` 会成为一个独立的堆叠上下文，确保其内部的图片不会受到外部或父元素的影响，从而解决堆叠问题。

# 六、mask-image

这个东西用于设置一个图像蒙版，这样就能让一张方形的图片展示出来很多种形状，蒙版的形状决定你看到的图片的形状

```
.masked {
  width: 100px;
  height: 100px;
  background-color: #8cffa0;
  -webkit-mask-image: url(star.svg);  //支持老浏览器用的
  mask-image: url(star.svg);  //支持新浏览器用的，这个就是设置蒙版
}
```

# 七、overscroll-behavior

这个能控制滚动条的真实效果比如：

有一个大的元素包裹着小元素，且他俩同时都有滚动条，当你将小元素的滚动条滚到底的话，如果你是`overscroll-behavior: auto`（浏览器默认是这个）如果你继续向下滚动，就会让大元素的滚动条也滚动，显然我们是不愿意看到这个情况的，所以就要设置这个 `overscroll-behavior`，这个属性有下面几个值

```
auto
默认效果

contain
设置这个值后，默认的滚动边界行为不变（“触底”效果或者刷新），但是临近的滚动区域不会被滚动链影响到，比如对话框后方的页面不会滚动。

none
临近滚动区域不受到滚动链影响，而且默认的滚动到边界的表现也被阻止。
```

<img src="D:\Web学习\前端\assets\image-20241130142607400.png" alt="image-20241130142607400" style="zoom:70%;" />

一般用contain就够了

# 八、inline, block, inline-block对比

**`block`**：占据一整行，后面会换行，支持宽高。

**`inline`**：在同一行内显示，不换行，不支持宽高。

**`inline-block`**：在同一行内显示，但可以设置宽高。

## 8.1. **`block`**

- **显示行为**：块级元素（`block`）会占据其所在行的整个宽度，默认情况下，它会从新的一行开始，并且其后的元素会被推到下一行。常见的块级元素包括 `<div>`、`<p>`、`<h1>` 等。

- 特点：

  - 可以设置宽度和高度。
  - 会换行（前后有换行符）。

  示例：

  ```
  <div style="display: block;">块级元素</div>
  ```

## 8.2. **`inline`**

- **显示行为**：行内元素（`inline`）会根据其内容的宽度自动占据空间，不会强制换行，多个行内元素可以在同一行内显示。常见的行内元素有 `<span>`、`<a>`、`<strong>` 等。

- 特点：

  - 不能设置宽度和高度（它只会根据内容的大小来确定宽度）。
  - 不会换行（始终与其他行内元素在同一行）。

  示例：

  ```
  <span style="display: inline;">行内元素</span>
  ```

## 8.3. **`inline-block`**

- **显示行为**：行级块元素（`inline-block`）结合了 `block` 和 `inline` 的特点。它像行内元素一样，能够与其他元素在同一行内显示，但同时也像块级元素一样，可以设置宽度和高度。

- **特点**：

  - 能设置宽度和高度。
  - 不会换行，可以和其他元素在同一行显示（除非内容过多导致换行）。
  - 行内块元素在布局时与其他行内元素并排显示。

  示例：

  ```
  <div style="display: inline-block; width: 100px; height: 50px;">行级块元素</div>
  ```

# 九、order

这个东西控制`flex`和`grid`布局中，每个元素的前后顺序，可以指定，是按`order` **数值从小到大排列**，**默认值是0**

## 区别：

- **`align-items`**：影响单行或单列中的元素对齐方式，控制的是**每个项目在交叉轴上的对齐**。
- **`align-content`**：影响 **多行或多列** 的对齐方式，控制的是**多行或多列之间的间距对齐**

# 十、使用CSS让某一元素居中对齐

## 方法一：transform+position

`transform: translate(-50%, 50%)` 是 CSS 中的 `transform` 属性的一部分，作用是对元素进行平移变换。它的具体含义和作用如下：

### 含义：

- `translate(x, y)`：是一个二维平移变换，x 和 y分别表示在水平方向（x）和垂直方向（y）上平移的距离。
  - **`x` 和 `y`** 可以是像素值（如 `px`）或百分比值（`%`）。
  - 当使用百分比时，它是基于元素的自身尺寸进行平移的。

translate(-50%, 50%)` 的含义：

- **`-50%`（水平平移）**：将元素沿水平方向向左平移元素自身宽度的 50%。例如，如果元素的宽度为 200px，那么它会被平移 100px 向左。
- **`50%`（垂直平移）**：将元素沿垂直方向向下平移元素自身高度的 50%。例如，如果元素的高度为 200px，那么它会被平移 100px 向下。

### 作用：

`transform: translate(-50%, 50%)` 主要用于将元素相对于其原位置进行平移，常常用于定位元素，特别是在需要居中对齐时，配合 `position: absolute` 或 `position: fixed` 使用。

### 典型用法：居中对齐

在下面的代码中：

```
.main-panel {
  position: fixed;  //也可以设置成absolute 当然，父元素的position要设置成relative
  bottom: 50%;
  left: 50%;
  transform: translate(-50%, 50%); 
}
```

因为left设置的是**左边界到父级元素的左边界**的距离，设置50%就是**子级盒子左边界**到**父级盒子左边界**的距离是整个父级盒子宽度的一半，所以会导致子级盒子的正中间偏右，所以用`transform`属性给他往左边移动子级盒子的50%(一半)就能居中

- **`position: fixed`**：将 `.main-panel` 元素固定在页面中某个位置。

- **`bottom: 50%` 和 `left: 50%`**：将元素的左下角定位到视口的 50% 位置（即视口的中点）。

- `transform: translate(-50%, 50%)`

  ：通过平移，修正元素的偏移：

  - **`-50%`**：将元素水平平移自身宽度的 50%，从而使元素的中心点与视口的中心对齐。
  - **`50%`**：将元素垂直平移自身高度的 50%，使元素的底部与视口的 50% 高度对齐。

### 总结：

`transform: translate(-50%, 50%)` 通过移动元素来确保其精确的对齐和定位，通常用来使元素在视口或容器内居中或精确对齐。

## 方法二：使用flex布局

使用`flex`以及`align-items`以及`text-align`

前两个是控制子元素在盒子内的垂直方向上的位置，后者则是控制子元素在他自己盒子里面的水平方向上的位置

```
//HTML
<div id="fa">
  <div id="baby">
      babyt2313131
  </div>
</div>

//CSS 只看布局控制就行，颜色只是为了显而易见的看到子元素在父级元素中的位置
*{
    box-sizing: border-box;
}

#fa{
    display: flex;
    flex-wrap: wrap;
    flex-direction: row;
    align-items: center;
    width: 256px;
    height: 500px;
    background-color: mediumpurple;
}
#baby{
    text-align: center;
    width: 100%;
    backdrop-filter: blur(2px);
}
```

# 十一、background-size

​	这个玩意儿你必须要有一个背景图他才会生效，你可以是渐变，也可以就是一张图片，但是你要是这两个一个都没有，直接用这个属性是不会生效的（background-color也不行）

# 十二、实现背景颜色渐变实例

```
.description {
  background-image: linear-gradient(to right, #f9a8d4, #ec4899); /* 下划线渐变 */
  background-size: 100% 2px; /* 设置背景高度为 2px */
  background-position: left bottom; /* 将背景放置到底部 */
  background-repeat: no-repeat; /* 避免背景重复 */
  transition: background-size 0.3s ease; /* 平滑过渡 */
}

.description:hover {
  background-size: 100% 100%; /* 悬浮时背景覆盖整个内容 */
}
```

指针未悬浮时：

<img src="D:\Web学习\前端\assets\image-20241204155220799.png" alt="image-20241204155220799" style="zoom:90%;" />

指针悬浮时：

<img src="D:\Web学习\前端\assets\image-20241204155248400.png" alt="image-20241204155248400" style="zoom:80%;" />

# 十三、animation

这个就是直接让你生成动画的，你不需要用什么的scale这种，与`transition`不同

在 CSS 中使用 `animation` 属性定义动画时，通常需要搭配 `@keyframes` 规则来定义动画的关键帧内容。以下是详细说明：

## 1. **`animation` 属性**

`animation` 是用来设置动画效果的速记属性。它包含以下几个子属性：

- `animation-name`: 指定要使用的关键帧名称。
- `animation-duration`: 动画的持续时间。
- `animation-timing-function`: 动画的时间函数（如线性、缓动等）。
- `animation-delay`: 动画开始前的延迟时间。
- `animation-iteration-count`: 动画的重复次数（如 `infinite` 表示无限循环）。
- `animation-direction`: 动画的方向（如正向、反向、交替等）。
- `animation-fill-mode`: 设置动画结束时元素的状态（如 `forwards` 保持动画最后一帧）。
- `animation-play-state`: 控制动画的播放或暂停状态。

## 2. **`@keyframes` 规则**

`@keyframes` 用来定义动画的关键帧序列。通过指定多个时间点（百分比或 `from/to`），可以描述动画过程中样式的变化。

#### 示例：

创建一个简单的平移动画：

```
css复制代码/* 定义动画的关键帧 */
@keyframes moveRight {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(200px);
  }
}

/* 应用动画 */
.box {
  width: 50px;
  height: 50px;
  background-color: blue;
  animation: moveRight 2s linear infinite;
}
```

## 3.animation实现皮球弹跳时，产生皮球被压缩的效果(flex)

如下对比：[原网页](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-name)

<img src="D:\Web学习\前端\assets\image-20241222094711350.png" alt="image-20241222094711350" style="zoom:50%;" />

<img src="D:\Web学习\前端\assets\image-20241222094707423.png" alt="image-20241222094707423" style="zoom:50%;" />

​	一个是这个盒子装的元素到底时候的效果，一个是下落过程中的效果，可见其明显被压缩，这就涉及到了height属性的改变，但是我们height属性是定死的，所以我们使用margin属性让这个球跳的时候会发现，这个盒子会溢出，就像下面那样

<img src="D:\Web学习\前端\assets\image-20241222094902001.png" alt="image-20241222094902001" style="zoom:50%;" />

​	所以我们可以用布局的手段，来动态调整这个盒子的height，就比如用flex，因为盒子是纵向跳的，所以我们给他的direction设置成column，内部的小球的那个盒子该怎么设置怎么设置就行，关键的是animation-direction就行，如下所示（部分代码）：

​							父级元素设置：

<img src="D:\Web学习\前端\assets\image-20241222095054887.png" alt="image-20241222095054887" style="zoom:80%;" />

​					子元素盒子设置（小球）：

​											<img src="D:\Web学习\前端\assets\image-20241222095226734.png" alt="image-20241222095226734" style="zoom:80%;" />

<img src="D:\Web学习\前端\assets\image-20241222095245701.png" alt="image-20241222095245701" style="zoom:80%;" />













# 项目最开始时的CSS配置

## 1. **`box-sizing: border-box;`**

这是最常用的默认配置，它改变了元素的盒模型计算方式。默认情况下，元素的宽度和高度是由内容区域的大小决定的，不包括边框和内边距。而设置 `box-sizing: border-box;` 后，元素的宽度和高度包括边框和内边距，这样可以避免一些意外的布局问题，特别是当我们设置 `width` 和 `height` 时。

```
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

## 2. **`margin` 和 `padding` 重置**

​	很多时候我们需要重置浏览器默认的 `margin` 和 `padding`，特别是对所有元素的外边距和内边距进行清理。这通常通过设置 `*` 或更具体的选择器来实现。

清除所有元素的 `margin` 和 `padding`：

```
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
}
```

## 3. **`body` 和 `html` 的默认样式**

确保页面的根元素和 `body` 元素有一致的样式设置，以避免浏览器差异。通常包括设置 `font-size`，`line-height`，以及防止页面内容溢出等。

```
html {
  font-size: 100%; /* 默认字体大小 */
  line-height: 1.5; /* 行高 */
}

body {
  font-family: Arial, sans-serif;
  line-height: 1.5;
  background-color: #fff;
}
```

## 4. **`a` 标签的样式重置**

浏览器默认为 `a` 标签设置了下划线和颜色，通常需要去除下划线，并根据设计设置颜色。

```
a {
  text-decoration: none;  /* 去掉下划线 */
  color: inherit;  /* 继承父元素的颜色 */
}
```

## 5. **`list-style` 重置**

浏览器默认为 `<ul>` 和 `<ol>` 列表添加了项目符号或数字，需要清除样式。

```
ul,
ol {
  list-style: none;
  padding: 0;
  margin: 0;
}
```

## 6. **`img` 和 `video` 标签的样式**

通常需要为 `img` 和 `video` 标签设置 `max-width: 100%`，以确保它们在父容器内自适应大小，避免溢出。

```
img,
video {
  max-width: 100%;
  height: auto;
}
```

## 7. **`fieldset` 和 `legend` 的样式**

对于表单元素 `<fieldset>` 和 `<legend>`，浏览器会默认应用一些样式，通常需要去掉这些样式，尤其是在没有特殊需求时。

```
fieldset {
  border: none;
  padding: 0;
}

legend {
  display: none;
}
```



# 
