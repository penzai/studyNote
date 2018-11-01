## `JS`规范

### `bind`方法

- 语法`fun.bind(thisArg[, arg1[, arg2[, ...]]])`
- 此方法会产生一个**新方法**，所以原方法改变了,它依然存在
- `bind`传递的参数优先级比原函数高，且后来的参数是紧跟着`bind`参数的后面，而不是对位取代

### `class`

#### 基础知识

- 函数拥有`prototype`属性，而任意对象拥有原型`__proto__`。
- 所谓的原型链其实是指`__proto__`与`prototype`的从属关系
  - `对象实例.__proto__ === 构造函数.prototype`
  - `构造函数.prototype.__proto__ === 父构造函数.prototype`（如果父构造函数又继承  于其他函数，则关系类似，一直直到顶层构造函数）
  - `顶层构造函数.prototype.__proto__ === Object.prototype`
  - `Object.prototype.__proto__ === null`
  - 一层特殊的关系：`Object.__proto__ === Function.prototype`

![](img/7-11-1.png)
![http://www.mollypages.org/tutorials/js.mp](img/js-standard/jsobj_full.jpg)

#### `es5`写法

> 注意继承方法，即设置`prototype`

```javascript
// Car类 - 父类
function Car(name, color) {
  this.name = name
  this.color = color
}
Car.prototype.showName = function () {
  console.log(this.name)
}
Car.say = function () {
  console.log('I am car.')
}

// Audi - 子类
function Audi(name, color, price) {
  Car.call(this, name, color)
  this.price = price
}
// Polyfill
if (typeof Object.create !== 'function') {
  Object.create = function (proto, propertiesObject) {
    if (typeof proto !== 'object' && typeof proto !== 'function') {
      throw new TypeError('继承的必须为对象或者函数！')
    } else if (proto === null) {
      throw new Error('该浏览器不支持null属性的继承！')
    }

    if (typeof propertiesObject !== 'undefined') {
      throw new Error('该浏览器不支持此方法设置第二个参数！')
    }

    function F() {}
    F.prototype = proto

    retunr new F()
  }
}
// 推荐，es6式继承方法
// 等同于 Object.setPrototypeOf(Audi.prototype, Car.prototype)
Audi.prototype = Object.create(Car.prototype)
Audi.prototype.constructor = Audi
// 不建议使用 Audi.prototype = new Car()
// 不建议使用 Audi.prototype = Car.prototype

Audi.prototype.showPrice = function () {
  console.log(this.price)
}
```

#### `es6`写法

- 继承时注意实现`super`
- `static`为类方法

```javascript
class Car {
  constructor(name, color) {
    this.name = name
    this.color = color
  }
  showName() {
    console.log(this.name)
  }
  static say() {
    console.log('I am car.')
  }
}

class Audi extends Car {
  constructor(name, color, price) {
    super(name, color)
    this.price = price
  }
  showPrice() {
    console.log(this.price)
  }
}
```

#### 待解决

-  字面量创建对象与构造函数创建对象的区别
- new 的具体代码实现

### `Promise`

#### `all`

都成功才能拿进入结果函数，`arr`数组里面是`p1, p2`的返回值数组。

```
Promise
  .all([p1, p2])
  .then(function (arr) {
    console.log(arr)
  }, function (err) {
    console.log(err)
  })
```

#### `race`

谁最快返回谁

### `Generator`

中间能停的函数，注意`yield`的前后区别（分别是不同的作用域）。

- 本身返回的是一个`generator`对象
- 需要用`.next()`方法来调用进行“走”
- 返回值。在`yield`后面紧跟的对象，返回值为`.next()`的的返回值，为`{value, done}`
- 输入值为`yield`

```javascript
function* show() {
  alert(1)
  let a = yield 3
  alert(2)
  console.log(a) // a = 传入的参数
}
```

#### 利用`runner`函数进行带逻辑的异步请求

```javascript
function runner(_gen) {
  return new Promise((resolve, reject) => {
    var gen = _gen()

    _next()
    function _next(_last_res) {
      var res = gen.next(_last_res)

      if (!res.done) {
        var obj = res.value

        if (obj.then) {
          obj.then(
            res => {
              _next(res)
            },
            err => {
              reject(err)
            }
          )
        } else if (typeof obj == 'function') {
          if (obj.constructor.toString().startsWith('function GeneratorFunction()')) {
            runner(obj).then(res => _next(res), reject)
          } else {
            _next(obj())
          }
        } else {
          _next(obj)
        }
      } else {
        resolve(res.value)
      }
    }
  })
}

runner(function*() {
  let userData = yield $.ajax({ url: 'getUserData', dataType: 'json' })

  if (userData.type == 'VIP') {
    let items = yield $.ajax({ url: 'getVIPItems', dataType: 'json' })
  } else {
    let items = yield $.ajax({ url: 'getItems', dataType: 'json' })
  }

  //生成、...
})
```

#### 与`async/await`区别

- 写法一样，还不需要`runner`函数
- 还可以写成箭头函数。

### 正则表达式

#### 常用基础知识

- `.`匹配非换行符 (`s`模式可以让`.`匹配换行符)
- `/s`匹配空白符（换行、制表等）`[ \f\n\r\t\v]`

#### 贪婪模式

.\*代表匹配任意次的非换行符字符。它是一个贪婪匹配，非贪婪如下

- `*?` 重复任意次，但尽可能少重复
- `+?` 重复 1 次或更多次，但尽可能少重复
- `??` 重复 0 次或 1 次，但尽可能少重复
- `{n,m}?` 重复 n 到 m 次，但尽可能少重复
- `{n,}?` 重复 n 次以上，但尽可能少重复

#### 分组

匹配下面一个字符

- 在代码里是字符串模式
- 里面有双斜杠`//`

那么规则为

```
/(?<!\\)('|"|`).*?\/\/.*?(?<!\\)\1/mg
```

### 事件循环`event loop`

- 主进程（即整体代码）属于`macrotasks`（`tasks`）。待执行完毕才会去寻找`microtask` (主动使用`.click()`触发事件，不属于异步！此时并没结束主进程，所以如果有事件冒泡，这时会先冒完，而且此时`MutationObserver`处于`pending`状态，无法多次实现)
- `js`通过事件循环（`event loop`）机制来定期访问异步任务队列
- `macroTasks`的任务，一次循环只处理一次（比如嵌套`setTimeout`)，而`microtask`队列能处理多次（比如`Promise`的`then`回调）

![](./js-standard/event-loop.jpg)

**macro-task**

- 整体代码
- `setTimeout`
- `setInterval`
- `setImmediate` (存在于`node`, 在`v.9.11.1`环境下测试，慢于 `setTimeout(fn, 0)`)
- `requestAnamationFrame`
- `I/O`
- `UI渲染`

**microtask**

- `Promise`
- `process.nextTick`
- `Object.observe`
- `MutationObserver`

### `new`执行了什么

创建一个新对象 foo；

并将它的**proto**指向其构造函数的 prototype，foo.**proto** = Foo.prototype;

动态将 this 指向新对象，Foo.apply(foo，arguments);

执行函数体中的代码；

放回新对象 foo;

### `ajax`

#### 原生写法

```javascript
if (window.XMLHttpRequest) {　 // Mozilla, Safari...
  xhr = new XMLHttpRequest();
} else if (window.ActiveXObject) { // IE
  try {
    xhr = new ActiveXObject('Msxml2.XMLHTTP');
  } catch (e) {
    try {
      xhr = new ActiveXObject('Microsoft.XMLHTTP');
    } catch (e) { }
  }
}
if (xhr) {
  xhr.onreadystatechange = onReadyStateChange;
  xhr.open('get', 'http://localhost:3000/user', true);
  // 设置 Content-Type 为 application/x-www-form-urlencoded
  // 以表单的形式传递数据
  xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
  xhr.send('username=admin&password=root');
}

