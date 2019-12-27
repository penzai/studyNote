## 大纲
- css选择器
- 盒模型
- 布局：浮动、position、display
- 响应式设计
- transform/transition/animation

## text-align和vertical-align
### text-align
作用于块级元素，影响里面的行内元素排列。

justify值只影响文字。

### vertical-align
作用于行内元素，影响自身相对于其他行内元素排列。

#### 基线baseline
位置：汉字、阿拉伯数字的最末端，英文字母四条基线的第**三**条基线
#### text-bottom
英文字母四条基线的第**四**条基线

## 拖动

- 设置属性darggable为true来开启拖动功能
- 拖放块的drop和dragover事件需阻止默认行为

各事件的调用如下。

```html
<div
  id="dragDIV"
  style="width: 200px;height:200px;background-color:aqua;"
  draggable="true"
></div>
<div
  id="dropDIV"
  style="width: 600px;height:300px;background-color:rgb(221, 215, 215);"
></div>

<script>
  const dropDIV_DOM = document.querySelector('#dropDIV')
  const dragDIV_DOM = document.querySelector('#dragDIV')

  // 拖动快
  dragDIV_DOM.ondragstart = e => {
    console.log('dragstart...')
  }
  dragDIV_DOM.ondrag = e => {
    console.log('draging...')
  }
  dragDIV_DOM.ondragend = e => {
    console.log('dragend...')
  }

  // 放置区域
  dropDIV_DOM.ondragenter = e => {
    console.log('enter dropDIV..')
    e.currentTarget.innerHTML = '拖放在这里'
  }
  dropDIV_DOM.ondragover = e => {
    console.log('over dropDIV..')
    e.preventDefault()
  }
  dropDIV_DOM.ondragleave = e => {
    console.log('leave dropDIV..')
    e.currentTarget.innerHTML = ''
  }
  dropDIV_DOM.ondrop = e => {
    console.log('drop..')
    e.currentTarget.innerHTML = '成功拖放'
    e.preventDefault()
  }
</script>
```

### `e.dataTransfer`属性

## 层级
默认后定义的元素比先定义的层级高，相对定位比默认定位的元素层级高。
### `z-index`

在设置`position: absolute`时，解决引起的元素重叠顺序问题。

- 当与`float: left`的元素重叠时，`absolute`的元素在上层
- `z-index`指的是在一个堆叠上下文的层级，而且自身会创建一个新的上下文



## `css选择器`

### 元素选择符

略。

### 关系选择符

- `E + F`相邻选择符
- `E ~ F`兄弟选择符
  > 区别在与，兄弟选择符只认兄弟，并不管你在不在我旁边

### 属性选择符

css3：

- `E[foo *= "bar"]`
- `E[foo ^= "bar"]`
- `E[foo $= "bar"]`

css2:

- `E[foo]`
- `E[foo = "bar"]`
- `E[foo ~= "bar"]`把所有类用空格分割，包含`val`的类，所以此选择器不支持复合类
- `E[att |= "en"]`
  - 类只有一个且为`en`，`class="en"` 
  - 类以`en-`开头

### 伪类选择器

#### 非贪婪类

即你给的元素 E 和条件不搭配，就不匹配。`

- E:first-child
- E:last-child
- E:only-child
- E:nth-child(n)
- E:nth-last-child(n)`

#### 贪婪类

总是想办法找到符合条件的那个元素 E。`

- E:first-of-type
- E:last-of-type
- E:only-of-type
- E:nth-of-type(n)
- E:nth-last-of-type(n)`

## 权重

伪元素与伪类的区别在于，前者需要 create 一个“幽灵”元素,比如一个 p 段落中的第一行。

- 通配符：0
- 伪元素：1
- 元素选择器：1
- class 选择器：10
- 伪类：10
- 属性选择器：10
- id 选择器：100
- 行内样式：1000

> 同权重的时候，按书写样式的先后顺序，而不是class的使用顺序。

## `box-shadow`

- 各参数代表：`x`轴距离 / `y`轴距 / `blur`值（即模糊的程度）/ `spread`值（模糊块伸展的程度）
- 加上`inset`即设置元素内的阴影
```
div {
 box-shadow: 0 5px 10px rgba(0, 0, 0, .1);
}
```

