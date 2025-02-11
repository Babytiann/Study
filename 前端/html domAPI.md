# 1.innerText与textContent的区别

​	**`innerText`**: 会解析 HTML 标签的实际样式，只返回用户在页面上实际可见的文本。

```
<div>
  <p>Visible</p>
  <p style="display: none;">Hidden</p>
</div>
<script>
  console.log(document.querySelector('div').innerText); // 输出: "Visible"
</script>
```

​	**`textContent`**: 返回的是所有子节点的纯文本，**包括隐藏的内容**，同样不包含任何 HTML 标签。

```
<div>
  <p>Visible</p>
  <p style="display: none;">Hidden</p>
</div>
<script>
  console.log(document.querySelector('div').textContent); // 输出: "VisibleHidden"
</script>
```

就是innerText返回的是这个元素渲染到DOM上的样子，但是textContent返回的是**文本内容**，有什么文本他就返回个什么