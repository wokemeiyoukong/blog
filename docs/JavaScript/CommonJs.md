#### `Common.js`

+ `exports`: 包含模块希望提供给其他模块的对象及导出的函数

+ `require`： 参数引入模块的路径

+ 导入的是值的拷贝，动态导入

  ```js
  exports = module.exports
  // ./maths.js
  exports.add = function() {
    console.log('math')
  }
  // ./increament.js
  const add = require('./math').add
  exports.incre = function() {
    console.log('incre')
    return add()
  }
  // ./run.js
  const inc = require('./increament').incre
  console.log(String(inc))
  inc() // incre math
  // * require接受表达式
  ```



#### `AMD/Require.js`

+ 定义模块：`define(id?, dependencies?, factory)`

+ 加载模块：require([module], factory)

  ```js

  // a.js
  // 依赖有三个默认的，即"require", "exports", "module"。顺序个数均可视情况
  // 如果忽略则factory默认此三个传入参数
  // id一般是不传的，默认是文件名
  define(["b", "require", "exports"], function(b, require, exports) {
      console.log("a.js执行");
      console.log(b);
  // 暴露api可以使用exports、module.exports、return
      exports.a = function() {
          return require("b");
      }
  })
  // b.js
  define(function() {
      console.log('b.js执行');
      console.log(require);
      console.log(exports);
      console.log(module);
      return 'b';
  })
  // index.js
  // 支持Modules/Wrappings写法，注意dependencies得是空的，且factory参数不可空
  define(function(require, exports, module) {
      console.log('index.js执行');
      var a = require('a');
      var b = require('b');
  })
  // index.js
  require(['a', 'b'], function(a, b) {
      console.log('index.js执行');
  })
  ```

  

#### `EsModule`

+ `export`

+ `import`： 值的引用

  ```js
  // 编译时加载 静态语法 import编译阶段执行 提升到模块的头部 值的引用
  import('./module').then({a} => {})
  const { a } = await import('./module')
  ```



#### 循环加载

+ `common.js`

  ```js
  // 第一次加载脚本执行脚本 内存中生成一个对象 再次加载缓存中取值 只会在第一次加载时运行
  
  // 循环加载 只输出已执行的部分 未执行的部分不会输出
  
  // a.js
  exports.done = false;
  var b = require('./b.js'); // 同步执行 执行到这 后续代码暂停等待 b执行完毕
  console.log('在 a.js 之中，b.done = %j', b.done);
  exports.done = true;
  console.log('a.js 执行完毕');
  
  // b.js
  exports.done = false;
  var a = require('./a.js'); // a只执行了 exports.done = false 处于等待b执行完毕状态
  console.log('在 b.js 之中，a.done = %j', a.done);
  exports.done = true;
  console.log('b.js 执行完毕');
  
  // main.js
  var a = require('./a.js');
  var b = require('./b.js'); // 不会再次执行b输出缓存中的b的执行结果 exports.done = true
  console.log('在 main.js 之中, a.done=%j, b.done=%j', a.done, b.done);
  ```

  