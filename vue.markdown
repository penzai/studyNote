## 生命周期
### 创建阶段
- `beforeCreate`
- prop属性 -> data属性 -> watch属性(仅immediate)
> 依顺序执行，且不受书写顺序影响。
- `created`
- 模板编译到render函数(vNode)
> 注：这个时候还未执行模板里的代码，只是编译成render函数。如果写了render函数，那么自动跳过这一步。
- `beforeMount`
- render函数代码执行
> 遇见有使用computed的就执行computed相应方法。
- `mounted`

### 更新阶段
- 依赖变更(或者执行`$forceUpdate`方法)
- `beforeUpdate`
- render函数代码执行
- `updated`

### 销毁阶段
- `beforeDestroy`
- `destroyed`

## 函数式组件
- 无状态、无实例、没有this上下文、无生命周期
- 一般作展示用