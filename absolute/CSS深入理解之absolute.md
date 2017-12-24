## CSS深入理解之absolute

以下内容主要来自[张鑫旭大神的讲解](https://www.imooc.com/learn/192)。

元素设置absolute之后，

1. 会脱离文档流形成“悬空”效果
2. 设置absolute的元素仍然可以有margin

想象一下，这时候元素就相当于一个小飞碟，“悬浮”在文档流所在平面的上方，margin就相当于小飞碟四周用来控制方向的喷射器。

小飞碟(元素)想往下移动，就开动上方的喷射器(设置margin-top)，往上喷气(margin-top为正值)，小飞碟(元素)就往下走了，这个道理大家都懂的。

想想宇航员太空行走的样子吧！噗噗噗，那个小喷头，就相当于在改变宇航员自身的"margin"值。

### 1. absolute 和 float——兄弟关系

#### 相同的特性表现：

- 包裹性wrap（父元素absolute，则包含内容，不占据整行）
- 破坏性broken（子元素absolute，父元素出现塌陷现象）

页面布局可相互替代

```css
float:left; 
position:absolute;
```

### 2. absolute 和 relative

relative 和 absolute 不是兄弟关系

大部分人都是两者结合使用，父元素设置relative，子元素设置absolute相对于父元素定位。

clear 用来限制 float

relative 用来限制 absolute

独立的 absolute 可以摆脱 overflow 的限制，无论是滚动还是隐藏。

### 3. 无依赖的 absolute 定位

#### 一、释义

　　无依赖绝对定位：不受relative限制的absolute定位,行为表现上是不使用top/right/bottom/left任何一个属性或使用auto做为值。

#### 二、无依赖绝对定位的特性：

  1.  去浮动（可作为一种清除浮动的方法）

     float与absolute不能同时存在。

     因为absolute具有去浮动的特性，如果先使用了float，然后再使用absolute，结果就是使float失效，和单独使用absolute的效果一样。

  2.  位置跟随（脱离文档流后仍处于原位置）

     - absolute 的元素如果是 block ，设置成absolute后，位置不变，依然换行显示
     - absolute 的元素是inline/ inline-block 位置不变，依然 inline 显示
     - chrome浏览器的特殊性：chrome浏览器对于absolute的元素，页面渲染完成后，再次修改display 属性，浏览器是不会再进行渲染的，所以这种情况下，先默认给图片设置`display：block`。

  3.  超越overflow（不受overflow的影响）

#### 三、注意：

　　IE7 的特殊性：ie7 会把所有绝对定位的元素 的display inline-block 化，解决办法就是在绝对定位的元素上加一个无意义的div父元素

### 4. absolute 的强大应用

#### 一、图片图标绝对定位覆盖

对需要绝对定位实现左上角的图标时 (无依赖的绝对定位更好)

1. 传统的方法使用：对父元素使用`position: relative;`在对上角图标使用 absolute 来布局


2. 左上角使用 absolute ，再加上 margin 来定位，使得父元素改变也不会涉及图标，自适应更强

无需在父元素设置position：relative，利用了 absolute 的跟随性和不占空间的特点，设置margin属性对其微调。

只设置 absolute  之后（无依赖的绝对定位），有个非常大的特性——“跟随性”，当它是普通元素时，呆在什么位置，绝对定位后，还呆在什么位置（无依赖使用的是margin定位）。

注意：

注释节点<!-- -->可以用来消除两个标签之间的空格，保证两个元素之间完美的贴合。(同时保持代码可读性)

#### 二、下拉框定位最佳实践

<ul>在<input>前，<ul>设置absolute，<input>坍缩到<ul>的位置。再用 margin 定位<ul>。

需要absolute的元素在前面，绝对定位后，位置跟随，可以很方便的定位。

#### 三、居中以及边缘定位

需要居中的元素前加上`&nbsp;`，然后让父元素`text-align: center;`，这时空格会居中（后面的元素设置 `absolute` 后已经脱离文档流了），而跟随在空格后的元素也会产生“跟随性”，出现在空格的旁边，再设置`margin-left: -自身宽度的一半;`，这就实现了居中效果。

```css
/* 父元素 */
text-align: center;
/* 子元素：&nbsp; + 居中元素*/
/* 居中元素 */
position: absolute;
margin-left: -自身宽度的一半(这是负值);
```

同样，需要边缘的元素，让父元素`text-align: right/left;`，其他结构相似。

#### 四、文本图标对齐与定位

1. 处理文字前的星号

   将星号绝对定位后其不占据任何空间，方便后面的文字左对齐

   ```css
   <label class="regist-label">
     <span class="regist-star">*</span>登录密码
   </label>
   .regist-star { 
     position: absolute; 
     margin-left: -1em; 
     font-family: simsun; 
     color: #f30; 
   }
   ```

2. 图标与文字垂直对齐

   对图标设置绝对定位，并设置margin为负值

   ```css
   <span class="regist-remark regist-warn">
   	<i class="icon-warn"></i>邮箱格式不准确（演示）
   </span>
   .regist-warn { 
     padding-left: 20px; 
     color: #be3948; 
   }
   .regist-warn > .icon-warn { 
     position: absolute; 
     margin-left: -20px; 
   }
   ```

3. 处理文字溢出

   利用absolute绝对定位使span不占据任何空间，从而使其不换行（注：<span>必须紧跟前一个标签，不能有空格）

   ```css
   <span class="regist-remark">请输入6-16位密码，区分大小写，不能使用空格</span>
   .regist-remark { 
     position: absolute; 
     line-height: 21px; 
     padding-top: 9px; 
     color: #666; 
   }
   ```

### 5. absolute 和层级

1. 动画尽量作用在绝对定位元素上。（因为可以不影响其它元素）
2. 垂直空间的层级，遵循后来居上的原则。
3. 注意 z-index 的误区，对 z-index 无依赖：
   1. 如果只有一个绝对定位元素，自然不需要z-index,自动覆盖普通元素；
   2. 如果两个绝对定位，控制DOM流的前后顺序达到需要的覆盖效果，依然无 z-index；
   3. 如果多个绝对定位交错，非常非常少见，`z-index: 1;`控制；
   4. 如果非弹窗类的绝对定位元素 z-index > 2，必定 z-index 冗余，请优化！

### 6. absolute 和 top/right/bottom/left 

absolute 的移动：

​	可以设置 top/right/bottom/left

​	一般是相对 body 而言

absolute 的限制：

​	会被外层（父元素以上）的 relative，absolute，fixed，sticky 限制

​	这时的移动是相对于拥有这些属性的元素而言。

### 7. absolute 的 top/right/bottom/left 和 width/height 

1. 相互替代性

   绝对定位方向是对立的（如：left vs right，top vs bottom ）的时候，结果不是元素的移动，而是元素的扩大拉伸。

   所以，很多情况下，absolute 的对立拉伸和 width/height 是可以相互替代的。

   ```css
   position: absolute; top: 0; left: 0; width: 50%;
   /* 等同于： */
   position: absolute; top: 0; left: 0; right: 50%;
   ```

   **注意：absolute 的对立拉伸 IE7+支持！**

2. 差异所在——拉伸更强大

   ```css
   /* 实现一个距离右侧200像素的全屏自适应的容器层，你会怎么实现？ */
   /* 拉伸更直接 */
   position: absolute; left: 0; right: 200px;
   /* width 需要 CSS3 calc 的计算 */
   position: absolute; left: 0; width: calc(100% - 200px);
   ```

3. 相互支持性

   - 容器无需固定 width/height 值，内部元素亦可拉伸；
   - 容器拉伸，内部元素支持百分比 width/height 值。
     - 通常情况下，元素百分比 height 要想其作用，需要父级容器的 height 值不是 auto。
     - 绝对定位拉伸下，即使父级容器的 height 值是 auto，只要容器绝对定位拉伸形成，百分比高度值也是支持的。

4. 相互合作性

   - absolute 拉伸和 width/height 同时存在时， width/height 设置的尺寸会**大于** top/right/bottom/left 拉伸的尺寸。

   ```css
   position: absolute; top: 0; left: 0; right: 0; width: 50%;
   /* 实际宽度是50%，而不是100% */
   ```

   - 当 尺寸限制，absolute的拉伸 和 `margin: auto;` 同时出现时，就会有绝对定位元素的绝对居中效果。

     注意：IE8+支持！

### 8. absolute 和移动 web 布局 

1. body 降级，子元素升级

   ```css
   /* 升级的子 div （假设类名为 page ）为满屏： */
   .page {
     position: absolute;
     top: 0;
     right: 0;
     bottom: 0;
     left: 0;
   }
   /* 绝对定位受限于父级，因此，page 想要拉伸，需要：*/
   html, body {
     height: 100%;
   }
   /* body默认高度为0 */
   ```

2. 各模块——头尾部，侧边栏（PC端）各居其位

   ```css
   header, footer {
     position: absolute;
     left: 0;
     right: 0;
   }
   header {
     top: 0;
     height: 48px;
   }
   footer {
     bottom: 0;
     height: 52px;
   }

   aside {
     position: absolute;
     top: 48px;
     bottom: 52px;
     left: 0;
     width: 250px;
   }
   ```

3. 在第2种情况下，更进一步，内容区域想象成body

  ```css
  .content {
    position: absolute;
    top: 48px;
    bottom: 52px;
    left: 250px;
    overflow: auto; /* 内容太多，滚动条滚动 */
  }
  /* 此时的头部尾部以及侧边栏都是 fixed 效果，不跟随滚动，避免了移动端 position: fixed 实现的诸多问题。*/
  ```

4. ​ 全屏覆盖与 page 平级

   ```css
   .overlay {
     position: absolute;
     top: 0; right: 0; bottom: 0; left: 0;
     background-color: rgba(0, 0, 0, .5);
     z-index: 9;
   }
   <div class="page"></div>
   <div class="overlay"></div>
   ```
