## 术语缩写
- `WWW` (Word Wide Web) 万维网
- `AJAX` (Asynchronous JavaScript and XML)
- `URI` (Uniform Resource Identifier) 统一资源标识符
- `URL` (Uniform Resource Locator) 统一资源定位符
- `HTML` (HyperText Markup Language) 超文本标记语言
- `MAC` (Media Access Control Address) 网卡固定地址
- `ARP` (Address Resolution Protocol) 解析地址的协议（根据通信方的IP地址反查出对应的MAC地址）
- `REST` (Representational State Transfer) 表征状态转移
- `SSL` (Secure Sockets Layer) 安全套接层
- `TLS` (Transport Layer Security) 传输层安全

### 应用层
- `HTTP` (HyperText Transfer Protocol) 超文本状态转移协议
- `FTP` (File Transfer Protocol) 文件传输协议
- `DNS` (Domain Name System) 域名系统

### 传输层
- `TCP` (Transmission Control Protocol) 传输控制协议
- `UDP` (User Data Protocol) 用户数据报协议

## 尺寸
- `clientWidth/clientHeight`：`padding` + `content`
- `clientTop/clientLeft`：`border`
- `offsetWidth/offsetHeight`：`padding` + `content` + `border`
- `offsetTop/offsetLeft`: 元素左上角距离最近定位元素的距离
- `scrollWidth/scrollHeight`: `client`系列 + 溢出尺寸
- `scrollLeft/scrollTop`: 元素滚动条位置，**可写**(即调整滚动条位置，整个网页的滚动条位置用`window.scrollTo(x, y)`)
> `getBoundingClientRect`获取元素位置宽高

## 编码与解码
`encodeURI`、`decodeURI`、`encodeURIComponent`、`decodeURIComponent`
``` javascript
encodeURI('http://www.baidu.com/My First')
encodeURIComponent('http://www.baidu.com/My First')
// http://www.baidu.com/My%20First
// http%3A%2F%2Fwww.baidu.com%2FMy%20First
decodeURI('http://www.baidu.com/My%20First')
decodeURIComponent('http%3A%2F%2Fwww.baidu.com%2FMy%20First')
```

## TCP🤝
### 建立，三次🤝
- C --(SYN数据包)--> S
- C <--(SYN/ACK数据包)-- S
- C --(ACK数据包)--> S
### 断开，四次🤝
- C <--(FIN)-- S
- C --(ACK)--> S
- C --(FIN)--> S
- C <--(ACK)--> S

## 疑问
- PUT DELETE方法为什么不常用，不带验证机制什么意思？