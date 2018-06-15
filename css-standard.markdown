## `css`规范

### `transition`动画
#### 简单使用
写上你准备变化的属性。比如`width`，`height`等，然后再紧接着写上变化时间。
```
div {
	transition: top .3s, height .3s;
}
```

### 阴影
- 前两位为`x`，`y`坐标（第四象限为正）
- 第三位为`blur`的程度，单位为`px`
- 第四位为颜色，一般为`rgba(0, 0, 0, .1)`
```
div {
 box-shadow: 0 5px 10px rgba(0, 0, 0, .1);
}
```

### `white-space`空格处理
- `nowrap `强制同行显示

### 拖动
- 设置属性`darggable`为`true`来开启拖动功能
- 拖放块的`drop`和`dragover`事件需阻止默认行为

各事件的调用如下。

``` html
<div id="dragDIV"style="width: 200px;height:200px;background-color:aqua;" draggable="true"></div>
<div id="dropDIV" style="width: 600px;height:300px;background-color:rgb(221, 215, 215);"></div>

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
    e.currentTarget.innerHTML = "拖放在这里"
  }
  dropDIV_DOM.ondragover = e => {
    console.log('over dropDIV..')
    e.preventDefault()
  }
  dropDIV_DOM.ondragleave = e => {
    console.log('leave dropDIV..')
    e.currentTarget.innerHTML = ""
  }
  dropDIV_DOM.ondrop = e => {
    console.log('drop..')
    e.currentTarget.innerHTML = "成功拖放"
    e.preventDefault()
  }
</script>
```
#### `e.dataTransfer`属性

### `z-index`
在设置`position: absolute`时，解决引起的元素重叠顺序问题。

- 当与`float: left`的元素重叠时，`absolute`的元素在上层
- `z-index`指的是在一个堆叠上下文的层级，而且自身会创建一个新的上下文

### `css选择器`
#### 元素选择符
略。
#### 关系选择符
- `E + F`相邻选择符
- `E ~ F`兄弟选择符
> 区别在与，兄弟选择符只认兄弟，并不管你在不在我旁边
#### 属性选择符
推荐使用css3的属性选择符。即：
- `E[att *= "val"]`
- `E[att ^= "val"]`
- `E[att $= "val"]`

老旧的选择。
- `E[att ~= "val"]`把所有类用空格分割，包含`val`的类，所以此选择器不支持复合类
- `E[att |= "val"]`以`val`开头
	- 类只有一个且为`val`，`class="val"`
	- 类以`val-`开头
	- 支持复合类，比如`b a`匹配`b a`或者`b a-`
#### 伪类选择器
**非贪婪类**，即你给的元素E和条件不搭配，就不匹配。`
E:first-child
E:last-child
E:only-child
E:nth-child(n)
E:nth-last-child(n)`
**贪婪类**，总是想办法找到符合条件的那个元素E。`
E:first-of-type
E:last-of-type
E:only-of-type
E:nth-of-type(n)
E:nth-last-of-type(n)`
#### 权重
伪元素与伪类的区别在于，前者需要create一个“幽灵”元素,比如一个p段落中的第一行。

- 通配符：0
- 伪元素：1
- 元素选择器：1
- class选择器：10
- 伪类：10
- 属性选择器：10
- id选择器：100
- 行内样式：1000

### `box-shadow`
- 各参数代表：`x`轴距离 / `y`轴距 / `blur`值（即模糊的程度）/ `spread`值（模糊块伸展的程度）
- 加上`inset`即设置元素内的阴影

### `className`的相关操作
利用`element.classList`的相关`api`，如果没有就用`element.className.split(/\s+/)`分割成数组再进行操作。
- `add/remove(String[, String])` 增加/移除相应的元素，可多个
- `item(Number)`
- `toggle(String[, force])` 切换相应的`class`，第二个参数决定强制增加还是删除
- `contains(String)` 是否包含

### `flex`布局
#### `container`
- `flex-flow` (默认`row nowrap`)
- `justify-content`
- `align-items`
- `align-content`

#### `item`
- `order`
- `flex` [flex-grow, flex-shrink, flex-basis] (默认 `0 1 auto`)
