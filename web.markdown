## 术语缩写
- `WWW` (Word Wide Web) 万维网
- `AJAX` (Asynchronous JavaScript and XML) 异步JavaScript与XML技术
- `URI` (Uniform Resource Identifier) 统一资源标识符
- `URL` (Uniform Resource Locator) 统一资源定位符
- `HTML` (HyperText Markup Language) 超文本标记语言
- `HTTP` (HyperText Transfer Protocol) 超文本状态转移协议
- `FTP` (File Transfer Protocol) 文件传输协议
- `DNS` (Domain Name System) 域名系统
- `TCP` (Transmission Control Protocol) 传输控制协议
- `UDP` (User Data Protocol) 用户数据报协议
- `MAC` (Media Access Control Address) 网卡固定地址
- `ARP` (Address Resolution Protocol) 解析地址的协议（根据通信方的IP地址反查出对应的MAC地址）
- `REST` (Representational State Transfer) 表征状态转移
- `SSL` (Secure Sockets Layer) 安全套接层
- `TLS` (Transport Layer Security) 传输层安全
- `Dos` (Denial of Service) 拒绝服务攻击
- `CDN` (Content Delivery Network) 内容分发网络

## 容量
1字节（Byte）= 8比特（bit)

1KB = 1000Byte

1KiB = 1024Byte

## 渲染
- script会阻塞渲染
- link在没有script的情况下不会阻塞渲染，有script的情况下会阻塞
- 两种标签均可设置异步避免阻塞，script使用defer/sync，link使用`media="print"`。
> async加载后立即执行，没有顺序可言。script按顺序异步加载，所以比async慢。
## DOM
三种节点：
- document，最外层，html之上，只有一个唯一子元素html
- 元素节点
- 文本节点

## 尺寸
- `clientWidth/clientHeight`：`padding` + `content`
- `clientTop/clientLeft`：`border`
- `offsetWidth/offsetHeight`：`padding` + `content` + `border`
- `offsetTop/offsetLeft`: 元素左上角距离最近定位元素的距离
- `scrollWidth/scrollHeight`: `client`系列 + 溢出尺寸
- `scrollLeft/scrollTop`: 元素滚动条位置，**可写**(即调整滚动条位置，整个网页的滚动条位置用`window.scrollTo(x, y)`)
> `getBoundingClientRect`获取元素位置宽高

## 图片
### 优化手段
- webpack压缩，并开启渐进效果提升体验。
- 按需加载。使用IntersectionObserver可监听元素进入视野内，使用chrome特有的loading属性。
- 固定图片设置背景渐变色，用工具获取色值。
- retina屏幕，img标签使用srcset属性。
- webP。使用picture标签加载多种格式图片来兼容处理，.webp排第一位。也可以使用cdn判断。

### 懒加载图片
 实现原理：元素的offsetTop < 页面的scrollTop + 页面的clientHeight

 元素offsetTop的获取：
 ``` javascript
 function getH(el) {
   var h = 0
   while(el) {
     h += el.offsetTop
     el = el.offsetParent
   }
   return h
 }
 ```

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

## 跨域
三同条件：

1. 协议相同
2. 域名相同
> 完整域名，例如www.baidu.com与baidu.com不同！！！
3. 端口相同

### 解决办法
- JSONP
- CORS
- document.domain
- window.name
- window.postMessage

## 从URL输入开始
### dns解析
通过域名找寻ip地址
#### 顺序
1. 浏览器缓存
2. 系统缓存
3. host文件
4. LocalDNS服务器
  - a. 请求根域名服务器（RootDNS），得到主域名服务器（ gTLDServer）地址
  - b. 请求主域名服务器（ gTLDServer），如 .com、 .cn、. org等，得到域名提供商服务器地址（NameServer）
  - c. 请求域名提供商服务器地址（NameServer），如`xxx.com`，至此获得IP和TTL值
> dns缓存存在多级，例如浏览器缓存、系统缓存、路由器缓存、IPS服务器缓存、根域名服务器缓存、顶级域名服务器缓存、主域名服务器缓存。
#### 优化
- 减少 DNS的请求次数
- DNS预解析
  ``` html
  <meta http-equiv="x-dns-prefetch-control" content="on" />
  <link rel="dns-prefetch" href="//example.com">
  ```
- CDN，通过在现有的 Internet中增加一层新的 CACHE(缓存)层，将网站的内容发布到最接近用户的网络”边缘“的节点
- HTTPDNS，使用HTTP协议替代UDP协议，绕过LocalDNS，可以有效防止域名劫持
- DNS负载均衡（根据权重轮询返回不同的服务器地址）

## 疑问
- PUT DELETE方法为什么不常用，不带验证机制什么意思？
- CDN具体是怎么应用的

## 缓存
### 强缓存
服务端通过cache-control(1.1)和expires(1.0)设置，命中则不发起请求，直接调用磁盘缓存。优先级cache-control > expires。

### 协商缓存
服务端通过last-modified/if-modified-since组合或者etag/if-none-match组合来判断，前者是服务端设置，后者是客户端请求携带。优先级ETag > Last-Modified。

### 其他缓存
pragma(1.0)，只有一个唯一值no-cache。优先级pragma > cache-control。

### 用户行为
我们可以把刷新/访问界面的手段分成三类：

- 在URI输入栏中输入然后回车/通过书签访问。会通过Expires或者Cache-Control判断是否过期，未过期则不发送请求，使用缓存。
- F5/点击工具栏中的刷新按钮/右键菜单重新加载。始终会发送一个请求，并带上etag或者last-modified的值，以此来决定是否使用缓存。
- Ctl+F5。彻底拿一份新资源。

## 攻击
- XSS，动态解析html（从服务端数据或者从url上）造成加载其它不可控的脚本。
- SQL注入攻击，动态拼接SQL造成调用攻击者设置好的SQL。
- OS，例如发邮件调用系统级的shell时。
- HTTP首部注入，例如利用location字段进行注入攻击。
- CSRF，跨站点请求伪造。
- DDoS，拒绝服务攻击。