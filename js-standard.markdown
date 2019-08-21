## 类型判断
- typeof操作符： 简单判断类型，无法区分例如数组与对象、null与对象。
- instanceof操作符/isPrototypeOf()： 测试实例与原型链中**出现过**的构造函数。
> 当构造函数返回的是引用类型时，无法判断。
- Object.prototype.toString.call(obj)
- obj.hasOwnProperty(key) 判断obj实例有没有key属性

## `bind`方法

- 语法`fun.bind(thisArg[, arg1[, arg2[, ...]]])`
- 此方法会产生一个**新方法**，所以原方法改变了,它依然存在
- `bind`传递的参数优先级比原函数高，且后来的参数是紧跟着`bind`参数的后面，而不是对位取代

## Object

### `Object.is`

与`===`区别：

- `+0` 与 `-0`，`===`返回 true，Object.is 返回 false
- `NaN`与`NaN`，`===`返回 false，Object.is 返回 true

### `Object.defineProperty`

新增或者修改一个对象的一个属性，并返回这个对象。语法为`Object.defineProperty(obj, prop, descriptor)`。

其中descriptor分为如下：
- 公共描述符
  - `configurable`(false)能否配置以及删除
  - `enumerable`(false) 能否枚举
- 择其一
  - data scriptors
    - `value`(undefined)
    - `writable`(false) 能否被赋值运算符改变
  - accessor descriptors
    - `get`(undefined)
    - `set`(undefined)

### 对象循环

#### for in

含继承，不含 symbol 属性，可枚举属性

#### Object.keys()

不含继承，不含 symbol 属性，可枚举属性

#### Object.getOwnPropertyNames()

含继承，不含 symbol 属性， 可枚举与不可枚举属性

#### Reflect.ownKeys()

以上  所有

以上的几种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

- 首先遍历所有数值键，按照数值升序排列。
- 其次遍历所有字符串键，按照加入时间升序排列。
- 最后遍历所有 Symbol 键，按照加入时间升序排列。

### 继承
最好的继承的特点：
- 能给超类构造函数传参
- 拥有共享方法
- constructor能正确指向
#### 原型链继承
``` javascript
function SubType() {}
SubType.prototype = new SuperType()
```
#### 借用构造函数继承（constructor stealing）
``` javascript
function SubType() {
  SuperType.call(this)
}
```
#### 组合继承（conbination inheritance）
结合原型链继承与借用构造函数继承。
#### 原型式继承（prototypal inheritance）
即es5的Object.create()方法。需要一个基础对象，来创建另一个类似的对象。
``` javascript
function object(o) {
  function F() {}
  F.prototype = o
  return new F()
}
```
#### 寄生式继承（parasitic inheritance）
创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象。
``` javascript
function createAnother(original) {
  var clone = object(original)
  clone.sayHi = function() {
    alert('hi')
  }
  return clone
}
```
#### 寄生组合式继承
通过借用构造函数来继承属性，使用寄生式继承来继承超类的原型。
``` javascript
SubType.prototype = object(SuperType.prototype);
SubType.prototype.constructor = SubType
```

### 原型链关系表

- 函数拥有`prototype`属性，而任意对象拥有原型`__proto__`。
- 所谓的原型链其实是指`__proto__`与`prototype`的从属关系
  - `对象实例.__proto__ === 构造函数.prototype`
  - `构造函数.prototype.__proto__ === 父构造函数.prototype`（如果父构造函数又继承  于其他函数，则关系类似，一直直到顶层构造函数）
  - `顶层构造函数.prototype.__proto__ === Object.prototype`
  - `Object.prototype.__proto__ === null`
  - 一层特殊的关系：`Object.__proto__ === Function.prototype`

