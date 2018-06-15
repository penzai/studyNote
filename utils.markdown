## 工具类函数
### 根据周找出日期范围
非标准，标准的可参考`moment.js·
``` javascript
/**
 * 通过周查询出具体的年月时间段
 * @param {Number} week 周
 * @param {Number} year 年
 */
var week2DayRange = (week, year) => {
  if (!(week && year)) return
  // 天数转为毫秒
  var days2Milliseconds = days => {
    days = days == 7 ? 0 : daysk
    return days * 24 * 60 * 60 * 1000
  }
  // Date对象转成字符串
  var date2string = date => {
    if (Object.prototype.toString.call(date) !== "[object Date]") return

    var y = date.getFullYear()
    var m = '0' + (date.getMonth() + 1)
    var d = '0' + date.getDate()
    return y + '-' + m.slice(-2) + '-' + d.slice(-2)
  }

  // 第一周
  var firstWeekStart = new Date(year + '' + '/1/1')
  var firstWeekEnd = new Date(+firstWeekStart + (days2Milliseconds(7 - firstWeekStart.getDay())))
  if (week == 1) {
    return {
      start: firstWeekStart,
      end: firstWeekEnd,
      startString: date2string(firstWeekStart),
      endString: date2string(firstWeekEnd)
    }
  }

  // 第N周
  var nWeekEnd = new Date( +firstWeekEnd + days2Milliseconds(7 * (+week - 1)) )
  var nWeekStart = new Date(+nWeekEnd - days2Milliseconds(6))

  return {
    start: nWeekStart,
    end: nWeekEnd,
    startString: date2string(nWeekStart),
    endString: date2string(nWeekEnd)
  }
}
```
### Date对象转成字符串
``` javascript
  /**
   * 工具类 - Date对象转成字符串
   */
  var date2string = function (date) {
    if (Object.prototype.toString.call(date) !== "[object Date]") return

    var y = date.getFullYear()
    var m = '0' + (date.getMonth() + 1)
    var d = '0' + date.getDate()
    return y + '-' + m.slice(-2) + '-' + d.slice(-2)
  }
```
### 深拷贝
使用序列化和反序列化深拷贝时，会有副作用，比如会忽略某些隐藏对象。
``` javascript
let copyObject = obj => {
  let newObj = {}
  let func = (object, newObj) => {
    for (const key in object) {
      if (object.hasOwnProperty(key)) {
        var newObj2 = {}
        var element = object[key]

        if (typeof object[key] === 'object') { // {a: 3}
          newObj[key] = newObj2
          return func(element, newObj2)
        } else { //3
          newObj[key] = element
        }
      }
    }
    return func
  }
  func(obj, newObj)
  return newObj
}
```