// onreadystatechange 方法
function onReadyStateChange() {
  // 该函数会被调用四次
  console.log(xhr.readyState);
  if (xhr.readyState === 4) {
    // everything is good, the response is received
    if (xhr.status === 200) {
      console.log(JSON.parse(xhr.responseText);
    } else {
      console.log('There was a problem with the request.');
    }
  } else {
    // still not ready
    console.log('still not ready...');
  }
}
```

#### `fetch`

基于 Promise 设计，基础用法如下。

```javascript
fetch(url, {
  method: 'post',
  body: JSON.stringify({
    a: 1,
    b: 2
  }),
  // body: 'a=1&b=2',
  headers: {
    'Content-Type': 'application/json'
    // 'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
  },
  // 是否带cookie
  // same-origin: 同源带 include: 始终带 omit: 不带
  credentials: 'omit' //默认
})
  .then(
    response => {
      // 在这里进行中间件操作，封装
      if (response.ok) {
        return response.json()
      } else {
        // throw new Error('fetch done. but something is wrong!')
        return Promise.reject({
          status: response.status,
          statusText: response.statusText
        })
      }
    },
    error => {
      // 请求本身没成功，比如url地址错误
      console.log('fetch is error', error) })
    }
  )
  .then(data => {
    // 处理最终结果
  })
  .catch(error => {
    // 中间件捕获的错误
  })
