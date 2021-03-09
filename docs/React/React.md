#### `React`

+ 构建用户页面的 `UI` 库

+ 轻量、高效

+ 虚拟 `dom` `diff` 算法

+ 单向数据流 

+ 使用 `typescript`

  ```sh
  create-react-app my-app --scripts-version=react-scripts-ts
  ```

  

#### `render` 满足的条件

+ `React` 创建 `Fiber` 树，每个组件对应的 `fiber` 都是通过如下两个逻辑之一创建的：

  + `render` : 调用 `render` 函数，根据返回的 `JSX` 创建新的 `fiber`

  + `bailout` : 满足一定条件时，`React` 判断组件在更新前后没有发生变化，则复用该组件在上一次更新的 `fiber` 作为本次更新的 `fiber`

    + `bailout` 需要满足的条件

      ```js
      // oldProps === newProps ？
      本次更新的 props 不等于 上次更新的 props render 的返回结果实际上是 React.createElement 的执行结果，一个包含 props 属性的对象， 更新后的React.createElement 的执行结果是一个全新的 props引用 oldProps !== newProps
      如果使用 PureComponent 或 Memo 在判断进入 render还是bailout时。不会进行全等比较，而是对props内的每个属性进行浅比较
      // context没有变化
      context 的 value 没有变化
      // workInProgress.type === current.type ？
      更新前后的fiber.type是否变化 ex: div标签 变成 p标签
      // !includesSomeLane(renderLanes, updateLanes) ？
      当前fiber上是否存在更新，如果存在那么更新的优先级是否和本次整棵fiber树调度的优先级一致？
      ```

+ `Child` 是否会 `render`

  ```js
  function Child() {
    console.log('child render')
    return <div>Child</div>
  }
  
  function Parent(props) {
    const [count, setCount] = useState(0)
    return (
      <div onClick={() => setCount(count + 1)}>
        count: {count}
  	  {/* {props.children} */}
        <Child /> // 直接引入 Child 组件 会重新渲染 child render React.createElement(Son, null) oldProps !== newProps 进入render逻辑 props.children是Son对应的JSX，而这里的props是App fiber走bailout逻辑后返回的。所以Son对应的JSX与上次更新时一致，JSX中保存的props也就一致
      </div>
    )
  }
  
  function App() {
    return (
      <div className="App">
        <Parent>
          <Child />
        </Parent>
      </div>
    );
  }
  
  // 点击时 Child不会打印 进入 bailout 逻辑满足上述条件
  ```

#### `React 16 & React 17`

+ 底层附加事件处理方式
+ 在 React 16 或更早版本中，React 会对大多数事件执行 `document.addEventListener()`。React 17 将会在底层调用 `rootNode.addEventListener()`。

#### `React-dom`

+ 将元素和它的子元素之前的状态进行比较 必要的更新`dom`

#### `input dom` 事件类型

+ `react` 事件类型

  ```tsx
  function handleEvent(e: FormEvent<HTMLInputElement>) {
      console.log(e.currentTarget.value)
      const currentVal = e.currentTarget.value
      setVal(currentVal)
  }
    
  function handleDiv(e: SyntheticEvent<HTMLElement>) {
      console.log(e.target)
  }
  ```



#### `PureComponent memo`

+ 组件`props` 的浅比较决定组件是否需要重新render
+ `hooks` 配合 `memo` 缓存组件 `useCallback useMemo`

