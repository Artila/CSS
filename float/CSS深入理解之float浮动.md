## CSS深入理解之float浮动

以下内容主要来自技术大牛张鑫旭的讲解。

### 1. Float 的历史

​浮动原本的作用——文字环绕，可以实现图文混排的效果。

### 2. Float的特性——包裹与破坏

#### 浮动的两个特性：

1、包裹：收缩，坚挺，隔绝，这个特性也就是BFC

（Block Formatting Context 块级格式化上下文，空间大小变成自身容器内容本身的空间大小(宽高收缩), 不受外部影响）

2、破坏：父级元素高度塌陷，自身位置浮动

#### 除了float以外, 其他具有包裹性 (BFC) 的属性

- display: inline-block/table-cell/...

- position: absolute/fixed/sticky

- overflow: hidden/scroll

#### 其他具有破坏性的属性(影响布局等)

- display: none
- position: absolute/fixed/sticky

#### 为什么要清除浮动？

解决父元素高度塌陷。

只用在浮动元素的父元素上。

### 3. 被误解的Float

浮动的破坏性只是单纯为了实现文字环绕效果而已！

——因此，父容器高度塌陷根本就不是bug，特性使然！

浮动使父元素高度塌陷是标准，当父元素高度塌陷后，置于父元素下的元素没有限制会在空间允许的条件下浮动上来，这就是文字环绕图片的原因；或者是图片位于文字之间，图片的浮动会使其突破父元素向左或右浮动后文字拼接呈环绕效果。

### 4. 清除浮动-究竟是清除浮动还是清除浮动带来的影响？ 

#### 清除浮动(带来的影响)

1. 在底部插入`clear:both`：与外界还有联系，例如会产生margin重叠的效果

   - HTML block水平元素放置于底部 `<div ...></div>`
   - CSS after 伪元素在底部生成 `.clearfix: after{}`

2. 父元素BFC(IE8+) 或 haslayout(IE6/IE7)：里面的声明不会对外界产生影响，但也有缺陷，无法使用所有浏览器

   - float: left/right
   - position: absolute/fixed
   - overflow: hidden/scroll (IE7+)
   - display: inline-block/table-cell (IE8+)
   - width/height/zoom:1/... (IE6/IE7)

3. 由于以上两个方法各有缺点所以：
   权衡后的策咯

   ```css
   .clearfix:after{
     content:''; display:block; height:0; overflow:hidden; clear:both;
   }(IE8+)
   .clearfix{*zoom:1;} (IE6/IE7)

   或 缩短名称
   .fix:after{}
   .fix{}
   ```

4. 更好的方法：

   ```css
   .clearfix:after{
     content:''; display:table; clear:both;
   }(IE8+)
   .clearfix{
     *zoom:1;
   } (IE6/IE7)
   ```

5. 切勿滥用

   `.clearfix`只应用在包含浮动子元素的『父级元素』上

### 5. Float的滥用-不在其职而谋其政 

#### 浮动的作用：实质上是实现文字环绕效果

1. 元素的块状化(`display: block;`)
2. 破坏性造成的紧密排列属性，去空白，去换行及去空格特性(使`display: inline-block`的元素之间的空隙排去，实际上在元素的末尾)

#### float 砌砖布局的缺点：

1. 容错性低，容易出现问题；
2. 这种布局需要元素固定尺寸，很难重复使用；
3. 在低版本的IE下会有许多问题

#### 总结：

float 本来设计就是用来实现环绕文字的效果,不应该滥用。

### 6. Float与流体布局 

#### 文字环绕变身——普通布局（左中右）：

```css
float: left;
float: right;
text-align: center;(中间是标题)
```

#### 文字环绕衍生——单侧固定

```css
/* 固定侧 */
width
float: left
/* 自适应侧 */
padding-left/margin-left: 固定侧的宽度
```

#### DOM位置与显示位置匹配的单侧固定布局

```css
/* 右浮动，改变DOM位置的流体布局写法 */
  /* 固定侧 */
  width
  float: right
  /* 自适应侧 */
  margin-right: 固定侧的宽度
/* 左浮动，不改变DOM位置的流体布局写法 */
  /* 父元素，大的整体 */
  width: 100%; float: left;
  /* 自适应侧 */
  padding-right/margin-right: 固定侧的宽度
  /* 固定侧 */
  width
  float: left;
  margin-left: -固定侧的宽度(这里是负值)
```

#### 高级进化——智能自适应尺寸

```css
/* 固定侧 */
float
/* 自适应侧 */
display: table-cell;     IE8+
*display: inline-block;  IE7
width: 2000px; 
*width: auto; 
```

### 7. Float与兼容性 

#### IE6浮动bug

1. float双倍边距bug
2. 跟随float元素3px bug
3. float元素后面的斜体文字会有下沉的bug

#### IE7浮动bug

1. 含clear的浮动元素包裹不正确的问题
2. 浮动元素倒数2个莫名垂直间距问题
3. 浮动元素最后一个字符重复问题
4. 浮动元素楼梯排列问题
5. 浮动元素和文本不在同一行的问题