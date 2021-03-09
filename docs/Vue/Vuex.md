#### `Vuex`

+ 集中式存储管理

+ 状态管理仓库

  ```js
  import Vue from 'vue'
  import Vuex from 'vuex'
  Vue.use(Vuex)
  
  const debug = process.env.NODE_ENV !== 'production'
  
  const store = new Vuex.Store({
      strict: debug, // 环境变量不等于 production 时 开启严格模式
      state: {}, // 数据源
      mutations: {},
      actions: {},
      getters: {} // store的计算属性 根据state中的数据返回
  })
  
  export default store 
  ```

+ `state` 
  
+ 数据源
  + 只能通过显式的提交`commit mutations`更改 `state` 中的数据  
  
+ `getters`

  + `store` 的计算属性

    ```js
    const store = new Vuex.Store({
        state: {
            list: [
                {name: '1', select: false}
                {name: '2', select: true}
            ]
        },
        getters: {
          // getter state作为第一个参数 其他getter作为第二参数
          selectTrue: ({list}) => {
            return list.filter(item => item.select)
          },
          selectLen: ({list}, getters) => {
            return getters.selectTrue.length
          }
        }
    })
    
    this.$store.getters.selectTrue // [{name: '2', select: true}]
    
    // 计算属性中批量使用 getter
    computed: {
        ...mapGetters(['getter1', 'getter2'])
    }
    ```
    
  + `getter` 返回函数 传参查询

    ```js
    getters: {
        getTarget: ({list}) => (id) => {
            return list.find(item => item.id === id)
        }
    }
    ```

  + `mapGetters` 

    ```js
    computed: {
      // 使用对象展开运算符将 getter 混入 computed 对象中
        ...mapGetters([
          'doneTodosCount',
          'anotherGetter',
          // ...
        ])
      }
    ```

    

+ mutations`

  + 更改`state` 中数据的唯一入口 同步操作

  + `mapMutations`

    ```js
    methods: {
        ...mapMutations([
          'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
    
          // `mapMutations` 也支持载荷：
          'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
        ]),
        ...mapMutations({
          add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
        })
      }
    ```

    

+ `actions`

  + 提交 `mutations` 更改 `state` 可包含任意异步操作

    ```js
    const store = new Vuex.Store({
        state: {
            num: 1
        },
        mutations: {
            // state 第一参数
            increase({num}, payload) {
                num++
            }
        },
        actions: {
            // context 一个与 store 实例具有相同方法和属性的 context 对象
            {
                state,      // 等同于 `store.state`，若在模块中则为局部状态
                rootState,  // 等同于 `store.state`，只存在于模块中
                commit,     // 等同于 `store.commit`
                dispatch,   // 等同于 `store.dispatch`
                getters,    // 等同于 `store.getters`
                rootGetters // 等同于 `store.getters`，只存在于模块中
        	}
    
            _INCREASE(context, payload) {
                context.commit('increase')
        	    setTimeout(() => {commit('increase')},10)
            }
        }
    })
    // this.$store.commit('increment') 
    store.commit('increase', payload)
    store.dispatch({
        type: '_INCREASE',
        key: value
    })
    
    // mapMutations
    methods: {
        ...mapMutations({
            setMuta: 'increase'
        })
    }
    this.setMuta(payload) // this.$store.commit('increase', 10)
    ```
    
  + `mapActions`

    ```js
  methods: {
        ...mapActions([
          'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`
    
          // `mapActions` 也支持载荷：
          'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
        ]),
        ...mapActions({
          add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
        })
      }
    ```
    
    

+ module` 

  + 分割 `store` 不同模块不同管理

    ```js
    const moduleA = {
      state: () => ({ ... }),
      mutations: { ... },
      actions: { ... },
      getters: { ... }
    }
    
    const moduleB = {
      state: () => ({ ... }),
      mutations: { ... },
      actions: { ... }
    }
    
    const store = new Vuex.Store({
      modules: {
        a: moduleA,
        b: moduleB
      }
    })
    
    store.state.a // -> moduleA 的状态
    store.state.b // -> moduleB 的状态
    this.$store // stroe中的所有属性
```
    
    