#### `vite` &  `vue-cli`

+ `vite` 初始化项目

  ```sh
  npm init vite-app hello-vue3 # OR yarn create vite-app hello-vue3
  
  npm install -g @vue/cli # OR yarn global add @vue/cli
  vue create hello-vue3
  # select vue 3 preset
  
  # 手动升级 vue版本 
  yarn add vue@next
  #安装 typescript -D npx txc --init 生成 tsconfig.json
  yarn add typescript -D
  # 新建声明文件 shim.d.ts declare module "*.vue"
  ```



#### 性能提升

+ 静态标记

  +  变量进行静态标记 `diff` 时 对比带有静态标记的节点

  ```html
  <div>
    <p>静态节点</p>
    <p>{{name}}</p>
  </div>
  
  <script>
  	const _Vue = Vue
  
  return function render(_ctx, _cache, $props, $setup, $data, $options) {
    with (_ctx) {
      const { createVNode: _createVNode, toDisplayString: _toDisplayString, openBlock: _openBlock, createBlock: _createBlock } = _Vue
  
      return (_openBlock(), _createBlock("div", null, [
        _createVNode("p", null, "静态节点"),
        _createVNode("p", null, _toDisplayString(name), 1 /* TEXT */)
      ]))
    }
  }
  </script>
  ```

  

+ 静态提升，全局创建一次静态节点 `hoistStatic`

  + 静态节点超过6个会提升为一个静态节点，内容字符串化

  + 6个以下分开存储

    ```html
    <div>
      <section>
        <span @click="doClick">{{name}}</span>
      </section>
      <section><span>1</span></section>
      <section><span>2</span></section>
      <section><span>3</span></section>
      <section><span>4</span></section>
      <section><span>5</span></section>
      <section><span>6</span></section>
      <section><span>6</span></section>
    </div>
    
    <script>
    	import { toDisplayString as _toDisplayString, createVNode as _createVNode, createStaticVNode as _createStaticVNode, openBlock as _openBlock, createBlock as _createBlock } from "vue"
        
        // 分开存储
        const _hoisted_1 = /*#__PURE__*/_createVNode("section", null, [
      /*#__PURE__*/_createVNode("span", null, "1")
    ], -1 /* HOISTED */)
    const _hoisted_2 = /*#__PURE__*/_createVNode("section", null, [
      /*#__PURE__*/_createVNode("span", null, "2")
    ], -1 /* HOISTED */)
    const _hoisted_3 = /*#__PURE__*/_createVNode("section", null, [
      /*#__PURE__*/_createVNode("span", null, "3")
    ], -1 /* HOISTED */)
    
        // 6个以上静态提升节点
    const _hoisted_1 = /*#__PURE__*/_createStaticVNode("<section><span>1</span></section><section><span>2</span></section><section><span>3</span></section><section><span>4</span></section><section><span>5</span></section><section><span>6</span></section><section><span>6</span></section>", 7)
    
    export function render(_ctx, _cache, $props, $setup, $data, $options) {
    return (_openBlock(), _createBlock("div", null, [
        _createVNode("section", null, [
          _createVNode("span", { onClick: _ctx.doClick }, _toDisplayString(_ctx.name), 9 /* TEXT, PROPS */, ["onClick"])
        ]),
        _hoisted_1
      ]))
    }
    
    // Check the console for the AST
    </script>
    ```
    
    

+ 事件侦听器缓存

  + 事件缓存

    ```html
    <div>
      <p @click="handleClick">屋里一giao</p>
    </div>
    <script>
    	import { createVNode as _createVNode, openBlock as _openBlock, createBlock as _createBlock } from "vue"
    
    export function render(_ctx, _cache, $props, $setup, $data, $options) {
      return (_openBlock(), _createBlock("div", null, [
        _createVNode("p", { onClick: _ctx.handleClick }, "屋里一giao", 8 /* PROPS */, ["onClick"]) // 静态标记 diff时不会跳过 追踪变化
      ]))
    }
        
     // 开启cacheHandler
        
        import { createVNode as _createVNode, openBlock as _openBlock, createBlock as _createBlock } from "vue"
    
    export function render(_ctx, _cache, $props, $setup, $data, $options) {
      return (_openBlock(), _createBlock("div", null, [
        _createVNode("p", {
          onClick: _cache[1] || (_cache[1] = (...args) => (_ctx.handleClick && _ctx.handleClick(...args)))
        }, "屋里一giao") // 静态标记取消 事件缓存cache
      ]))
    }
    
    </script>
    ```

    

#### 响应式计算和监听

+ `computed`： `get/set` 函数的对象创建可写的  `ref` 对象

  ```js
  import { computed, ref, defineComponent } from 'vue'
  export default {
    setup() {
      const count = ref(1)
  
      // const double = computed(() => count.value * 2)
      const double = computed({
        get: () => count.value * 2,
        set: val => count.value = val - 1
      })
  
      double.value = 3 // count: 2 double: 4
  
      return {
        count,
        double
      }
    }
  }
  
  // *defineComponent 
  defineComponent(() => {}) // 只有setup函数时 直接传入setup函数
  ```



