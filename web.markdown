### 尺寸
- `clientWidth/clientHeight`：`padding` + `content`
- `clientTop/clientLeft`：`border`
- `offsetWidth/offsetHeight`：`padding` + `content` + `border`
- `offsetTop/offsetLeft`: 元素左上角距离最近定位元素的距离
- `scrollWidth/scrollHeight`: `client`系列 + 溢出尺寸
- `scrollLeft/scrollTop`: 元素滚动条位置，**可写**(即调整滚动条位置，整个网页的滚动条位置用`window.scrollTo(x, y)`)

### 编码与解码
`encodeURI`、`decodeURI`、`encodeURIComponent`、`decodeURIComponent`
``` javascript
encodeURI('http://www.baidu.com/My First')
encodeURIComponent('http://www.baidu.com/My First')
// http://www.baidu.com/My%20First
// http%3A%2F%2Fwww.baidu.com%2FMy%20First
decodeURI('http://www.baidu.com/My%20First')
decodeURIComponent('http%3A%2F%2Fwww.baidu.com%2FMy%20First')
```