## `CSS`实例

### 小点子

- 有些虽然是白色背景的还是要设置颜色。（比如`z-index`覆盖的时候没颜色就看得见下面的）
- `main`容器为大外层，`inner`为内容的开始容器
- 元素直接的间隔可以用 `A元素 + 同类A元素`来设置
- 弹性布局时。`flex`元素新启一个类，使用时添加此类，然后设置`flex`属性时使用`X类 .flex { 设置属性 }`
- 无厘头的空格用压缩可以解决

### 自适应

- 小屏的自适应变化，加入动画更流畅
- 使用百分比布局时，当屏幕小到一定程度时，注意更改百分比为高百分比
- `rem` 布局设置 `html` 的 `font-size`
> 16 / 3.75 = 4.266667

```css
html {
  font-size: 4.266667vw;
}
```

### 一些初始化样式

- `html, body, ul`的`margin，padding`设为`0`

### css 文件

稀土的一个做法，一个文件分为三类，分别是 BASE、COMPONENT、LAYOUT。

### 清除浮动

```css
.clearfix:after {
  content: '';
  clear: both;
  display: block;
  visibility: hidden;
  line-height: 0;
}
```

### 一系列图片合集

??? 使用`inline-block`后图片并没有在一行，而是加上`white-space: nowrap`才行

```html
<div class="inner photos">
	<img src="" class="photo">
	<img src="" class="photo">
	<img src="" class="photo">
	<img src="" class="photo">
</div>
```

```css
.photos {
  overflow-x: scroll;
  overflow-y: hidden;

  height: 400px;

  white-space: nowrap;
}
.photos .photo {
  display: inline-block;

  max-width: initial;
  height: inherit;
}
.photos .photo + .photo {
  margin-left: 10px;
}
```

### 画常用图形

#### 三角形

不需要的边框设置透明

```css
border-right: 10px solid aqua;
border-left: 10px solid transparent;
border-top: 10px solid transparent;
border-bottom: 10px solid transparent;
```

#### 移动端上传图片那个加号方框

```css
a {
  position: relative;
  display: inline-block;
  padding: 30px 20px;
  color: #a7a7a7;
  border: 1px solid;
}
a:hover {
  color: #249ff1;
}
a:before,
a:after {
  content: '';
  display: block;
}
a:after {
  border-top: 2px solid;
  width: 20px;
}
a:before {
  position: absolute;
  top: 20px;
  left: 29px;
  border-left: 2px solid;
  height: 20px;
}
```

### 图片部分模糊

主要利用伪元素`::beforeafter`和`::after`，前者进行透明白色背景，后者进行图片背景并模糊。

> 如果你要在模糊背景上放元素，需设置，`position: relative;`。

```css
/* 核心代码1 - 前置透明 */
.blur::before {
  content: '';
  top: 0;
  left: 0;
  position: absolute;
  width: 100%;
  height: 100%;

  z-index: 10;
  opacity: 0.15;
  background-color: #fff;
}
/* 核心代码1 - 后置模糊 */
.blur::after {
  content: '';
  top: 0;
  left: 0;
  position: absolute;
  width: 100%;
  height: 100%;

  filter: blur(5px);

  background-image: url('1.jpg');
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
}
```

### 好看的一个渐变色

`radial-gradient(ellipse farthest-side at 100% 100%,#dbf6c8 20%,#1cafc6 50%,#012690 110%)`

### 文字处理一行并裁剪

```css
 {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

### 边框

使用伪类元素

```css
li::after {
  content: ' ';
  position: absolute;
  right: 0;
  top: 0;
  width: 1px;
  bottom: 0;
  border-right: 1px solid #cccccc;
  color: #cccccc;
  -webkit-transform-origin: 100% 0;
  transform-origin: 100% 0;
  -webkit-transform: scaleX(0.5);
  transform: scaleX(0.5);
}
li::before {
  content: ' ';
  position: absolute;
  width: 100%;
  height: 1px;
  bottom: 0;
  left: 0;
  border-bottom: 1px solid #cccccc;
  color: #cccccc;
  -webkit-transform-origin: 100% 0;
  transform-origin: 100% 0;
  -webkit-transform: scaleY(0.5);
  transform: scaleY(0.5);
}
```

### 弹窗

两层结构，一层负责内容（定位用`transform:translate(-50%,-50%)`实现）。一层负责阴影，调整四个方向为 0 来铺满整个屏幕。

### 背景图片的一些处理

- 充满屏幕，居中

```css
div {
  background-repeat: no-repeat;
  background-position: 50% 50%;
  background-size: cover;
}
```

- 伸缩满容器

```css
div {
  background-size: 100% 100%;
}
```

## 疑难杂症
### 两个span不居中
如下代码，正常居中。
``` html
<div>
  <span>123</span>
  <span>456</span>
</div>
```
当加入img，并设置inline-block居中时，后面的456不居中，此时需要设置img为`vertical-align: middle`
``` html
<div>
  <span>
    <img style="vertical-align: middle;" />
  </span>
  <span>456</span>
</div>
```

## div的伸展
div 默认情况由本内容撑开

设置 width:100% 会一直追溯到有宽度的父级

flex: 1会把所有子元素都撑开