![http://www.mollypages.org/tutorials/js.mp](img/js-standard/jsobj_full.jpg)

### new

1. 创建空对象
2. 链接原型链
3. 执行构造函数
4. 根据步骤 3 的结果类型返回不同的值

```javascript
var _new = function() {
  var Constructor = [].shift.call(arguments)
  if (typeof Constructor !== 'function') {
    throw new Error('请传入正确的构造函数')
  }
  var instance = Object.create(Constructor.prototype)
  var ret = Constructor.apply(instance, arguments)
  return ret instanceof Object ? ret : instance
}
```

## `Promise`

### `all`

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

### `race`

谁最快返回谁

## `Generator`
- 一个状态机，内部封装了很多种状态。
- 此函数会返回一个遍历器对象



### 利用`runner`函数进行带逻辑的异步请求

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
          if (
            obj.constructor
              .toString()
              .startsWith('function GeneratorFunction()')
          ) {
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

### 与`async/await`区别

- 写法一样，还不需要`runner`函数
- 还可以写成箭头函数。

## `Iterator`
默认的Iterator接口部署在数据结构的Symbol.iterator属性上。

## 正则表达式
正则要么匹配字符，要么匹配位置。

### 提示要点
- 字符集`[]`中的特殊字符无需转义符`/`
- `\1` `\2`代表括号匹配结果，宿主环境不一定是这个，例如js环境中是`$1`

### 位置
`\b` `\B` `^` `$` `(?=p)` 都表示位置，位置都不参与实际匹配结果。

#### `(?=p)`：
表示p前面的位置。反义是`(?!p)`。反向是`(?<=p)`，表示p后面的位置。反义是`(?<!p)`。

p本身并不是匹配结果，它只是匹配的条件。例如你要匹配的字符是xxx。只能使用如下几种组合方式：
`xxx(?=p)` positive lookahead
`xxx(?!p)` negative lookahead
`(?<=p)xxx` positive lookbehind
`(?<!p)xxx` negative lookbehind

### 贪婪模式

默认匹配是贪婪匹配，可以在量词`*`或者`{m,n}`后面加上`?`符号，使其惰性匹配。

### 分组

匹配下面一个字符

- 在代码里是字符串模式
- 里面有双斜杠`//`

那么规则为

```
/(?<!\\)('|"|`).*?\/\/.*?(?<!\\)\1/mg
```

### 字符集
- 



### 计算匹配结果

match、exec、test 方法

## 事件
### 事件流
捕获 -> 本体 -> 冒泡，其中`dom.onclick = function () {}`为冒泡阶段，`addEventListener`根据useCapture参数来定（默认false，即冒泡阶段），但是本体不区分捕获或者冒泡，按照事件定义顺序执行。
### 事件循环event loop
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

### 冒泡排序

#### 基础写法

```javascript
const bubbleSortByASC = arr => {
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
    }
  }
}
```

#### 优化 1：外层轮回次数优化

当一轮没进行任何交换时，已是有序状态，不再轮回排序

```javascript
const bubbleSortByASC = arr => {
  for (let i = 0; i < arr.length; i++) {
    let isSorted = true
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        ;[arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
        isSorted = false
      }
    }
    if (isSorted) {
      break
    }
  }
}
```

#### 优化 2：找出有序边界

每轮回一次，尽量缩小无序数组的范围

```javascript
const bubbleSortByASC = arr => {
  // 上次交换位置
  let lastExchangeIndex = 0
  // 无序数组边界，即在这个范围内进行两两比较
  let sortBorder = arr.length - 1

  for (let i = 0; i < arr.length; i++) {
    let isSorted = true
    for (let j = 0; j < sortBorder; j++) {
      if (arr[j] > arr[j + 1]) {
        ;[arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
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
    if (isSorted) {
      break
    }
  }
}
```

#### 一直认为的错误版本（虽然也能排序）

```javascript
const sort = arr => {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] < arr[j]) {
        ;[arr[i], arr[j]] = [arr[j], arr[i]]
      }
    }
  }
}
```

### 连等赋值

一道”面试“题

```javascript
var a = { n: 1 }
a.x = a = { n: 2 }
console.log(a.x) // ???
```

- 连等赋值会提前保存对象的引用，复制时右边取新的，左边取提前保存的。
- A=B=C 的调用过程其实是 B=C，再执行 A=B，注意 C 值只调用一次（可用 getter 进行测试）

所以上述答案为`undefined`

## Array

### Array.prototype.map

内部原理：

- 提前预知并确定循环长度
- 执行时
  - 元素为 empty，不进行方法调用，并在返回值中设为 empty
  - 元素不为 empty，获取此刻原数组的元素值，执行方法调用

> 使用 map 方法处理数组时，数组元素的范围是在 callback 方法第一次调用之前就已经确定了。在 map 方法执行的过程中：原数组中新增加的元素将不会被 callback 访问到；若已经存在的元素被改变或删除了，则它们的传递到 callback 的值是 map 方法遍历到它们的那一时刻的值；而被删除的元素将不会被访问到。 —— MDN https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map

### Array.prototype.reduce

传入函数的 a，b 值并不是数组的每一项，准确的讲，分别为累加器与当前项，有 2 种情况：

- 设定了初始值，第 1 次循环，累加器值为初始值
- 未设定初始值，第 1 次循环，累加器值为第 1 项，且从数组第 2 项开始循环

综上，应避免以下错误：

```javascript
// ❌ 报错
const arr = [] //一定要确定数组不为空
arr.reduce((a, b) => a + b)

