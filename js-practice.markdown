## `js`实例
### 小点子
- 复制数组有`slice(), concat(), ...`
- `Date`时间的`new`方法可自由设置`年月日小时分`，以此来构造想要的任何时间
- 取整，应该用内置`Math.floor()`向下取整，对应的还有`Math.ceil()`向上取整，四舍五入`Math.round()`
### js糟粕
- 变量，重复声明，无法限制修改，没有块级作用域
- 
### 各种简写
```
// for
for (let i = 0; i < array.length; i ++)
for (let index of array)

// foreach
function a() {}
array.forEach(a)
```
### 函数节流
``` javascript
// 先执行一次，后续点击无效，必须满足wait
const throttle = wait => fn => {
  var timer;
  return (...args) => {
    if (!timer) {
      timer = setTimeout(() => timer = null, wait);
      return fn(...args);
    }
  }
}

// wait后执行，连续执行则不执行
const throttle = wait => fn => {
  var timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn(...args);
    }, wait)
  }
}
```
### 函数柯里化
``` javascript
/**
 * 一、惰性求值
 * 1. 并不真正实时计算
 * 2. 当用不调用参数结尾时，即xxx()才开始计算，把柯里化过程中的所有参数一下子传递给目标函数进行计算
 */
/**
 * 柯里化主函数
 */
const curryingLazy = fn => {
  const loop = (...args1) => {
    if (args1.length == 0) return fn(args1)
    return (...args2) => {
      if (args2.length == 0) return fn(args1)
      return loop.call(null, ...args1, args2)
    }
  }
  return loop
}
/**
 * 业务函数
 */
const calc = curryingLazy(args => args.length > 0 && args.reduce((prev, next) => {
  const covert = arr => {
    if (Array.isArray(arr) && arr.length > 1) {
      return arr.reduce((prev, next) => prev + next)
    } else {
      return arr
    }
  }
  return covert(prev) * covert(next)
}))

console.log(calc(2)(3, 5)(3)()) // 48

/**
 * 二、实时求值
 */
const calcLiving = (() => {
  let nums = []

  const calc = (...args) => {
    const item = args.reduce((prev, next) => prev + next)
    nums.push(item)
    return calc
  }

  calc.valueOf = () => {
    const rlt = nums.reduce((prev, next) => prev * next)
    nums = []
    return rlt
  }

  return calc
})()

var calcLivingRlt = calcLiving(2)(3, 5)(3)
console.log(calcLivingRlt.valueOf()) // 48
```

