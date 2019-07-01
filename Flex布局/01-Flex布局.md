### 引言

Flex ，即 **Flex(ible) Layout Box Model** , 译为 **弹性盒式模型**。它能够更加高效方便控制元素 **对齐、排列**，更重要的是能够 **自动计算** 布局内元素的 **尺寸** ，无论这个元素的尺寸是 **固定的还是动态** 的。

### 常见布局模型

|      Layout Model       |
| :---------------------: |
|      block-layout       |
|      inline-layout      |
|      table-layout       |
|    positioned-layout    |
| **flexible-box-layout** |
|       grid-layout       |

从上至下的布局模型依次为：

* 块布局
* 行内布局
* 表格布局
* 基于定位的布局
* **弹性盒子布局**
* 网格布局

### Flexible Box Layout Model

![](E:\Notes\Flex布局\Flexible-Box-Layout-Mode.png)

* flex-container：父容器，也叫伸缩容器。
* flex-item：父容器内的元素。
* main axis：主轴。主轴的方向由属性 `flex-direction` 设置。
  * main start：主轴起始点。
  * main end：主轴终点。
  * main size：主轴大小。由 `flex-item` 的宽决定的。

* cross axis：交叉轴。
  * cross start：交叉轴起点。
  * cross end：交叉轴终点。

> 并非水平的一定是主轴，也并非纵向的一定是交叉轴。但是两个轴一定是相互垂直，形成直角的。

### Flex 容器属性

* **display**

  将布局变成伸缩容器：`display: flex;`。

  > 兼容多浏览器写法：display：-webkit-flex;

  `reset.css` 文件中的作用：

  > 1. 清除浏览器默认样式。
  > 2. box-sizing: border-box（padding 和 border 被包含在定义的 width 和 height 之内）。
  > 3. 文字的一些基本样式，如居左、居中、居右。

  默认的块级元素会占满整行空间。

* **flex-direction**

  此属性定义 **子元素** 在 **主轴** 的排列方向，它的取值如下：

  `row` ：默认值，**从左到右** 。

  `row-reverse` ：**从右到左** 。

  `column` ：**从上到下**  。

  `column-reverse` ：**从下到上** 。

* **flex-wrap**

  设置当 **子元素** 超过 **父容器宽度/高度** 时的排列方式。它的取值如下：

  `nowrap` ：默认值，**不换行**。子元素会被挤在父容器中，计算子元素设置固定宽度也会被压缩。

  `wrap` ：**换行**，从上至下正常换行。

  `wrap-reverse` ：**逆序换行**，从下往上逆序换行。

* ***flex-flow**

  它是一个复合属性，它是由 `flex-direction` 和 `flex-wrap` 两个属性组合而成的。

  > flex-flow：row-reverse wrap;

* **justify-content**

  此属性设置 **子元素** 在 **主轴** 上的对齐模式。它的取值如下：

  `flex-start` ：默认值。在 **主轴起点对齐**，类似于左对齐。

  `flex-end` :  在 **主轴终点对齐**，类似于右对齐。

  `center` : **居中排列**。

  `space-between` ：**两端对齐，中间剩余空间平分** 。

  `space-around` :  **子元素之间的空间是一致的，剩余空间分配给两端。**

  > space-around ：伸缩项目平均分，剩余空间两边碰。
  >
  > space-between : 伸缩项目两边碰，中间空间平均分。

* **align-content** 

  此属性控制容器内 **多行** 在 **交叉轴** 上的排列方式。它的取值与 `justify-content` 类似，如下：

  `flex-start` ：在交叉轴起点对齐，类似于顶部对齐。

  `flex-end` :  在交叉轴终点对齐，类似于底部对齐。

  `center` : **居中排列**。

  `stretch` ：默认值，**拉伸对齐** 。

  `space-between` ：**两端对齐，中间剩余空间平分** 。

  `space-around` :  **子元素之间的空间是一致的，剩余空间分配给两端。**

  > 需要注意的是：`align-content` 属性的设置只有在 `flex-wrap` 属性为 `wrap` 的情况才有效。

* **align-items**

  此属性设置 **子元素** 在 **交叉轴** 上的对齐方式。它的取值为：

  `flex-start` ：

  `flex-end` ：

  `center` ：**居中对齐**。

  `baseline` ：子元素在交叉轴上以 **内容的基线** 为准来对齐。

  `stretch` : 默认值，**拉伸对齐**。

  > 此属性是将子元素当成一个整体来进行对齐。

* **小技巧**

一个实现子元素水平居中和垂直居中的方法。

```html
display:flex;
justify-content:center;
align-items:center;
```

### Flex 项目属性

* **order**

  此属性用于控制 **子元素** 出现在 **父容器的顺序** 。它的取值如下：0（默认值）、任何整数、负值。值越低优先级越高。

* **flex-grow**

  此属性用于 **分配剩余空间**。它的取值如下：0（默认值），数字，不能是负数。

  > 注意：可分配的空间是除去内容之后所剩余的空间。

  空间分配有以下几种情况：

  1. 设置为同一数值，空间 AA 制度
  2. 设置为不同数值，数值大的占空间多

* **flex-shrink**

  此属性作用域 `flex-growth` 属性相反。当 **父容器** 空间不足以容纳 **子元素** 时，便会按照 `flex-growth` 减少相应子元素所占用的空间。它的取值：1（默认值），数字，不能是负数。

  它有以下几种情况：

  1. 默认情况下是1，所有子元素减少的控制均一致。
  2. 设置为0，则所占用的空间不会被减少。

* **flex-basis**

  此属性是 **计算前刨去的空间** 。它的值有 `auto` (默认值)、`width` 、0 。

  > 对 flex-basis 设置数值等同于直接设置 width，如 50%，5rem, 100px 等。

  **计算公式**

  **伸缩项目分配空间 = 伸缩容器的空间 - basis 设置的空间 - 其他子元素的 width (若无则为其他内容占用的空间)。**

  > 若某个子元素设置了 basis 的话，就等价于不考虑它的 width 了。

  它的情况如下：

  1. auto：默认值，以 `flex-item` 的内容的 width 来计算可分配的空间。
  2. 0：当子元素设置此属性之后，父容器计算可分配空间时，此元素当做是空，即不占用任何空间。

* ***flex**

  此属性是一个复合属性，它的组成规则依次是 **伸展、收缩、基准**。

  > *-grow		*-shrink		*-basis

  它的取值情况如下：`1 0 auto` ：默认值，none，0。

* **align-self**

  此属性是 **子元素** 设置自己在交叉轴上的对齐方式。它的取值如下：

  `flex-start`：**交叉轴起点对齐** 。

  `flex-end`：**交叉轴终点对齐** 。

  `center`：**居中对齐**

  `baseline`：**基线对齐**

  `stretch`：**拉伸对齐** 。

  `auto`：**默认值** 。

  > 需要注意的是：如果伸缩项目设置有 align-self 且不为 auto，那么会覆盖掉伸缩容器为其设置的对齐方式。