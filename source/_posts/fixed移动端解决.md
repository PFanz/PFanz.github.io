---
title: fixed移动端解决
date: 2016-07-29
tags: [JavaScript, 移动Web]
categories: 
- JavaScript
comments: true
---

From [here](http://efe.baidu.com/blog/mobile-fixed-layout/)

### fixed移动端解决

主要是IOS上的。

	思路：滚动内容在标签中，固定标签的位置。

1. 将所有非fixed定位的，也就是需要滚动的元素用block标签包裹。
2. fixed定位的元素给fixed或者absolute都可以。
3. 包裹标签绝对定位在fixed和边界中间，`overflow-y: scroll`。
4. 标签内滚动不流畅，给包裹标签增加`-webkit-overflow-scrolling: touch;`

```html
<body class="layout-scroll-fixed">
    <!-- fixed定位的头部 -->
    <header>
        
    </header>
    
    <!-- 可以滚动的区域 -->
    <main>
        <div class="content">
        <!-- 内容在这里... -->
        </div>
    </main>
    
    <!-- fixed定位的底部 -->
    <footer>
        <input type="text" placeholder="Footer..."/>
        <button class="submit">提交</button>
    </footer>
</body>
```

```css
header, footer, main {
    display: block;
}

header {
    position: fixed;  /* fixed、absolute都可以 */
    height: 50px;
    left: 0;
    right: 0;
    top: 0;
}

footer {
    position: fixed;
    height: 34px;
    left: 0;
    right: 0;
    bottom: 0;
}

main {
    /* main绝对定位，进行内部滚动 */
    position: absolute;
    top: 50px;
    bottom: 34px;
    /* 使之可以滚动 */
    overflow-y: scroll;
    /* 增加该属性，可以增加弹性 */
    -webkit-overflow-scrolling: touch;
}

main .content {
    height: 2000px;
}
```

From [here](http://imweb.io/topic/577e64a47c99347163ec0b10)

### 图片高度占位
给图片提供一个容器，设置高度为0，根据图片比例使用padding-top设置百分值。
```css
.img-wrap{
    position: relative;
    height: 0;
    padding-top: 50%；// 图片宽度的一半
}
.img{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
```