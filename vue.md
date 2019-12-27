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

### 父子组件的生命周期
- 父组件会等待子组件挂载
- 当子组件完成挂载后，父组件会主动执行一次beforeUpdate/updated钩子函数

## 插槽

- slot 合并
- v-slot 替换
- slot + slot-scope 替换

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
};
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

## echarts

1. 使用 this.\$refs.xxx 获取 dom，而不使用 document.getElementById('xxx)

2. 图表的 chart.resize()功能提取为单独函数，使用 resize-detector 库的 addListener 进行绑定和 removeListener 取消绑绑定，且使用 debounce 进行防抖处理(可在 created 生命周期里对 vue 组件的 methods 中的方法进行改造)

3. 不要忘记在 beforeDestroy 生命周期里解绑事件，以及使用 chart.dispose()图表

## mock

```javascript
 proxy: {
    "/api": {
      target: "http://localhost:3000"
      bypass: function(req, res) {
        if (req.headers.accept.indexOf("html") !== -1) {
          console.log("Skipping proxy for browser request.");
          return "/index.html";
        } else if (process.env.MOCK !== "none") {
          console.log(req.path);
          const name = req.path
            .split("/api/")[1]
            .split("/")
            .join("_");
          const mock = require(`./mock/${name}`);
          const result = mock(req);
          delete require.cache[require.resolve(`./mock/${name}`)];
          return res.send(result);
        }
        return false;
      }
    }
  }
}
```

## vue-cli 脚手架使用注意

### 自动格式化

默认的错误警告提示只在命令行显示，且格式化需要手动运行`yarn lint`。

自动操作如下：

- 安装 eslint 插件
- 配置

```json
{
  // 打开保存时自动格式化，在vue文件中与autoFix属性缺一不可
  "eslint.autoFixOnSave": true,
  // 需要调用eslint的文件类型
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    {
      "language": "vue",
      "autoFix": true
    }
  ]
}
```

建议规则配置操作，修改`.eslintrc.js`：

- extends 字段中的`"plugin:vue/essential"` 改为`"plugin:vue/recommended"`
- rules 字段中添加如下忽略规则：
  - "vue/require-default-prop": "off"

## 踩坑

- props 传递的数据，如果不是在原对象上面修改，那么直接紧接着使用`this.$refs.xxx.xxx()`方法里面访问到的还是旧值。需要使用`this.$nextTick()`;
