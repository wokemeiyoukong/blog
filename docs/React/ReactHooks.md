#### 类组件的三大问题

+ 状态逻辑难复用
+ 趋向复杂难维护
+ `this` 指向问题

#### Hooks

+ 函数内部的最外层使用 `Hook` 不要在循环、条件判断或者子函数中使用

+ 实现机制：链表 `hook` 链表 `effects` 链表 顶部的 `hook`

+ 组件复用逻辑复用，自定义的 `hooks` 逻辑复用

  <hr>

+ `useState`

  + 函数式组件中使用 `setState` 和 `state`

    ```js
    const [state, setState] = useState(initial) // 返回一个数组 解构赋值
    // 设置初始值 初始值只在第一次有效
    // state 变量 setState 修改状态的setter函数 initial 初始值
    // 类组件中的 this.setState 是新旧state的合并 useState 是直接替换 两个对象指向的内存地址不同新生成对象
    
    // useState 
    let memoizedStates = []
    let index = 0
    function useSatae(initialState) {
        // 初始化state的值
        memoizedStates[index] = memoizedStates[index] || initialState
        let currentIndex = index
        // setState 传入的值 直接替换
        function setState(newState) {
            memoizedStates[currentIndex] = newState
            render()
        }
        return [ memoizedStates[index++], setState]
    }
    function render() {
        index = 0
        ReactDOM.render(<Counter />, document.getElementById('root'))
    }
    
    const Test = () => {
        const [ count, setCount ] = useState(0)
        // [] 只执行一次 
        useEffect(() => {
            setCount(100)
        }, [])
        return (<>{count}</>)
    }
    ```
    
  + `class state` 的区别

    ```js
    // function state 保存的快照（闭包）
    export default function(props: any) {
      const [ count, setCount ] = useState(0)
    
      const increase = () => {
        setTimeout(() => {
            // 1s内的点击只会增加一次 
            // setCount重新执行当前函数 count通过闭包获取
          setCount(count + 1)
        }, 1000)
      }
    
      return (
        <div className={styles.wrap}>
          <button onClick={increase}>{count}</button>
          <CountIn />
        </div>
      )
    }
    // class state 最新值的引用
    class CountIn extends Component {
      state = {
        count: 0
      }
    
      increament = () => {
        setTimeout(() => {
          this.setState({
            count: this.state.count + 1
          })
            // count 通过this.state 引用 每次都可以拿到最新值
          console.log(this.state.count) // 依次打印 12345
        }, 1000);
      }
    
      render() {
        return <h1 onClick={this.increament}>{this.state.count}</h1>
      }
    }
    ```

    

