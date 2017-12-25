## CSS深入理解之overflow

以下内容主要来自[张鑫旭大神的讲解](https://www.imooc.com/learn/256)。

### 一、Overflow 基本属性

1. overflow 基本属性值

   - visible （默认）——当尺寸超出容器时，依然显示超出的部分，容器不变（IE6中的容器会被内容撑开）；
   - hidden——超出部分会被隐藏；
   - scroll——会出现两条滚动条（上下，左右）；
   - auto——超出的方向上出现滚动条；
   - inherit（不常用）

2. overflow-x 和 overflow-y (IE8+)

   - overflow x y 值相同，则等于`overflow: auto;`；
   - overflow x y 值不同，其中一个属性的值被赋予 visible，另外一个被赋予hidden/auto/scroll，则 visible 会被重置为auto

3. 宽度设定机制

   ```css
   在其他浏览器中只有y方向上出现滚动条；
   而IE7上x和y方向都出现了滚动条，这是因为content的宽度设为400px，而box的宽度也是400px,但是它里面包裹的不仅仅是content的内容，还有滚动条，所以box的宽度小于400px，从而出现了x方向上的滚动条。
   要想除去滚动条，把width: 100%;去掉就好。
   <div class="box">
   	<div class="content"></div>
   </div>
   .box {
     width: 400px;
     height: 100px;
     overflow: auto;
   }
   .content {
     width: 100%;
     height: 200px;
     background: #beceeb;
   }
   ```

4. 起作用的前提

   - 非`display: inline;`。
   - 对应方位的尺寸限制，例如有 width/height，max-width/max-height，absolute拉伸。（同样需要注意IE7的bug）
   - 对于单元格`td`等，还需要`table`为`table-layout: fixed;`才行。

5. `overflow: visible;`的妙用

   - IE7浏览器下，文字越多，按钮两侧 padding 留白就越大！
   - 同样内容在IE8下则留白很正常。
   - IE7中，给所有按钮添加`overflow: visible;`后会回复正常。

### 二、Overflow 与滚动条

### 三、Overflow 与块状格式上下文

### 四、Overflow 与 absolute 绝对定位

### 五、依赖 Overflow 的样式表现

### 六、Overflow 与锚点技术