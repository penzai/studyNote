### 双向数据绑定
.sync只是一个语法糖，在子组件还是需要监听别名的改变，然后出发this.$emit('update:同步字段名', value)