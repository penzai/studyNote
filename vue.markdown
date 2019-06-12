## 疑问???

- Slot 存数据
- 跨组件获取组件实例（provide 为对象和返回对象函数的区别，前者不能获取到 this）

## 生命周期

### 创建阶段

- `beforeCreate`
- prop 属性 -> data 属性 -> computed 属性 -> watch 属性(仅 immediate)
  > 依顺序执行，且不受书写顺序影响。
- `created`
- 模板编译到 render 函数(vNode)
  > 注：这个时候还未执行模板里的代码，只是编译成 render 函数。如果写了 render 函数，那么自动跳过这一步。
- `beforeMount`
- render 函数代码执行
- `mounted`

### 更新阶段

- 依赖变更(或者执行`$forceUpdate`方法)
- `beforeUpdate`
- render 函数代码执行
- `updated`

### 销毁阶段

- `beforeDestroy`
- `destroyed`

## 函数式组件

- 无状态、无实例、没有 this 上下文、无生命周期
- 一般作展示用

```javascript
// vnodes 传入jsx代码
export default {
  components: {
    VNodes: {
      functional: true,
      render: (h, ctx) => ctx.props.vnodes
    }
  }
}
```

## 组件通信方式

- props、\$emit
- $attrs、$listeners
- EventBus
- provide、inject
- v-model
- $parent、$children
- $broadcast、$dispatch
- vuex

## render

> 更抽象一点来看，我们可以把组件区分为两类：一类是偏视图表现的 (presentational)，一类则是偏逻辑的 (logical)。我们推荐在前者中使用模板，在后者中使用 JSX 或渲染函数。这两类组件的比例会根据应用类型的不同有所变化，但整体来说我们发现表现类的组件远远多于逻辑类组件。

## vuex

模块记得开启命名空间。

```javascript
modules: {
    namespaced: true,
    state: {},
    getters: {},
    //...
}
```

## vue-router

- `routes`第一层的`path`必须加`/`符号，或者为`''`，代表默认路径。
- 匹配路由时，当匹配到第一个时，就停止。
- 同时设置`redirect`和`{ children: { path: '' }}`时，将无视前者。
  > 先匹配后计算
