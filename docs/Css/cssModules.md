`CSS Modules`

+ 局部作用域

+ 模块依赖

  ```js
  // 局部作用域
  import React from 'react';
  import style from './App.css';
  
  export default () => {
    return (
      <h1 className={style.title}> // 构建工具会将 style.title 编译成hash字符串 
        Hello World
      </h1>
  	<h1 className="name"> // 全局name样式类 
        Hello World
      </h1>
    );
  };
  
  // ./App.css
  .title { // 同时编译
    color: red;
  }
  
  :global(.name) { // 全局 不会编译成hash
    color: green;
  }
  
  // webpack.config.js
  {
      test: /\.css$/,
          loader: "style-loader!css-loader?modules" // 开启cssmodules
  },
  ```

+ 选择器之间可以相互继承

  ```css
  .className {
    background-color: blue;
  }
  
  .title {
      // 继承类名 样式复用
    composes: className; 
    color: red;
  }
  ```

+ 模块之间的继承

  ```css
  // 1.css
  .className {
    background-color: blue;
  }
  
  // 2.css
  .title {
    composes: className from './1.css';
    color: red;
  }
  ```

  