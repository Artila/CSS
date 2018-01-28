## 浅析CSS里的 BFC 和 IFC

https://segmentfault.com/a/1190000012993668

## 前言

之前一直听到有人提到 CSS里的BFC，正巧在 IFE的练习里遇到了外边距折叠的问题，所以正好弄清楚BFC的机制。 (参考来源 见**文末的 Reference**)

## 一 什么是 BFC

和之前所有博文一样，还是先从**What**的角度开始介绍，由浅入深的理解BFC。
所谓的 Formatting context(格式化上下文), 它是 W3C CSS2.1 规范中的一个概念。

> 它是页面中的一块渲染区域，并且有一套渲染规则.
> 它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。

而 Block Formatting Contexts (BFC，块级格式化上下文)，就是 一个块级元素 的渲染显示规则。通俗一点讲，可以把 BFC 理解为一个封闭的大箱子，，容器里面的子元素不会影响到外面的元素，反之也如此。

BFC的布局规则如下：

1. 内部的盒子会在垂直方向，一个个地放置；
2. BFC是页面上的一个隔离的独立容器；
3. 属于**同一个BFC**的 两个**相邻Box**的 **上下margin**会发生重叠 ；
4. 计算BFC的高度时，浮动元素也参与计算
5. 每个元素的左边，与包含的盒子的左边相接触，即使存在浮动也是如此；
6. BFC的区域不会与float重叠；

那么如何触发 BFC呢？只要元素满足下面任一条件即可触发 BFC 特性：

- body 根元素；
- 浮动元素：float 不为none的属性值；
- 绝对定位元素：position (absolute、fixed)
- display为： inline-block、table-cells、flex
- overflow 除了visible以外的值 (hidden、auto、scroll)

## 二 BFC的特性及应用

接下来介绍BFC常见的特性和应用，这一部分 在解释原因时，会用到上文的布局规则 和 触发条件，所以需要注意一下。

### 1. 两个 相邻的普通流中的 块元素垂直方向上的 margin会折叠

```html
<head>
.p {  
  width:200px;  
  height:50px;  
  margin:50px 0;  
  background-color:red;  
}  
</head>

<body>
   <div class="p"></div>  
   <div class="p"></div>  
</body>
```

效果图是：
![img](https://segmentfault.com/img/remote/1460000012993673?w=203&h=147)

根据BFC规则的第3条：

> 盒子垂直方向的距离由margin决定，属于 同一个BFC的 + 两个相邻Box的 + 上下margin 会发生重叠。

上文的例子 之所以发生外边距折叠，是因为他们 同属于 body这个根元素， 所以我们需要让 它们 **不属于同一个BFC**，就能避免外边距折叠：

```html
<div class="p"></div>  

<div class="wrap">  
  <div class="p"></div>  
</div>  
```

```css
.wrap {  
  overflow:hidden;
}

.p {  
  width:200px;  
  height:50px;  
  margin:50px 0;  
  background-color:red;  
}
```

效果图是：
![img](https://segmentfault.com/img/remote/1460000012993674?w=194&h=181)

### 2. BFC可以包含浮动的元素（清除浮动）

正常情况下，浮动的元素会脱离普通文档流，所以下面的代码里：

```html
<div style="border: 1px solid #000;">
    <div style="width: 50px; height: 50px; background: #eee;
               float: left;">
    </div>
</div>
```

外层的div会无法包含 内部浮动的div，效果见下图：
![img](https://segmentfault.com/img/remote/1460000012993675?w=492&h=244)

但如果我们 触发外部容器的BFC，根据BFC规范中的第4条：**计算BFC的高度时，浮动元素也参与计算**，那么外部div容器就可以包裹着浮动元素，所以只要把代码修改如下：

```html
<div style="border: 1px solid #000;overflow: hidden">
    <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```

就可以完成以下效果：
![img](https://segmentfault.com/img/remote/1460000012993676?w=416&h=252)

### 3. BFC可以阻止元素被浮动元素覆盖

先看一个例子：

```html
<div class="aside"></div>  
<div class="main"></div>  

div {  
  width:300px;  
}  
.aside {  
  width: 100px;  
  height: 150px;  
  float: left;  
  background: black;  
}  
.main {  
  height:200px;  
  background-color:red;  
}  
```

效果图是：
![img](https://segmentfault.com/img/remote/1460000012993677?w=405&h=214)
之所以是这样，是因为上文的 规则5： **每个元素的左边，与包含的盒子的左边相接触，即使存在浮动也是如此；**

所以要想改变效果，使其互补干扰，就得利用规则6 ：BFC的区域不会与float重叠，
让 `<div class="main">` 也能触发BFC的性质，即：

```css
.main {  
  overflow:hidden;  
  height:200px;  
  background-color:red;  
}  
```

通过这种方法，就能 用来实现 两列的自适应布局。

## 三 简要介绍IFC

IFC布局规则：

> 1 框会从包含块的顶部开始，一个接一个地水平摆放。
>
> 2 摆放这些框时，它们在水平方向的 内外边距+边框 所占用的空间都会被考虑；
>  在垂直方向上，这些框可能会以不同形式来对齐：
>  水平的margin、padding、border有效，垂直无效。不能指定宽高;
>
> 3 行框的宽度是 由包含块和存在的浮动来决定;
>  行框的高度 由行高来决定

## 四 Refernce：

  1：[10 分钟理解 BFC 原理；](https://zhuanlan.zhihu.com/p/25321647)
  2：[理解BFC、IFC、GFC、FFC](http://blog.csdn.net/u011472830/article/details/73010596)