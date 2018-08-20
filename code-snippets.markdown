### 二叉树查询，并返回路径

```javascript
const all = [
  {
    id: 0,
    children: [
      {
        id: 1,
        children: [
          {
            id: 2,
            children: [
              {
                id: 3,
                children: []
              }
            ]
          },
          {
            id: 4,
            children: []
          }
        ]
      },
      {
        id: 5,
        children: []
      },
      {
        id: 6,
        children: [
          {
            id: 7,
            children: []
          },
          {
            id: 8,
            children: []
          }
        ]
      }
    ]
  }
]

let ids = []
const func = () => {
  let ret = {}
  const find = (data, id) => {
    data.map((v, i, arr) => {
      if (JSON.stringify(ret) !== '{}') return
      // 非首尾循环时，弹出上一个选项
      if (i !== 0 && ids[ids.length - 1] === arr[i - 1]) {
        ids.pop()
      }
      ids.push(v.id)
      if (v.id === id) {
        ret = v
      } else {
        if (v.children.length > 0) {
          find(v.children, id)
        } else {
          // 触底弹出
          ids.pop()
          // 最后一个循环时，弹出父级
          if (i === arr.length - 1) {
            ids.pop()
          }
        }
      }
    })
  }
  find(all, 3)
  return ret
}
```
