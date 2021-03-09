#### 数据双向绑定

+ 数据变化更新视图，视图变化更新数据

+  `vue` 会遍历`data` 中声明的属性 使用添加`Obj.defineProperty` 把属性转为`getter setter` 属性，通过`getter setter` 追踪依赖，在属性被访问和修改时通知变更  

+ 每个组件实例都对应一个`watcher` 实例，组件渲染过程的数据属性记录为依赖，当依赖对应的`setter` 触发是，通知`watcher` ，关联的组件重新渲染

+ `vue` 不能检测对象属性的新增或删除（`Vue.set || vm.$set`） 方法新增响应式数据

+ 数组里套对象，对象时支持响应式变化的，常量则没有效果

+ 修改数组索引和长度不会导致视图更新

+ 使用 `Object.assign()` 或 `_.extend()` 方法来添加属性。但是，这样添加到对象上的新属性不会触发更新。在这种情况下可以创建一个新的对象，让它包含原对象的属性和新的属性：

  ```js
  this.objitem = Object.assign({},this.objitem,{a:1,b:1})
  
  // 1、需要observe的数据对象进行递归遍历，包括子属性对象的属性，都加上setter和getter这样的话，给这个对象的某个值赋值，就会触发setter，那么就能监听到了数据变化
  
  // 2、compile解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图
  
  // 3、Watcher订阅者是Observer和Compile之间通信的桥梁，主要做的事情是:①在自身实例化时往属性订阅器(dep)里面添加自己②自身必须有一个update()方法③待属性变动dep.notice()通知时，能调用自身的update()方法，并触发Compile中绑定的回调，则功成身退。
  
  // 4、MVVM作为数据绑定的入口，整合Observer、Compile和Watcher三者，通过Observer来监听自己的model数据变化，通过Compile来解析编译模板指令，最终利用Watcher搭起Observer和Compile之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定效果。
  ```

#### `watch` 深度监听

+ 最初绑定是是不会执行的，需要监听的数据改变才会执行

+ 可以执行任何逻辑，如函数节流、Ajax异步获取数据，甚至操作 DOM（不建议）。

  ```html
  <div>
        <p>FullName: {{fullName}}</p>
        <p>FirstName: <input type="text" v-model="firstName"></p>
  </div>
   
  new Vue({
    el: '#root',
    data: {
      firstName: 'Dawei',
      lastName: 'Lou',
      fullName: ''
    },
    watch: {
      firstName(newName, oldName) {
        this.fullName = newName + ' ' + this.lastName;
      }
    } 
  })
  ```

+ `handler` 方法 和 `immediate` 属性

  ```javascript
  watch: {
    firstName: {
      handler(newName, oldName) {
        this.fullName = newName + ' ' + this.lastName;
      },
      // 代表在wacth里声明了firstName这个方法之后立即先去执行handler方法
      immediate: true
    }
  }
  ```

+ `deep` 属性

  ```javascript
  watch: {
    obj: {
      handler(newName, oldName) {
        console.log('obj.a changed');
      },
      immediate: true,
      // deep 每个属性改变都会执行handler函数 性能开销
      deep: true
    }
  }
  // 改善 遇到属性a 才会设置监听函数 针对具体属性变化执行handler
  watch: {
    'obj.a': {
      handler(newName, oldName) {
        console.log('obj.a changed');
      },
      immediate: true,
      // deep: true
    }
  }
  ```

+ 监听路由

  ```javascript
  watch: {
      changeShowType(value) {
        console.log("-----"+value);
      },
      '$route'(to,from){
        console.log(to);   //to表示去往的界面
        console.log(from); //from表示来自于哪个界面
        if(to.path=="/shop/detail"){
          console.log("商品详情");
        }
      }
    },
  ```

#### `vue` 特点

+ 响应式和组件化开发
  + 数据和视图的同步
  + 一切都是组件

#### 生命周期

```js
new Vue(): 创建vue实例
// beforeCreate: data/methods 数据还没有初始化 || 实例初始化之后 数据观测之前调用
// Created: data/methods 已经初始化 || 实例创建完成之后调用 数据观测 属性和方法的运算 watch/event 事件回调 没有$el
// beforeMount: 模板已经编译还未挂载 || 挂载之前调用相关的render函数首次调用
// Mounted: Vue实例初始化创建完成 || $el 被新创建的vm.$el 替代 挂载到实例上
// beforeUpdate: 编译好的模板替换到浏览器页面中 data和数据最新 页面还是旧数据
// virtual DOM re-render and patch: 内存中重新渲染一份最新的内存DOM树，differ算法 渲染页面
// Updated: data编译到内存的dom树中页面更新完成 实现 M→V的更新
// Destroy: data的改变不会再触发周期函数 vue实例解除事件监听和dom绑定 但 dom结构依然存在
// vm.$mount(el): 编译模板字符串渲染为内存中的Dom
```

