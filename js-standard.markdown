## `JS`规范
### `bind`方法
- 语法`fun.bind(thisArg[, arg1[, arg2[, ...]]])`
- 此方法会产生一个**新方法**，所以原方法改变了,它依然存在
- `bind`传递的参数优先级比原函数高，且后来的参数是紧跟着`bind`参数的后面，而不是对位取代
### `class`
#### 基础知识
- 函数拥有`prototype`属性（原型链），而任意对象都拥有原型`__proto__`。最简单的一条从属关系为`sp.__proto__ === Super.prototype`
#### `es5`写法
> 注意继承方法，即设置`prototype`
``` javascript
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

``` javascript
class Car {
  constructor(name, color) {
    this.name = name
    this.color = color
  }
  showName () {
    console.log(this.name)
  }
  static say () {
    console.log('I am car.')
  }
}

class Audi extends Car {
  constructor (name, color, price) {
    super(name, color)
    this.price = price
  }
  showPrice () {
    console.log(this.price)
  }
}
```
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

``` javascript
function *show () {
  alert(1)
  let a = yield 3
  alert(2)
  console.log(a)  // a = 传入的参数
}
```
#### 利用`runner`函数进行带逻辑的异步请求
``` javascript
function runner(_gen){
  return new Promise((resolve, reject)=>{
    var gen=_gen();

    _next();
    function _next(_last_res){
      var res=gen.next(_last_res);

      if(!res.done){
        var obj=res.value;

        if(obj.then){
          obj.then((res)=>{
            _next(res);
          }, (err)=>{
            reject(err);
          });
        }else if(typeof obj=='function'){
          if(obj.constructor.toString().startsWith('function GeneratorFunction()')){
            runner(obj).then(res=>_next(res), reject);
          }else{
            _next(obj());
          }
        }else{
          _next(obj);
        }
      }else{
        resolve(res.value);
      }
    }
  });
}

runner(function *(){
  let userData=yield $.ajax({url: 'getUserData', dataType: 'json'});

  if(userData.type=='VIP'){
    let items=yield $.ajax({url: 'getVIPItems', dataType: 'json'});
  }else{
    let items=yield $.ajax({url: 'getItems', dataType: 'json'});
  }

  //生成、...
});
```
#### 与`async/await`区别
- 写法一样，还不需要`runner`函数
- 还可以写成箭头函数。

### 正则表达式
默认匹配是贪婪匹配，非贪婪如下
- `*?` 重复任意次，但尽可能少重复
- `+?` 重复1次或更多次，但尽可能少重复
- `??` 重复0次或1次，但尽可能少重复
- `{n,m}?` 重复n到m次，但尽可能少重复
- `{n,}?` 重复n次以上，但尽可能少重复

### 事件循环`event loop`
- 主进程（即整体代码）属于`macrotasks`（`tasks`）。待执行完毕才会去寻找`microtask` (主动使用`.click()`触发事件，不属于异步！此时并没结束主进程，所以如果有事件冒泡，这时会先冒完，而且此时`MutationObserver`处于`pending`状态，无法多次实现)
- `js`通过事件循环（`event loop`）机制来定期访问异步任务队列
- `macroTasks`的任务，一次循环只处理一次（比如嵌套`setTimeout`)，而`microtask`队列能处理多次（比如`Promise`的`then`回调）

![](./js-standard/event-loop.jpg)

**macro-task**
- 整体代码
- `setTimeout`
- `setInterval`
- `setImmediate` (存在于`node`, 在`v.9.11.1`环境下测试，慢于`setTimeout(fn, 0)`)
- `requestAnamationFrame`
- `I/O`
- `UI渲染`

**microtask**
- `Promise`
- `process.nextTick`
- `Object.observe`
- `MutationObserver`

### `new`执行了什么
创建一个新对象foo；

并将它的__proto__指向其构造函数的prototype，foo.__proto__ = Foo.prototype;

动态将this指向新对象，Foo.apply(foo，arguments);

执行函数体中的代码；

放回新对象foo;