```

 有几个坑

- `cookie` 传递
- `404`、`503` 等错误不被认为是网络错误，是不会抛错的。在中间件里用 `reponse.ok` 与 `response.status` 来确定处理

### 对象循环
#### for in
含继承，不含symbol属性，可枚举属性
#### Object.keys()
不含继承，不含symbol属性，可枚举属性
#### Object.getOwnPropertyNames()
含继承，不含symbol属性，可枚举与不可枚举属性
#### Reflect.ownKeys()
以上所有

以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。
- 首先遍历所有数值键，按照数值升序排列。
- 其次遍历所有字符串键，按照加入时间升序排列。
- 最后遍历所有 Symbol 键，按照加入时间升序排列。

### 冒泡排序
#### 基础写法
``` javascript
const bubbleSortByASC = arr => {
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
    }
  }
}
```
#### 优化1：外层轮回次数优化
当一轮没进行任何交换时，已是有序状态，不再轮回排序
``` javascript
const bubbleSortByASC = arr => {
  for (let i = 0; i < arr.length; i++) {
    let isSorted = true
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
        isSorted = false
      }
    }
    if(isSorted) {
      break
    }
  }
}
```
#### 优化2：找出有序边界
每轮回一次，尽量缩小无序数组的范围
``` javascript
const bubbleSortByASC = arr => {
// 上次交换位置
let lastExchangeIndex = 0
// 无序数组边界，即在这个范围内进行两两比较
let sortBorder = arr.length - 1

for (let i = 0; i < arr.length; i++) {
  let isSorted = true
  for (let j = 0; j < sortBorder; j++) {
    if (arr[j] > arr[j + 1]) {
      [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
      // 设false，说明这一轮进行了交换，是无序的
      isSorted = false
      // 每次更新交换位置
      lastExchangeIndex = j
    }
  }
  // 这一轮的最后交换位置即为有序数组与无序数组的分界
  // lastExchangeIndex属于无序数组
  sortBorder = lastExchangeIndex
  // 这一轮没进行任何交换，即已是有序数组，跳出，不再轮回
  if(isSorted) {
    break
  }
}
}
```


#### 一直认为的错误版本（虽然也能排序）
``` javascript
const sort = arr => {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] < arr[j]) {
        [arr[i], arr[j]] = [arr[j], arr[i]]
      }
    }
  }
}
```