// ❌ 结果为NaN
const arr = [{ price: 1 }, { price: 2 }]
arr.reduce((a, b) => a.price + b.price, 0)
```

### empty

使用`delete arr[i]`，原数组的下标并不会改变，而是在原来的下边出，把值设为 empty，此值有一定的特殊性。

```javascript
const arr = [1, 2, 3, 4, 5]
arr[1] = undefined
delete arr[3]
console.log('changed : ', arr)

// for (不跳过任意)
for (let i = 0; i < arr.length; i++) {
  console.log(`for ${arr[i]}`)
}

// map（跳过empty，返回结果包含empty）
const mapedArr = arr.map(v => {
  console.log(`map ${v}`)
  return 'hello'
})
console.log(`mapedArr ${mapedArr}`, mapedArr)
// filter（跳过empty，返回结果不包含empty）
arr.forEach(v => console.log(`forEach ${v}`))
const filteredArr = arr.filter(v => {
  console.log(`filter ${v}`)
  return true
})
console.log(`filteredArr ${filteredArr}`, filteredArr)
```

### async/await

注意 for 循环（循环之间会等待）与 forEach（不会等待）调用的不同。

### 稀疏与密集数组

稀疏数组即非连续性数组，里面有空元素，这在使用数组方法（map,forEach 等）时不会遍历该元素。

快速创建密集数组：

```javascript
//
const arr = Array.apply(null, Array(3))
// [undefined, undefined, undefined]

// 扩展应用
const arr = Array.apply(null, Array(5))

arr.map(Function.prototype.call.bind(Number))
arr.map(Function.prototype.call, Number)
// 等同于下面
arr.map((v, i, array) => {
  return Number.call(v, i, array)
})
// [1, 2, 3, 4, 5]
// ？？？为什么这样不行，arr.map(Number.call)
```



## 数据转换为规则

### ToPrimitive

#### string

调用 toString 方法：

- 原始值，强转 string 后的原始值
- 非原始值，调用 valueOf 方法
  - 原始值，强转 string 后的原始值
  - 非原始值，抛出异常`TypeError: Cannot convert object to primitive value`

#### number

调用 valueOf 方法：

- 原始值，return 强转 number 后的原始值
- 非原始值，调用 toString 方法 - 原始值，return 强转 number 后的原始值 - 非原始值，抛出异常`TypeError: Cannot convert object to primitive value`
  > 强转 number 规则，null -> 0，undefined -> NaN，true -> 1，false -> 0，失败 -> NaN

## Promise
灵活运用转Promise对象的接口Promise.resolve()
### Promise.resolve()
处理4种情况参数
- Promise对象，直接返回。
- thenable对象，该对象then方法里必须使用resolve或者reject，否则Promise.resolve(thenable对象)一直是pending状态
``` javascript
const obj = {
  then: () => {}
}

Promise.resolve(obj)
// 等价于
new Promise(obj.then)
```
- 其他值。
``` javascript
const obj = {}
Promise.resolve(obj)
// 等价于
new Promise(resolve => resolve(obj))
```
- 不传值。等价于Promise(undefined)