## background
常用简写形式：
``` css
background: url(图片地址) position属性/size属性 重复属性
```
### background-position
- 百分比（相对于容器的宽高）
- 具体px
- 关键字：left、right、top、bottom、center
### background-size
- 百分比（相对于容器的宽高）
- 具体px
- 关键字：auto、contain(最大限度看见全貌)、cover(同比例充满容器)
### background-repeat
可以分别指定是在x轴重复还是y轴重复

## `className`的相关操作

利用`element.classList`的相关`api`，如果没有就用`element.className.split(/\s+/)`分割成数组再进行操作。

- `add/remove(String[, String])` 增加/移除相应的元素，可多个
- `item(Number)`
- `toggle(String[, force])` 切换相应的`class`，第二个参数决定强制增加还是删除
- `contains(String)` 是否包含

## 负边距
在流（正常文档流，浮动流）中，类似于相对定位的偏移，区别是前者会丢失流空间，被后来的元素乘虚而入，而后者会保留原来的流空间。

> ，会增加其原来的宽度。在一些容器中，可利用此特性，临时增加容器的宽度。

> 可以
### 应用
1. 对`width: auto`元素，临时增加宽度。
2. 使用`padding-bottom`和`margin-bottom`很大数值相互抵消来实现多列等高布局，但是缺点很多。
3. 圣杯双飞翼布局。middle/left/right设置`float: left`，然后使用负边距让3个元素集中在同一行，最后增加容器padding并使用相对定位再次调整位置。
3. 垂直居中已知宽高的元素。

## flex

### container

- `flex-flow`
  - `flex-direction`（默认row）
  - `flex-wrap` (默认nowrap)
- `justify-content`（默认flex-start）
- `align-content`（默认stretch，高度尽可能撑大，当容器只有一行时，无效  ）
- `align-items`（默认stretch，高度尽可能撑大 ）

### item
float、clear、vertical-align不能影响item
- `order`（默认0）
- `flex` [flex-grow, flex-shrink, flex-basis]（默认 `0 1 auto`, 缺省值分别是1，1，0%）
> 注意`1 1 auto`与`1 1 0%`的区别，前者是分发的是额外空间，后者分发的是整个空间。
- `align-self` (单独的align-items设置)

## float
存在是为了使文字良好的环绕图片。
> 即当浮动元素遮住非浮动元素时，非浮动元素的文字会从**不遮盖**的地方开始，但是布局还是以原始地方开始计算。

当用于排版布局时，子元素浮动后会影响父元素高度，为了使父元素依然完美包裹浮动子元素，需要在最后一个浮动元素处清除浮动`clear: both`。

### 清除浮动
- 触发BFC，例如给容器添加`overflow: hidden`
- 容器末尾增加一个元素
  ``` css
  .clearfix {
    content: '';
    display: table;
    clear: both;
  }
  .clearfix {
    content: '.';
    display: block;
    visibility: hidden;
    height: 0;
  }
  ```

## position
- 只设置absolute值时，默认位置在原来正常文档流的位置。
- 没有祖先为relative元素时，会参照浏览器首屏区域。
- 参照的边界从border结束开始（不包括border本身）。

## 响应式设计
- fluid grids
- flexible images，设置max-width: 100%
- media queries
- mobile first

## 文本
### white-space
决定html中空格的处理方式。
- normal，CJK和Non-CJK单词之间自动换行，空格合并为1个，换行符会被当作空白符来处理
- nowrap，不换行
- pre，保留所有特征
- pre-wrap，保留所有特征，但是会自动换行
- pre-line，在默认的基础上增加换行符生效特征

### word-break
决定单词**内部**断行方式。
- normal，默认，CJK自动换行，Non-CJK单词内部不断行（但是单词之间会换行）。

由上可知，两类字符的断行方式默认不一样，那么下面两种就是为了设置成统一的。
- break-all，都断行
- keep-all，都不断行（标点符号处，依然会进行断行）

> 细想上面的属性，其实处理都不太好，假如我们只想让比宽度还宽的那个单词换行，我们可以使用break-word属性，但是兼容性不太好。不过可以通过设置`word-wrap: break-word;`来实现，css3里提议为overflow-wrap名称。
