#### `Redux` 

+ `react` 的状态管理库

  ```jsx
  // action dispatch reducer
  // dispatch 分发 action, action包含 type和payload type的类型在 reducer中通过 swich..case 决定触发
  // reducer纯函数返回state,不能执行副作用 总是返回新的state
  ```

+ 组件如何与 `redux` 连接

  ```jsx
  /*
   * mapStateToProps state映射到组件的props中 订阅store中的数据
   * mapDispatchToProps anciton creators 绑定到props中
   */
  import { connet } from 'react-redux'
  
  export default connet(mapStateToProps, mapDispatchToProps)(MyComponent)
  ```

  