+ `useEffect`

  + 函数组件中使用生命周期的概念 执行副作用操作（请求异步数据、设置订阅、修改组件中的`Dom`）

  + `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合

    ```js
    useEffect(() => {
        
    },[])
    
    // []空数组表示依赖为空 effect只执行一次
    // 无第二个参数表示每次渲染都执行
    // [name] 对应属性变化时执行effect
    // 函数中可以有多个useEffect按顺序执行
    // Effect 在组件渲染之后执行 return销毁前一个副作用函数
    // 约定返回一个空函数或者清除函数
    // 组件渲染之后执行
    
    useEffect(() => {
        const subscription = 订阅全国人民吃饭的情报！
        return () => {
            取消订阅全国人民吃饭的情报！
        }
    },[]) // return 取消订阅 防止bug
    
    // componentDidMount 参数为空数组是 组件渲染依次
    // componentDidUpdate 组件更新完成 依赖改动 初次渲染
    // componentWillUnmount 组件卸载 清除副作用
    // useLayoutEffect 组件渲染之前执行
    
    // useEffect 调度effect不会阻塞浏览器更新屏幕 componentDidMount componentDidUpdate 会阻塞浏览器更新屏幕
    
    // useEffect 返回清理函数 | undefined 
    useEffect(async () => { // return Promise
      const user = await getUser()
      setUser(user)
    }, [])
    
    useEffect(() => {
        const fetchData = async () => {
            const user = await getUser()
            setUser(user)
        }
    }, [])
    
    
    // render之后执行 特定页面下闪烁的问题
    React.useEffect(() => {
        if (val === 0) {
          setVal(10 + Math.random() * 200)
        }
      }, [val])
    // 阻塞渲染
      React.useLayoutEffect(() => {
        if (val === 0) {
          setVal(10 + Math.random() * 200)
        }
      }, [val])
    
    ```

  <hr>

+ `useRef`

  + 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的 ref 对象在组件的整个生命周期内保持不变。

  + 可用于访问 `dom` 节点或 `React` 组件实例和作为容器保存可变对象

    ```js
    const countRef = useRef(0)
    
    const Parent = () => {
      const numRef = useRef(null)
      useEffect(() => {
        console.log(numRef) // {current: p} ref对象
      }, [])
      return (
        <>
          <p ref={numRef}>1</p>
        </>
      )
    }
    
    // createRef 每次渲染都会返回一个新的引用 useRef 返回相同的引用
    
    
    //组件实例 组件实例是对于类组件来说的 函数组件没有实例
    // 获取子组件的ref 子组件为类组件 useImperativeHandle
    
    class Child2 extends Component {
      
      render() {
        const { title } = this.props
        return (
          <>
            <button>{title}</button>
          </>
        )
      }
    }
    
    const Parent = () => {
      const [num, setNum] = useState(0)
      const numRef = useRef(null)
      const Child2Ref = useRef(null)
      useEffect(() => {
        console.log(numRef)
        console.log(Child2Ref)
      }, [])
      return (
        <>
          <p ref={numRef}>{num}</p>
          <Child2  ref={Child2Ref} title='child2标题'/>
        </>
      )
    }
    ```

  <hr>

+ `useMemo`

  + 返回一个 `memoized` 的值

    ```js
    const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
    
    // 创建函数和依赖项数组作为参数掺入 useMemo 仅会在依赖项改变时才重新计算 memoized的值 这种优化有助于避免在每次渲染时都进行高开销的计算
    
    // 没有传入依赖 每次渲染都会计算新的值
    ```

  <hr>

+ `useCallback`

  + 缓存可反复执行的函数

  + 依赖数组改变时使用 `Object.is()` 进行比较 非原始类型时，每次改变内存地址都会变化 导致 `Effect` 每次都触发

    ```js
    const onChange = useCallback((e)=>{
            setText(e.target.value)
       },[])
    
    // 第二个参数作为依赖的更改项
    ```

  <hr>

+ `useReducer`

  + `redux`中的`reducer` 提取

    ```js
    function  ReducerDemo() {
      const [count, dispatch] = useReducer((state, action) => {
        switch (action) {
          case 'add':
            return state + 1
          case 'minus':
            return state - 1
          default:
            return state
        }
      }, 0) // 第二个参数味对应count的初始化的值
    
      return (
        <div>
          <h1>{count}</h1>
          <button onClick={ () => dispatch('add') }>increment</button>
          <button onClick={ () => dispatch('minus') }>decrement</button>
        </div>
      )
    }
    ```

  <hr>

+ `useContext`

  + 创建一个上下文对象

  + 接收一个 context 对象（`React.createContext` 的返回值）并返回该 context 的当前值。当前的 context 值由上层组件中距离当前组件最近的 `MyContext.Provider` 的 `value` prop 决定

    ```js
    const value = useContext(MyContext);
    
    <Context.Provider value={getContext()}>
       {props.children}
    </Context.Provider>
    // 调用了 useContext 的组件总会在 context 值变化时重新渲染
    
    // 多层组件传值 同过createContext 创建一个上下文 通过上下文的Provider 提供参数进行多层级传递 通过 useContext(CenterContext) 获取创建的上下文中包含的参数
    ```

#### 函数式组件

+ 函数返回值是一个 `React Element`

+ 每一次渲染都是独立的

+ `hooks` 之前函数式组件 没有实例 没有生命周期

  ```js
  const element = (props) => {
      // props 为参数
      return (
      	<h1>标题</h1>
      )
  }
  ```

#### `HOC` 高阶组件

+ 接受一个组件作为参数，返回一个组件，额外增加一些功能

  ```js
  const func = () => {
      console.log('func')
  }
  
  const wrap = (func) => {
      console.log('wrap')
      return func
  }
  
  wrap(func)() // 高阶组件
  ```

#### `Render Props` 

+ 函数传递一个回调函数作为参数，该回调函数就能利用外面函数的执行结果做为参数

  ```js
  const func = param => {
    console.log("func");
  };
  
  const wrap = (param, func) => {
    console.log("wrap");
    func(param);
  };
  
  // wrap 逻辑已被复用
  wrap("", func);
  ```




#### `React.memo`

+ 缓存组件，`props` 不变的情况下，被包裹的组件不会重新 `render`

  ```jsx
  // 组件缓存
  export default React.memo(Component)
  // React.memo 默认情况只会对参数进行浅比较，支持传入第二个参数，控制对比
  const MyComponent = (props) => {
      
  }
  const isEqual = (prevProps, nextProps) => {
      /*
    如果把 nextProps 传入 render 方法的返回结果与
    将 prevProps 传入 render 方法的返回结果一致则返回 true，
    否则返回 false
    */ 
  }
  
  export default React.memo(MyComponent, isEqual)
  ```

  

  

#### `DvaJS`

+ 

