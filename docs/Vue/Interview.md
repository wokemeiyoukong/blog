#### `v-for & v-if` 优先级

+ 2.6 源码中的位置 `/compiler/codegen/index.js`

```js
v-for 的优先级 高于 v-if
同时使用时用 会执行 v-for 在每次循环时再判断 v-if 浪费性能 v-if将重复运行在每个v-for的循环中

 <p v-for="item in list" v-if="isFolder">{{item.name}}</p>
 
# 生成的render函数 (isFolder) 每次循环都会先去判断 isFolder 再执行运算
ƒ anonymous() {
    with(this){return _c('div',{attrs:{"id":"root"}},[_c('h1',[_v("v-for & v-if 优先级问题")]),_v(" "), _l((list),function(item){return (isFolder)?_c('p',[_v(_s(item.name))]):_e()})],2)}
}

<template v-if="isFolder">
	<p v-for="item in list">{{item.name}}</p>
</template>
# 生成的render函数 先判断 再决定是否执行render函数
ƒ anonymous() {
	with(this){return _c('div',{attrs:{"id":"root"}},[_c('h1',[_v("v-for & v-if 优先级问题")]),_v(" "),(isFolder)?_l((list),function(item){return _c('p',[_v(_s(item.name))])}):_e()],2)}
}
```

+ `vue-next`

```vue
<template>
  <!-- error: isComplete is undefined v-if的优先级比v-for更高 -->
  <div v-for="todo in todos" v-if="!todo.isComplete" :key="todo">{{todo}}</div>
</template>

<script lang='ts'>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'IfFor',
  data() {
    return {
      todos: [
        { isComplete: false, todo: '1'},
        { isComplete: true, todo: '3'},
        { isComplete: false, todo: '2'},
        { isComplete: true, todo: '4'}
      ]
    }
  }
})
 // render函数
function render(_ctx, _cache) {
  with (_ctx) {
    const { renderList: _renderList, Fragment: _Fragment, openBlock: _openBlock, createBlock: _createBlock, toDisplayString: _toDisplayString, createVNode: _createVNode, createCommentVNode: _createCommentVNode } = _Vue

    return isFolder
      ? (_openBlock(true), _createBlock(_Fragment, { key: 0 }, _renderList(list, (item) => {
          return (_openBlock(), _createBlock("p", null, _toDisplayString(item.name), 1 /* TEXT */))
        }), 256 /* UNKEYED_FRAGMENT */))
      : _createCommentVNode("v-if", true)
  }
}
    
    //
function render(_ctx, _cache) {
  with (_ctx) {
    const { renderList: _renderList, Fragment: _Fragment, openBlock: _openBlock, createBlock: _createBlock, toDisplayString: _toDisplayString, createVNode: _createVNode, createCommentVNode: _createCommentVNode } = _Vue

    return isFolder
      ? (_openBlock(true), _createBlock(_Fragment, { key: 0 }, _renderList(list, (item) => {
          return (_openBlock(), _createBlock("p", null, _toDisplayString(item.name), 1 /* TEXT */))
        }), 256 /* UNKEYED_FRAGMENT */))
      : _createCommentVNode("v-if", true)
  }
}
</script>
```





#### 组件 `data` 是函数，根组件是个对象

```
组件可能存在多个实例，以对象形式定义data，多个组件之间共用一个data对象，状态改变会影响所有组件实例。
采用函数定义，在initData时会将其作为工厂函数返回全新的data对象，规避了多实例之间状态污染问题
根实例只有一个不存在状态污染问题
```