#### 编译过程

1. 将模板解析为抽象语法树（`AST`）： `parse`
2. 优化（`AST`）: `optimize`
3. 将（`AST`）转换为 `render` 函数: `generate`

+ `3W1H（what who when how）` 原则
  + `vue` 的语法指令是不能被`html` 解析的
  + 通过编译过程进行依赖的收集，收集后`data` 中的数据模型和视图之间产生了绑定关系产生了依赖关系
  + 模型发生变化后通知依赖进行更新，达成模型驱动视图的变化

#### 虚拟`DOM`

+ 用`js` 模拟`dom`结构 

+ `DOM` 变化对比放在`js` 层来做

+ 提高重绘性能

  + 两个相同的组件产生类似的DOM结构，不同的组件产生不同的DOM结构。

  + 同一层级的一组节点，他们可以通过唯一的id进行区分。

  + 虚拟DOM的 `Diff` 算法的复杂度从O(n^3)降到了O(n)

  + 当页面的数据发生变化时，`Diff`算法只会比较同一层级的节点：

    如果节点类型不同，直接干掉前面的节点，再创建并插入新的节点。

    如果节点类型相同，则会重新设置该节点的属性，从而实现节点的更新。

  + **key的作用主要是为了高效的更新虚拟DOM** ，``key`  给每个节点做一个唯一标识，  `diff` 算法中正确识别此节点。 相同标签名元素过度切换时，也会使用到key属性，用作区分
  
  ```js
  /*
  * 通过JavaScript创建 Virtual Dom 的Dom对象，寻找Dom节点更新Dom的成本很高，批处理调用，同时修改Dom
  * 虚拟Dom是轻量级的JavaScript对象，由渲染函数创建。包含三个参数：元素，带有数据的对象，prop,attr以及更多，和一个数组，数组是传递子级的地方，子级也可以有自己的子级，构建完整的元素树。
  */
  ```
  
  

#### 插槽

+ 2.6 中同名插槽会覆盖前一个

  ```vue
  <div id="root">
      <body-content>
          <template v-slot:header='slotProps'>
              <div class="header">{{ slotProps.user.b}}</div>
          </template>
          <template #footer>
              <div class="footer">footer</div>
          </template>
          <template #footer>
              <div class="footer">same footer</div>
          </template>
          // v-slot 代替
          <!-- 匿名插槽 -->
          <template #default>
              <div>我是默认插槽</div>
          </template>
          <!-- 具名插槽 -->
          <template #content>
              <div>我是具名插槽</div>
          </template>
          <!-- 作用域插槽 子组件的值暴露给父组件-->
          <template v-slot:scope='{slotvalue, foo}'>
              <div>{{ slotvalue }} - {{ foo }}</div>
          </template>
      </body-content>
  </div>
  <script>
  Vue.component('body-content',{
      data(){
          return {
              user: {
                  a: 'aa',
                  b: 'bb'
              }
          }
      },
      template:  
      `<div>
          <slot name='header' :user='user'>{{ user.a }}</slot>
          <div class="content">content</div>
          <slot name='footer'></slot>
          <slot name='footer'></slot>
      </div> `
  })
  </script>
  ```


#### 计算属性

+ `computed`: `get set` 获取计算属性和设置计算属性，`set` 设置属性并不是直接修改计算属性，而是修改它的依赖

+ 依赖的属性没有改变多次计算得到的值是缓存的结果，不会多次执行

+ 同步操作，异步操作使用`watch`

  ```js
  computed: {
   fullName: {
     // getter
     get: function () {
       return this.firstName + ' ' + this.lastName
     },
     // setter
     set: function (newValue) {
       //this.fullName = newValue 这种写法会报错
       var names = newValue.split(' ')
       this.firstName = names[0]//对它的依赖进行赋值
       this.lastName = names[names.length - 1]
     }
   }}
  ```

#### `this.$nextTick()`

+ 回调延迟到下次 DOM 更新循环之后执行

+ 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

+ `vue` 更新`dom` 是异步进行更新的

+ `Vue `在修改数据后，视图不会立刻更新，而是等**同一事件循环**中的所有数据变化完成之后，再统一进行视图更新

+ `Vue ` 开启一个异步队列，并缓冲在此事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次

  ```js
  // 修改视图后将当前改变触发的Watcher 推入任务队列（异步）等待后续所有视图数据改变一起更新 
  // Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MessageChannel 方法，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。
  <template>
    <section>
      <h1 ref="hello">{{ value }}</h1>
      <el-button type="danger" @click="get">点击</el-button>
    </section>
  </template>
  <script>
    export default {
      data() {
        return {
          value: 'Hello World ~'
        };
      },
      methods: {
        get() {
          this.value = '你好啊'; // 异步更新dom 此时还没有更新
          console.log(this.$refs['hello'].innerText); // hello world 
          this.$nextTick(() => { // 回调放在dom更新之后执行 返回一个promise 可用async/await 改写
            console.log(this.$refs['hello'].innerText); // 你好 
          });
        }
         //  async/await 改写
          _dealClick: async function() {
            this.value = 'async'
            console.log(this.$refs.hello.innerHTML)
            const res = await this.$nextTick()
            console.log(this.$refs.hello.innerHTML)
          }
      },
      mounted() {
      },
      created() {
      }
    }
  </script>
  ```

#### 组件通信

+ `$parent $children`

  ```js
  <div id="app">
      <child :value='value'></child>
    </div>
    <script src="./vue.js"></script>
    <script>
      const child = {
        props: ['value'],
        data() {
          return {
            childvalue: 2
          }
        },
        template: `<div> child组件：{{ value }}</div>`,
        mounted () {
          console.log(this.$parent.value) // 1 父组件data的值
          console.log(this.$children) // 没有下一级 为空
        },
      }
      new Vue({
        el: '#app',
        data() {
          return {
            value: 1
          }
        },
        components: {
          child,
        },
        mounted () {
          console.log(this.$parent) // 根组件没有父组件 undefined
          console.log(this.$children[0].childvalue) // 父组件的$children为数组 2
        },
      })
    </script>
  ```

  

+ `$attrs $listeners`

  + 父组件向子组件传值时，子组件中没有通过`props` 接受时，未接受的值会通过`$attrs` 存储起来，父子组件通信时，未在`props` 中接受的值都保存在`$attrs`,并且子组件的节点上会挂载此属性，通过设置`inheritAttrs:false` 去除这一属性
  + 孙组件中的通过`this.$emit` 发射的方法会通过 子组件中使用 `v-on = '$listeners'`  向父组件传递,父组件再通过父子组件传值的方式接受此方法

  ```js
  <div id="app">
      <child :value='value' :coo='coo' @sonclick='rootclick'></child>
    </div>
    <script src="./vue.js"></script>
    <script>
      const son = {
        props: ['coo'],
        template: `<div @click='sonClick'>attrs: {{$attrs}} son: {{ coo }}</div>`,
        methods: {
          sonClick() {
            this.$emit('sonclick')
            console.log('sonclick')
          }
        },
      }
      const child = {
        props: ['value'],
        data() {
          return {
            childvalue: 2,
            childattr: 'childattr'
          }
        },
        inheritAttrs:false,
        components: {
          son,
        },
        template: `<div>
          attrs: {{ $attrs }} child组件：{{ value }}
          <son :childattr='childattr' v-bind='$attrs' v-on='$listeners'/>
          </div>`,
        mounted () {
          console.log(this.$parent)
          console.log(this.$children)
        },
      }
      new Vue({
        el: '#app',
        data() {
          return {
            value: 1,
            coo: '父组件传递hee'
          }
        },
        components: {
          child,
        },
        mounted () {
          console.log(this.$parent)
          console.log(this.$children[0].childvalue)
        },
        methods: {
          rootclick() {
            console.log('rootclick');        
          }
        },
      })
    </script>
  ```

  

+ `provide inject`

  + 注入的值不是响应式的
  
  ```js
  // 父组件
  export default{
      provide(){
          return{
              somevalue: 'this is from $parents',
              // provide 也可传递当前组件实例
              instance: this
          }
      }
  }
  // 子组件中
  export default {
      // 通过this.somevalue 直接使用 通过instance获取父组件实例
      inject: ['somevalue', 'instance']
      inject: {
      // 属性变更名称
      instance: {
          from: 'app',
          default: () => ({})
          }
  	}
  }
  ```
  



+ 事件总线机制

  ```js
  import Vue from 'vue'
  const eventBus = new Vue() // 事件触发池
  
  // *事件订阅
  eventBus.$on('eventname', (val) => {})
  
  // 事件触发
  eventBus.$emit('eventname', val)
  
  // 事件移除
  eventBus.$off('eventname', {})
  ```

  



#### 服务端渲染

+ 服务端渲染渲染首屏结构，其他页面 `spa`
+ `spa`: 首屏加载时间过长、不利于网站 `SEO`

#### `cube-ui` 页面切换屏幕超出解决

+ `bug` 首页使用了 `slide` 做轮播 同时切换时可能导致页面宽度超出

  ```vue
  <!-- 动画外层新增div 对div设置包裹住标签 固定页面-->
  <div class="animate-box">
      <transition :name="transitionName">
          <router-view class="Router"></router-view>
      </transition>
  </div>
  <style>
  .animate-box
      position absolute
      top 0
      bottom 0
      width 100%
      overflow hidden
  </style>
  ```

#### `v-for v-if 连用`

+ `v-for` 的优先级高于 `v-if` 连用会将 `v-if` 给每个元素添加上，造成性能浪费

#### `.sync` 修饰符

+ 父子组件通信 子组件修改父组件传递过来的值 纯修改时

  ```js
  // 子组件 通过事件触发
  this.$emit('update:title', '传递后的标题')
  
  // 父组件 子组件点击直接更改props 传递过来的title
  <Props :title.sync="title" />
  ```

####  `$options`

+ 实例属性

  ```js
  this.$options // 选项配置都会被存储在实例的$options 获取过滤器 data 其他配置项
  ```

#### 路径别名配置

+ `vue.config.js`

  ```js
  const path = require('path')
  function resolve (dir) {
    return path.join(__dirname, dir) // 获取文件夹路径
  }
  
  module.exports = {
    resolve: {
      alias: {
        // 设置路径代理
        '@': resolve('src'),
        'components': resolve('src/components'),
        'api': resolve('src/api'), 
        'icons': resolve('src/icons'),
        '#': resolve('src/assets'),
        'utils': resolve('src/utils')
      }
    }
  }
  ```

  

#### `vue.observable`

+ 对象变成响应式数据，小型状态管理器

  ```js
  // state.js
  import Vue from 'vue'
  
  export const state = Vue.observable({
      count: 0
  })
  
  export const mutations = {
    SET_COUNT(payload) {
      if (payload > 0) {
          state.count = payload
      } 
    }
  }
  // 使用
  import { state } from './state.js'
  
  computed() {
      count() {
          return state.count
      }
  }
  
  methods: {
      setCount() {
          state.count++
      },
      // 自定义mutations
      setCount() {
         mutations.SET_COUNT(100)
      }
  }
  ```

#### 样式穿透

+ 修改三方组件样式

  ```css
  <style scoped>
  >>> .scoped-third-party-class {
    color: gray;
  }
  </style>
  
  <style scoped>
  /deep/ .scoped-third-party-class {
    color: gray;
  }
  </style>
  
  <style scoped>
  ::v-deep .scoped-third-party-class {
    color: gray;
  }
  </style>
  
  ```

#### `diff` 算法

+ `diff` 算法的作用域
  + `patch`
    + 组件节点更新时，遍历 `vnode` 的过程中，通过不同的 `patch` 方法来 `patch` 新老 `vnode` ，找到对应的 新旧节点，复用未改变的节点，避免了重复创建元素节点
    + 在 ` patch` 子 `vnode` 过程中，找到与新 `vnode` 对应的老 `vnode`，复用真实的 `dom` 节点，避免不必要的性能开销
  + `patchChildren`
    + 根据是否存在key进行真正的 `diff` 或者直接 `patch`
  + `diff`算法存在 `patchChildren` 方法中，而 `patchChildren` 方法用在 `Fragment` 类型和 `element` 类型的 `vnode` 中
+ `key` 的作用
  + 通过 `isSameVNodeType` 方法判断，通过 `key` 相等判断新老节点， 判断 `newVnode` 和 `oldVnode` 是否相等，从而复用与新节点对应的老节点
+ `key` 的使用
  + `index` 作为 `key` 时，节点的改变 `diff` 算法都会从头到尾依次 `patch` ，所有节点都未复用
  + `index` 拼接其他值作为 `key`， 每个节点都找不到对应的 `key` ，节点未复用，新的 `Vnode` 都需要被重新创建
  + 唯一值 `id` 
+ `diff` 的主要策略
  + 按 `tree` 层级 `diff`
    +  `dom`树结构同层比对
  + 按类型进行`diff`
    + 类型发生改变时 直接创建新的 `vdom` 替换旧节点
  + 列表`diff`
    + `diff` 节点处于同一层级，三种节点操作，删除移动插入，
    + `key` 属性调整排序方式，快速识别新旧列表之间的变化内容



#### 过渡动画

+ `transition` 组件： 动态的添加/删除 `css` 类名  

+ 自定义过渡类名 ，结合第三方动画库使用

+ 同时使用过渡和动画，需要定义 `type` 属性 确定监听类型

  ```vue
  <template>
    <div class="demo">
      <button @click="show=!show">按钮</button>
      <transition name="fade">
        <p v-if="show">文字！</p>
      </transition>
      <transition name="bounce">
        <p v-if="show">bouncebouncebounce文字！</p>
      </transition>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        show: false
      }
    }
  }
  </script>
  
  <style>
   /* 动画进入和离开时添加的类名 */
   /* 默认前缀 v- 
    * v-enter-from
      v-enter-active
      v-enter-to
      v-leave-from
      v-leave-active
      v-leave-to
      
      设置不同的动画曲线
      
      使用自定义类名 配合第三方动画库
      enter-from-class
      enter-active-calss
      enter-to-class
      ...
    */
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity .5s ease;
  }
  
  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
  }
  </style>
  ```



+ `javascript` 钩子函数定义过渡动画，在 `attribute` 中声明 钩子

  ```vue
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.3.4/gsap.min.js"></script>
  
  <div id="demo">
    <button @click="show = !show">
      Toggle
    </button>
  
    <transition
      @before-enter="beforeEnter"
      @enter="enter"
      @leave="leave"
      :css="false"
    >
      <p v-if="show">
        Demo
      </p>
    </transition>
  </div>
  
  <script>
  	const Demo = {
    data() {
      return {
        show: false
      }
    },
    methods: {
      beforeEnter(el) {
        gsap.set(el, {
          scaleX: 0.8,
          scaleY: 1.2
        })
      },
      enter(el, done) {
          // done 进行回调 否则enter/leave会同步调用 过渡会立即完成 :css="false" 跳过对css的检测
        gsap.to(el, {
          duration: 1,
          scaleX: 1.5,
          scaleY: 0.7,
          opacity: 1,
          x: 150,
          ease: 'elastic.inOut(2.5, 1)',
          onComplete: done
        })
      },
      leave(el, done) {
        gsap.to(el, {
          duration: 0.7,
          scaleX: 1,
          scaleY: 1,
          x: 300,
          ease: 'elastic.inOut(2.5, 1)'
        })
        gsap.to(el, {
          duration: 0.2,
          delay: 0.5,
          opacity: 0,
          onComplete: done
        })
      }
    }
  }
  
  Vue.createApp(Demo).mount('#demo')
  </script>
  ```



+ 过渡模式： `in-out` 新元素先过渡，完成后当前元素离开 `out-in` 当前元素先过渡，完成后新元素进入

  ```vue
  <transition name="fade" mode="out-in">
      <p v-if="show">on</p>
      <p v-else>off</p>
  </transition>
  ```



#### 组件的 `Data` 为什么是函数

+ 组件复用，对象是引用关系，组件复用时，变量存在污染，使用函数每次都是返回独立对象的拷贝
+ `new Vue` 中只实例化一次，不存在复用关系

#### 组件复用中存在的问题

+ 组件的每个实例都拥有不同的 `data` ，但组件的方法会被复用



#### 自动注册模块

+ `webpack` 的 `require.context`

  ```js
  require.context(
      directory,  // 搜索的目录
      useSubdirectories = true,  // 是否搜索子目录
      regExp = /^\.\/.*$/,  // 匹配的目标文件格式
      mode = 'sync'  // 同步还是异步
  )
  
  
  const files = require.context('@/components', false, /\.vue$/)
  
  files.keys() // 文件夹下所有以.vue结尾的文件 获取
  files('./xx.vue') // 获取组件
  ```



#### 完整版&运行时版本

+ 完整版：包含运行时和编译器版本

  ```js
  // 可以使用template进行模板编写 编译器会将模板代码转换成render函数
  new Vue({
      template:'<div>{{name}}</div>'
  })
  ```

+ 只包含运行时

  ```js
  // 除去编译器功能 
  1. 手写render函数 定义渲染过程
  new Vue({
      render(h) {
          return h('div', this.name)
      }
  })
  
  2. 借助vue-loader 编译工具 模板的编译在构建过程中完成 渲染函数的转换
  ```



#### `Mixin` 混入

```js
// data 合并me 以组件数据优先
// 钩子函数 混入的钩子函数在组件的钩子函数之前执行
// methods components directives 合并为一个对象 键值对冲突时 以组件对象的键值对优先
```

