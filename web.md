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

## 事件模型
### DOM事件模型
xxx.onclick，发生在冒泡阶段
### DOM 2级模型
- 捕获，目标，冒泡三个阶段
- e.currentTarget指向注册事件的监DOM对象，e.target指向事件发生的DOM对象
- this指向指向注册事件的监听DOM对象

## 尺寸
- `clientWidth/clientHeight`：`padding` + `content`
- `clientTop/clientLeft`：`border`
- `offsetWidth/offsetHeight`：`padding` + `content` + `border`
- `offsetTop/offsetLeft`: 元素左上角距离最近定位元素的距离
- `scrollWidth/scrollHeight`: `client`系列 + 溢出尺寸
- `scrollLeft/scrollTop`: 元素滚动条位置，**可写**(即调整滚动条位置，整个网页的滚动条位置用`window.scrollTo(x, y)`)
> `getBoundingClientRect`获取元素位置宽高

## ajax
### get与post
本质上都是万维网中传输数据的一种协议，所有的区别都来自于浏览器或者规范的限制。
- 语义不同
- 浏览器限制`url`长度2k左右 -> get请求有长度限制
- 不同服务器对get中的request body处理方式不同，所以body里面的信息不一定会被识别 -> get请求使用url进行透明传参

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
- 在http/1.0中，不支持持久连接，所有连接串行。
- 在http/1.1中，支持持久连接，但一个TCP连接一个时刻只能处理一个http请求，不过多个http请求可以复用这次连接。
- 在http/2.0中，一个TCP就可以同时处理多个http请求，名为Multiplexing。
- 不同浏览器对TCP连接的数量限制不一样，chrome/6,safari/6,firefox/32。

### 建立，三次🤝
- C --(SYN数据包)--> S
- C <--(SYN/ACK数据包)-- S
- C --(ACK数据包)--> S
### 断开，四次🤝
- C <--(FIN)-- S
- C --(ACK)--> S
- C --(FIN)--> S
- C <--(ACK)-- S


## 跨域
三同条件：

1. 协议相同
2. 域名相同
> 完整域名，例如www.baidu.com与baidu.com不同！！！
3. 端口相同

### 解决办法
- JSONP
- CORS
- document.domain，父子iframe同时设置此属性，a.bbb.com与a2.bbb.com即可通信
- window.postMessage，监听message事件收取信息
- nginx反向代理

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
服务端通过cache-control(1.1)(max-age=毫秒)和expires(1.0)(Date类型值)设置，命中则不发起请求，直接调用磁盘缓存。优先级cache-control > expires。

### 协商缓存
服务端通过last-modified/if-modified-since(Date类型值)组合或者etag/if-none-match(资源码)组合来判断，前者是服务端设置，后者是客户端请求携带。优先级ETag > Last-Modified。

### 其他缓存
pragma(1.0)，只有一个唯一值no-cache。优先级pragma > cache-control。

### 用户行为
我们可以把刷新/访问界面的手段分成三类：

- 在URI输入栏中输入然后回车/通过书签访问/location.href。会通过Expires或者Cache-Control判断是否过期，未过期则不发送请求，使用缓存，返回200
- F5/点击工具栏中的刷新按钮/右键菜单重新加载。始终会发送一个请求，并带上etag或者last-modified的值，以此来决定是否使用缓存（命中就返回304）。
- Ctl+F5。彻底拿一份新资源。

## 攻击
- XSS，动态解析html（从服务端数据或者从url上）造成加载其它不可控的脚本。
- SQL注入攻击，动态拼接SQL造成调用攻击者设置好的SQL。
- OS，例如发邮件调用系统级的shell时。
- HTTP首部注入，例如利用location字段进行注入攻击。
- CSRF，跨站点请求伪造。
- DDoS，拒绝服务攻击。

## 常见项目优化
- 常用第三方库用cdn引入（公司专用最好），使用webpack的externals动态引入。
- gzip压缩。打包时生成.gz文件，方便nginx服务器直接返回文件，无需利用cpu计算。
- preload预加载首屏需要图片
- dns-prefetch预解析需要跨域的dns
- 首屏使用动画，提前渲染，减少用户的焦虑感
- moment.js改为使用day.js
- 图片懒加载。
 1. 在scroll事件里处理（根据getBoundingClientRect接口获取top与scrollTop比对），需要注意几点（事件函数节流，首次先运行一次，滚动完毕移除事件）
 2. 或者使用IntersectionObserver构造函数管擦判断是否加载图片
 3. 使用vue-lazyload插件
- 图片使用.webp格式，为了兼容不使用的环境，使用picture标签来设定一层层的兼容
- 减少DOM操作。避免获取视图信息（getBoundingClientRect,clientWidth,offsetWidth）,因为它会立即更新浏览器重排/重绘维护的队列。高频事件防抖节流。
- 打包优化，使用DllPlugin分离第三方类库，使用add-asset-html-webpack-plugin来注入到index.html中