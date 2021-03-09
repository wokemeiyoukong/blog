#### 变化侦测

+ 数据驱动视图

  ```js
  UI = render(state) 
  ```

+ 变化侦测

  + 数据变更，视图更新
    + `Angular` 脏值检测
    + `React` 对比虚拟 `dom` 
    + `Vue`  `Object.definProperty` 发布订阅

+ `Vue` 响应式原理

  + 组件 `render` 过程中 `touch` 数据

  + 通过 `Object.definProperty` 将 `data` 对象的属性转化为 `getter/setter` ，`getter` 用于依赖(`watcher` 依赖于这个数据)收集，`setter` 派发更新

  + 每个组件实例对应一个渲染 `watcher` ，`setter` 触发时，通知 `watcher` ，数据关联的组件重新渲染

  + `Object.definProperty`

    ```js
    let car = {}
    let val = 3000
    Object.defineProperty(car, 'price', {
      enumerable: true,
      configurable: true,
      get() {
        console.log('访问属性')
        return val
      },
      set(newVal) {
        console.log('属性修改')
        val = newVal
      }
    })
    car.price // 访问属性
    car.price = 5000 // 属性修改
    
    // 响应式数据原理
    function defineReactive (
      obj: Object,
      key: string,
      val: any,
      customSetter?: ?Function,
      shallow?: boolean
    ) {
      // !实例化一个依赖管理器，存储相关依赖 dep.subs 存储依赖了当前key的一系列对象
      const dep = new Dep()
    
      // !获取obj的属性描述符
      const property = Object.getOwnPropertyDescriptor(obj, key)
      // !属性描述符configurable为false时 直接return
      if (property && property.configurable === false) {
        return
      }
    
      // cater for pre-defined getter/setters
      // !获取属性吗描述符中的get set方法
      const getter = property && property.get
      const setter = property && property.set
      // !val 未传入时 !getter 或 setter arguments.length = 2
      if ((!getter || setter) && arguments.length === 2) {
        val = obj[key]
      }
    
      // !深层对象创建响应式属性 子对象递归调用observe
      let childOb = !shallow && observe(val)
      Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get: function reactiveGetter () {
          const value = getter ? getter.call(obj) : val
          // !Dep.target 渲染watcher
          if (Dep.target) {
            // !添加依赖 watcher
            dep.depend()   // !依赖当前数据的对象添加到依赖数组 this.subs中
            // !嵌套对象添加依赖
            if (childOb) {
              childOb.dep.depend() // !同一个watcher实例加入到两个depend中一个是本身闭包的depend 一个是子元素的
              // !嵌套对象是数组 添加依赖
              if (Array.isArray(value)) {
                dependArray(value) // !对数组的成员进行依赖收集
              }
            }
          }
          return value
        },
        set: function reactiveSetter(newVal) {
          // !
          const value = getter ? getter.call(obj) : val
          /* eslint-disable no-self-compare */
          // !set新值与旧值相同直接返回
          if (newVal === value || (newVal !== newVal && value !== value)) {
            return
          }
          /* eslint-enable no-self-compare */
          if (process.env.NODE_ENV !== 'production' && customSetter) {
            customSetter()
          }
          // #7981: for accessor properties without setter
          if (getter && !setter) return
          // !执行自定义的setter
          if (setter) {
            setter.call(obj, newVal)
          } else {
            // !赋值
            val = newVal
          }
          // !嵌套对象重新创建响应式
          childOb = !shallow && observe(newVal)
          // !依赖更新
          dep.notify() // !对应 key的value 发生变化 通知 subs中的watcher执行update
        }
      })
    }
    ```

  

  + `Dep` 

    ```js
    class Dep {
      // !静态属性target 全局唯一的 watcher 同一时间只能有一个全局的Watcher被计算
      static target: ?Watcher;
      id: number;
      // !sub 存储Watcher的数组
      subs: Array<Watcher>;
    
      constructor () {
        this.id = uid++
        this.subs = []
      }
    
      // !添加依赖对象
      addSub (sub: Watcher) {
        this.subs.push(sub)
      }
    
      // !删除依赖 watcher
      removeSub (sub: Watcher) {
        remove(this.subs, sub)
      }
    
      // !依赖收集
      depend () {
        if (Dep.target) {
          // ! this -> Wtacher 类中的get方法获取到的 window.target(Watcher本身)
          Dep.target.addDep(this) // !-> dep.addSub
        }
      }
    
      // !依赖更新
      notify () {
        // stabilize the subscriber list first
        const subs = this.subs.slice() // !watchers 数组
        if (process.env.NODE_ENV !== 'production' && !config.async) {
          // subs aren't sorted in scheduler if not running async
          // we need to sort them now to make sure they fire in correct
          // order
          subs.sort((a, b) => a.id - b.id)
        }
        for (let i = 0, l = subs.length; i < l; i++) {
          // !watcher 更新
          subs[i].update()
        }
      }
    }
    ```

  + `watcher`

    ```js
    class Watcher {
      vm: Component;
      expression: string;
      cb: Function;
      id: number;
      deep: boolean;
      user: boolean;
      lazy: boolean;
      sync: boolean;
      dirty: boolean;
      active: boolean;
      deps: Array<Dep>;
      newDeps: Array<Dep>;
      depIds: SimpleSet;
      newDepIds: SimpleSet;
      before: ?Function;
      getter: Function;
      value: any;
    
      constructor (
        vm: Component,
        expOrFn: string | Function, // !函数和数据路径 函数->执行函数 数据路径 -> 获取数据 触发getter 添加对应依赖 数据改变时触发update callback
        cb: Function,
        options?: ?Object, // !计算watcher
        isRenderWatcher?: boolean
      ) {
        this.vm = vm
        // !当前watcher 赋值到渲染watcher(_watcher)
        if (isRenderWatcher) {
          vm._watcher = this
        }
        // !当前 组件vm实例添加到_watchers中
        vm._watchers.push(this)
        // options
        if (options) {
          this.deep = !!options.deep  // !深度监听
          this.user = !!options.user // !用户定义的watch对象
          this.lazy = !!options.lazy // !计算属性watcher
          this.sync = !!options.sync // !同步的watcher
          this.before = options.before
        } else {
          this.deep = this.user = this.lazy = this.sync = false
        }
        this.cb = cb
        this.id = ++uid // uid for batching
        this.active = true
        this.dirty = this.lazy // for lazy watchers // !计算属性缓存 true时重新计算 否则缓存处理
    
        // !Dep 相关的属性
        this.deps = [] // !上一次添加的Dep实例的数组
        this.newDeps = [] // !新添加的Dep实例数组
        this.depIds = new Set()
        this.newDepIds = new Set()
    
        this.expression = process.env.NODE_ENV !== 'production'
          ? expOrFn.toString()
          : ''
        // parse expression for getter
        if (typeof expOrFn === 'function') {
          // ! 初始化渲染watcher 时 this.getter updateComponent vm._update(vm._render(), hydrating)
          this.getter = expOrFn // !表达式解析为 getter函数
        } else {
          this.getter = parsePath(expOrFn)
          if (!this.getter) {
            this.getter = noop
            process.env.NODE_ENV !== 'production' && warn(
              `Failed watching path: "${expOrFn}" ` +
              'Watcher only accepts simple dot-delimited paths. ' +
              'For full control, use a function instead.',
              vm
            )
          }
        }
        // !new Watcher -> this.get() computedWatcher this.lazy = true | this.value = undefined
        this.value = this.lazy
          ? undefined
          : this.get()
      }
    
      /**
       * Evaluate the getter, and re-collect dependencies.
       */
      get () {
        pushTarget(this) // !Dep.target = 当前的渲染watcher 并压入栈中 | computedWatcher -> 压入栈顶
        let value
        const vm = this.vm
        try {
          // !this.getter.call(vm, vm) 执行updateComponent 方法 vm._update(vm._render(), hydrating) 触发组件重新渲染 vm._render() 生产渲染vNode 访问vm上的数据 触发数据对象的getter -> dep.depend() -> Dep.target.addDep(this) 当前实例添加到渲染watcher subs数组中
          value = this.getter.call(vm, vm) // !获取被依赖的数据 触发getter 触发depend 依赖
        } catch (e) {
          // !
          if (this.user) {
            handleError(e, vm, `getter for watcher "${this.expression}"`)
          } else {
            throw e
          }
        } finally {
          // "touch" every property so they are all tracked as
          // dependencies for deep watching
          // !deep watcher 深度监听 嵌套对象监听
          if (this.deep) {
            traverse(value)
          }
          // !Dep.target.pop() 恢复上一个状态 | 恢复为渲染watcher
          popTarget()
          // !清空依赖
          this.cleanupDeps()
        }
        return value
      }
    
      /**
       * Add a dependency to this directive.
       */
      addDep (dep: Dep) {
        const id = dep.id
        if (!this.newDepIds.has(id)) {
          this.newDepIds.add(id)
          this.newDeps.push(dep)
          if (!this.depIds.has(id)) {
            dep.addSub(this)
          }
        }
      }
    
      /**
       * Clean up for dependency collection.
       */
      // !清空依赖收集 数据变化 重新render -> 触发getter 重新收集依赖 两个实例数组 防止重新触发
      cleanupDeps () {
        let i = this.deps.length
        while (i--) {
          const dep = this.deps[i]
          if (!this.newDepIds.has(dep.id)) {
            // !移除dep.subs中 Watcher 的订阅 订阅完新的依赖 移除旧的依赖 避免视图渲染通知 不显示的数据render
            dep.removeSub(this)
          }
        }
        // !this.deps this.newDeps 互换
        let tmp = this.depIds
        this.depIds = this.newDepIds
        this.newDepIds = tmp
        // !清空新添加的实例数组
        this.newDepIds.clear()
        tmp = this.deps
        this.deps = this.newDeps
        this.newDeps = tmp
        this.newDeps.length = 0
      }
    
      /**
       * Subscriber interface.
       * Will be called when a dependency changes.
       */
      // ! 渲染watcher更新
      update () {
        /* istanbul ignore else */
        if (this.lazy) {
          this.dirty = true // !计算属性需要更新
        } else if (this.sync) {
          // !同步时直接执行渲染视图
          this.run()
        } else {
          // !watcher只会添加一次 统一更新 异步添加到观察者队列 下一个tick中调用
          queueWatcher(this)
        }
      }
    
      /**
       * Scheduler job interface.
       * Will be called by the scheduler.
       */
      // !user watcher执行
      run () {
        if (this.active) {
          // !获取新值
          const value = this.get()
          // !新旧值不等 新值是对象 this.deep 执行watcher回调
          if (
            value !== this.value ||
            // Deep watchers and watchers on Object/Arrays should fire even
            // when the value is the same, because the value may
            // have mutated.
            isObject(value) ||
            this.deep
          ) {
            // set new value
            const oldValue = this.value
            this.value = value
            // !user watcher 相关 用户自定义watcher
            if (this.user) {
              try {
                // !执行user watcher 用户设置的回调函数
                this.cb.call(this.vm, value, oldValue)
              } catch (e) {
                handleError(e, this.vm, `callback for watcher "${this.expression}"`)
              }
            } else {
              // !渲染watcher 执行getter
              this.cb.call(this.vm, value, oldValue)
            }
          }
        }
      }
    
      /**
       * Evaluate the value of the watcher.
       * This only gets called for lazy watchers.
       */
      // !计算属性的 方法
      evaluate () {
        this.value = this.get() // !useDef 的函数执行后的结果设置 this.getter.call(vm, vm)  触发useDef getter函数
        this.dirty = false // !设置缓存标识
      }
    
      /**
       * Depend on all deps collected by this watcher.
       */
      depend () {
        let i = this.deps.length
        while (i--) {
          this.deps[i].depend()
        }
      }
    
      /**
       * Remove self from all dependencies' subscriber list.
       */
      // !将自身从所有的依赖数组中移除
      teardown () {
        if (this.active) {
          // remove self from vm's watcher list
          // this is a somewhat expensive operation so we skip it
          // if the vm is being destroyed.
          if (!this.vm._isBeingDestroyed) {
            remove(this.vm._watchers, this)
          }
          let i = this.deps.length
          // !依赖数组中移除自身watcher实例
          while (i--) {
            this.deps[i].removeSub(this)
          }
          this.active = false
        }
      }
    }
    
    ```

    