#### `watchEffect`

+ 立即执行传入的函数，同时响应式追踪其依赖，并在依赖更改时重新运行该函数

  ```js
  setup() {
      const count = ref(1)
      
      watchEffect(() => { console.log(count.value, 'effect')})
      // watchEffect 组件挂载初始化时执行一次
      // 更改count时 将在组件更新后执行副作用
  
      const Increase = () => {
        count.value ++
      }
  
      return {
        count,
        Increase
      }
    }
  ```

+ 在组件的 `setup` 函数或者 生命周期钩子函数被调用时，侦听器会被链接到该组件的生命周期，在组件卸载时自动停止，也可显式的停止侦听

  ```js
  const stop = watchEffect(() => {})
  
  stop() // 显式调用
  ```

+ 副作用的刷新时机

  ```js
   watchEffect(() => { console.log(count.value, 'effect')}, { flush: 'pre'}) // 'post' 'sync' 副作用执行时机
      // watchEffect 组件挂载初始化时执行一次
      // 更改count时 将在组件更新后执行副作用
  
  ```



#### `watch` 

+ 侦听特定的数据源，并在回调函数中执行副作用

+ 懒执行副作用

+ 侦听状态改变的前后值

+ 侦听具体的状态值改变

  ```js
  // 侦听ref对象
  setup() {
      const count  = ref(1)
      watch(
        count,
        (count, preCount) => {
            console.log(count, preCount)
        }
      )
  }
  
  // 侦听 getter函数
  const state = reactive({num: 0})
  watch(
    () => state.num,
    (num, preNum) => {
       console.log(num, preNum)
    }
  )
  
  // 侦听多个数据源
  watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
    /* ... */
  })
  ```

  

#### 响应式数据

+ `ref`：独立的响应式引用

+ `reactive`:  对象创建响应式状态

  ```js
  // 响应式解构
  const book = reactive({
      author: 'xx',
      year: 'xx',
      info: 'xx'
  })
  
  const { author, year } = book // 解构会丢失响应式
  const { author, year } = toRefs(book) // 转换成ref保留与源对象的响应式关联
  
  // 使用readonly防止更改响应式
  const original = reactive({count: 0})
  const copy = readonly(original) // 无法改变
  ```



#### `teleport`

+ 组件挂载在 `app` 之外的区域

#### `Vuex4` 

+ `state` 数据

  ```js
  setup(props: any, context: any) {
      const store = useStore();
  
      const state = store.state
      // 直接对外暴露不具有响应式
      const name = state.name // 直接点操作符号暴露不具有响应式
  
      let nameRef = ref("vite vue3");
  
      const handleName = (str: string) => {
        nameRef.value = str;
        store.commit('setName', str)
        console.log(store)
      };
  
      return {
        state, // 页面数据响应式更新
        nameRef,
        handleName,
      };
    },
```
  
  

#### 响应式

+ 数据改变，视图刷新

  + 检测某个值是否发生改变: `proxy` 允许我们拦截它
  + 跟踪 `track` 函数修改值：`proxt` 的 `getter` 中执行此操作，称为 `effect`
  + 触发 `trigger` 函数更新为最新的值: `proxy` 的 `setter` 中进行该操作，称为 `trigger`

  ```js
  const dinner = {
    meal: 'tacos'
  }
  
  const handler = {
    get(target, prop, receiver) {
      track(target, prop)
      const value =  Reflect.get(...arguments)
      if (isObject(value)) { // value 是对象时，转换成proxy
          return reactive(value)
      } else {
          return value
      }
    },
    set(target, key, value, receiver) {
      trigger(target, key) // 触发组件更新
      return Reflect.set(...arguments)
    }
  }
  
  const proxy = new Proxy(dinner, handler)
  console.log(proxy.meal) // tacos
  ```

  

#### `render` 函数

+ `h` 函数

  ```js
  import { defineComponent, h, onMounted, reactive, ref, toRefs } from 'vue'
  
  const Demo = defineComponent({
    name: 'Demo',
    setup() {
      const info = ref('default')
      const isShow = ref(false)
      const state = reactive({
        lists: [ 'banana', 'apple', 'orange']
      })
      onMounted(() => {
        setTimeout(() => {
          isShow.value = true
        }, 2000);
      })
      return {
        info,
        isShow,
        ...toRefs(state)
      }
    },
    render() {
            // *h(type, props, children): VNode type: String|Object|Function props: Object Children: String|Array|Object
  
      return h('div', { class: 'name' },
        this.isShow
          ? this.lists.map(item => h('div', { class: 'item' }, item))
          : 'Loading...'
      )
    }
  })
  
  export default Demo
   // h函数 生成dom解构 不需要编译器 runtime only
        // <div class='name'>{{this.info}}</div>
  ```

  

