# 一、实现点击某个元素跳转

```js
function headertop_down() {
    // 获取 #content 元素的距离顶部的距离
    var coverOffset = document.getElementById('content').offsetTop;

    // 使用 window.scrollTo 来实现平滑滚动
    window.scrollTo({
        top: coverOffset,  // 目标位置
        behavior: 'smooth' // 平滑滚动
    });
}

```

它会使页面平滑滚动到指定位置，这个指定位置就是获取到的id为`content`元素的距离页顶的距离