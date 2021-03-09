#### 原型链

+  `Object.create()` 
  
  + `Object.create(proto[, propertiesObject])`
    + `proto` 新创建对象的原型对象 第二个参数可选
  + 返回新对象 带有 `proto` 的属性
  
+ 原型（`prototype`）

  - 用于实现对象的属性继承 对象，`javascript` 对象都包含一个 `__proto__` 属性

    ```javascript
    const o1 = Object.create(null)
    // o1没有原型(prototype)
    ```

+ 构造函数（`constructor`）

  + 构造函数实例化后，同时会创建实例的属性和方法，一般每个实例的属性都不相同，而方法是函数会被复用

    ```js
  class Person {
        constructor(name) {
        this.name = name
        }
      eat() {
          console.log(this.name)
        }
      }
      const p1 = new Person('fang')
      const p2 = new Person('ming')
    
      console.log(p1.name === p2.name) // false
      console.log(p1.eat === p2.eat) // true
    
    // 方法是函数 得到了复用 p1.eat === p2.eat true
    ```
    
    

  + 可以通过 `new` 关键字创建 **实例** 的函数

  + 内部 `this` 指向生成的实例对象

  + `this` ：构造函数和原型上的方法的`this` 指向实例

    ```javascript
    function Person (name,age) {
        this.name = name
        this.age = age
        this.fn = function say () {
            console.log(`${this.name} is ${this.age} years old`)
        }
    }
    const child = new Person('bob', 22)
    console.log(child.name)
    console.log(child.age)
    console.log(child.fn)
    ```

+ 实例

  + `new Constructor()` 创建的对象 实例通过 `__proto__` 指向原型，通过 `constructor` 指向构造函数
  + `new` 关键字创建实例过程：
    + 通过 `Object.create(fn.prototype)` 创建新对象  `obj`
    
      ```js
    const me = Object.create(obj) // 创建一个基于obj __proto__ 的新对象
      me.__proto__ = obj.__proto__
      ```
    
    + `fn.apply(obj,args)` 绑定 `this` 指向
    
    + 返回新对象（如果构造函数有自己 `return` 时，则返回该值）
  
  ```javascript
  <script>
    function _new (fn,...args) {
      // 创建一个继承自构造函数fn的原型对象
      const obj = Object.create(fn.prototype)
      // 绑定this 将构造函数中的属性和方法绑定
      const result = fn.apply(obj,...args)
      // 判断构造函数func执行完的结果是不是对象类型
      return result instanceof Object? result : obj
  	}
  </script>
  ```


```javascript
<script>
    // new 关键字创建实例 Object 构造函数
    const instance = new Object()
    // 原型 Object.prototype是构造函数拥有的prototype属性
    const prototype = Object.prototype
    // 实例.__proto__ === 原型
    console.log(instance.__proto__ === prototype) // true
	// Object.prototype.__proto__ === null 原型链

	// 原型.constructor === 构造函数
    console.log(prototype.constructor === Object) // true
	// 构造函数.prototype === 原型
    console.log(Object.prototype === prototype) // true
	// 实例.constructor === 构造函数
	console.log(instance.constructor === Object) // true
  </script>
```

#### 继承

```javascript
// 定义继承的对象 this 指向实例
function Person(name,age) {
    this.name = name
    this.age = age
    this.say = function() {
		console.log(`${this.name} is ${this.age}`)
    }
}
Person.prototype.work = function() {
    console.log(this.name)
}
```

+ 原型链继承

  ```javascript
  function Child(name) {}
  Child.prototype = new Person()
  let c = new Child()
  // 所有属性被共享，不能传递参数 修改一个实例的属性其他实例都会受到影响
  ```

+ 构造函数继承（对象冒充）

  ```javascript
  function Child(name,age) {
      Person.call(this,name,age)
  }
  let c = new Child('naem','age')
  // 可以传递参数 浪费内存 只能继承父类中this声明的属性/方法，不能继承父类prototype上的属性/方法
  ```

+ 组合继承（原型链+对象冒充）

  ```javascript
  function Child(name,age) {
     	// 对象冒充 Child中添加Person的属性和方法
      Person.call(this,name,age)
  }
  // Child的原型指向Person的原型
  Child.prototype = new Person()
  // 将Child的构造函数指向Child
  Child.prototype.constructor = Child
  let c = new Child('naem','age')
  ```

+ `es6` 类继承

  + 静态方法只能在原型上调用，不能在实例上调用
  
    ```js
    Array.from() // 转换类数组对象为数组 字符串也可以转换成数组
    Array.of() // 结构一致的数组对象
    // .from() .of() 属于数组的静态方法
    Array.of(1,2) // [1,2]
    new Array(1) // [empty]
    new Array(1,2) // [1,2]
    [1,2].from() // Error 实例不能调用静态方法
    ```
  
  ```javascript
  // 父类 类的内部方法是不能枚举的和 es5 不同
  class Person{  
      constructor(name){
          this.name = name
      }
      say() {
          console.log(this.name)
      }
      // static 静态方法 实例不能调用 类本身可以调用
      static desc() {
          return `i am Person class`
      }
      set github(value){
        this.githubname = value
      }
      get github(){
        return `github user`
      }
  }
  const p = new Person('xx')
  p.github // github user
  p.github = 'woke' // woke
  // 子类
  class Child extends Person{
     constructor(name){
         // 调用父类的构造函数
         // super:子类的this对象得到父类的属性和方法
         super(name)
      } 
  }
  let c = new Child('name')
  
  child.__proto__ === Person
  child.prototype.__proto__ === Person.prototype
  
  // extends 关键字
  // _extends 变量保存function d子类的构造函数 b父类的构造函数
  var __extends = this._extends || function (d, b) {
      // 父类b的属性和方法赋值给子类d
      for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p]
      // __的构造函数 this访问它的原型 __的构造函数被赋值为d子类的构造函数
      function __() { this.constructor = d }
      // __的原型被赋值为父类b的原型
      __.prototype = b.prototype
      // 子类的原型赋值为__的实例
      d.prototype = new __()
  }
  // 生成类的方法
  var Child = (function(_super){
      __extends(Child, _super)
      function Child(name) {
          _super(name)
      }
      return Child
  })(Person)
  ```
  
+ 原型式继承

  ```javascript
  function cloneObj(obj) {
      function F(){}
      // 将被继承的对象作为空函数的prototype
      F.prototype = obj
      // 返回new 创建的新对象
      return new F()
  }
  // 存在篡改的情况不能传递参数
  ```

+ 寄生式继承（继承过程封装）

  ```javascript
  function createAnother(original){
    var clone = cloneObject(original); // 继承一个对象 返回新函数
    // do something 以某种方式来增强对象
    clone.some = function(){}; // 方法
    clone.obkoro1 = '封装继承过程'; // 属性
    return clone; // 返回这个对象
  }
  ```

+ 寄生组合式继承（call + 寄生式继承）

  ```javascript
  function Child() {
      Person.call(this)
  }
  function inherPro(son,father){
      let fatherPrototype = Object.create(father.prototype)
      son.prototype = fatherPrototype
      // 修正constructor指向 
      // constructor的作用：返回创建实例对象Object构造函数的引用
      son.prototype.constructor = son
  }
  inherPro(Child,Person)
  let c = new Child()
  ```

+ `ES5` 和 `ES6` 继承的区别
  + `ES5` ：先创建子类的实例对象this，然后将父类的方法添加到this上
  + `ES6` ：先将父类实例对象的方法和属性加到this上，子类的构造函数再修改this
  
+ `js` 构造函数和 `class`

  + `class` 更贴合面对对象编程的写法
  + `class` 实现继承更加易读和理解
  + 本质还是语法糖，使用`prototype` 

#### 执行上下文

+ `javascript` 执行过程中的对象

  + 变量对象（`VO`）
    + 活动对象（`AO`）
  + 作用域链（词法作用域）
  + `this` 指向

+ 执行过程

  + 创建全局上下文（global EC）
  + 全局执行上下文（caller）逐行自上而下执行，遇到函数时，函数执行上下文（callee） 被 push 到执行栈顶层
  + 函数执行上下文被激活，成为active EC，开始执行函数中的代码，caller 被挂起
  + 函数执行完毕，callee 被 pop 移除执行栈，控制权交还全局上下文（caller） ，继续执行

+ `return` 

  + 可直接终止执行代码的执行，会将当前的上下文直接弹出栈

+ 变量对象

  + 执行上下文中的一部分，存储执行上下文中所有的变量和函数声明（不包括函数表达式）

+ 作用域

  + 词法作用域:函数的作用域在定义时决定的

  + 上下文中声明的变量和声明的作用范围

  + 块级作用域和函数作用域

  + 特性

    + 声明提前：一个声明在函数体内都是可见的，函数优先于变量

    + 非匿名自执行函数，函数变量为只读状态，无法修改

      ```javascript
      let foo = function() { console.log(1) };
      (function foo() {
          foo = 10  // 由于foo在函数中只为可读，因此赋值无效
          console.log(foo)
      }()) 
      
      // 结果打印：  ƒ foo() { foo = 10 ; console.log(foo) }
      ```

+ 作用域链

  - 包含父级和自身的变量对象，通过作用域链访问到父级里声明的变量或者函数

+ 闭包

  + 父函数被回收机制回收后，`return` 的子函数中仍然含有父函数的单变量对象和作用域，可以继续访问

    + 作用：延伸了变量的作用范围
    + 有权访问另一个作用域内部变量的函数 -- 高级程序设计3
+ 退出函数之前将不使用的局部变量全部删除避免性能浪费
    + 多个子函数的 `[[scope]]` 同时指向父级，完全共享的，父级中的变量对象被修改，子函数都会受到影响。
    
    ```javascript
    // 变量声明时如果不使用 var 关键字，那么它就是一个全局变量，即便它在函数内定义
    function f1 () {
    		var n = 999;
           // 未使用var关键字声明 提升至全局变量 匿名函数本身也是一个闭包 可以在外部对函数内部的局部变量进行操作
    		nadd = function () {
    			n += 1
    		}
    		function f2 () {
    			console.log(n)
    		}
       		// f2 始终存在在内存中 f2的存在依赖于f1 导致f1没有在调用结束后被垃圾回收机制回收
    		return f2
    	}
    	var resulet = f1()
    	resulet() // 999
    	// 如果在函数f1外部定义var n =1;结果不会影响 nadd 中的n为全局变量
    	nadd()
    	resulet()// 1000
    ```


#### `Script` 引入方式 延迟脚本

+ `<script>` 立即加载并执行指定的脚本,不等待后续载入的文档元素，读到就加载并执行
+ 外部脚本
  + `<script defer>` 延迟加载，在所有元素解析完成后执行，不确定在``DOMContentLoaded` ` 事件前后执行（高设3不确定执行顺序）
    + `DOMContentLoaded`  监听 `HTML` 文档体加载解释完毕事件
    + `Load` 事件会在在 `DOMContentLoaded` 被触发之后触发
  + `<script async>` 异步加载，加载完就执行，执行时会阻塞元素渲染
  + 通过 `document.createElement` 创建的标签插入默认为 `async` 模式
+ `noscript` 页面不支持脚本时展示的内容

#### `instanceof` 原理

+ 测试一个对象能否在它的原型链中找到构造函数的prototype属性，返回 `true`

#### 类型转换

+ -、*、/、% ：一律转换成数值后计算

  +： 

  - 数字 + 字符串 = 字符串， 运算顺序是从左到右
  - 数字 + 对象， 优先调用对象的`valueOf` （`!string boolean number`） 再调用 `toString`
  - 数字 + `boolean/null` -> 数字 `null+1=1`
  - 数字 + `undefined` -> `NaN` 

  ```javascript
  [1].toString() === '1'
  ({}).toString() === '[object object]'
  NaN !== NaN 、+undefined 为 NaN
  [null,2,3].toString() //  ,2,3  null和undefined 在toString valueOf toLocalSting()方法中为空字符串
  
  if ([]) // true
  	console.log(1);
  if ({}) // true
  	console.log(2);
  if ([] == false) // true [] -> 0 false -> 0 任意值和boolean 相比较 -> 转换Number比较
  	console.log(3);
  if ({} == false) // false
  	console.log(4);
  /**
  * 
  * （1）空数组[]和空对象{}都是object类型，因此直接用于if判断条件时就会被转化为true。
    （2）任意值与布尔值比较，都会将两边的值转化为Number。
      如果将空数组[]与布尔值false比较，false转化为0，而空数组[]也转化为0，因此[] == false的判断得到true。
      如果将空对象{}与布尔值false比较，false转化为0，而空对象{}转化为NaN，由于NaN与任何数都不相等，因此{} == false的判断得到false。
  */
  
  
  // 对象之间转换
  var a = ?
  if(a==1&& a==2&& a==3) {
      console.log('success')
  }
  a = { num:1 ,toString() {return a.num ++}} // 改写toString 方法 每次比较 执行改写的toString 方法 后置操作符 先使用值 再执行操作符
  a = [1,2,3,4] // 数组与 number 比较 调用valueOf() -> toString() -> join('') 方法 改写 join 方法
  var a = [1,2,3,4]
  a.join = a.shift // 每次toString时执行join -> shift 获取数组头部的value
  a == 1 // 1
  a == 2 // 2
  a == 3 // 3 依次执行toString -> join -> shift 方法
  ```

+ 字符串比较大小 `charCodeAt(0)` 转换字符编码大小

  ```js
  'a' > 'b' // false
  'b' < 'c' // true
  'a'.charCodeAt(0) == 97
  'b'.charCodeAt(0) == 98
  
  '321' > '32' 
  '32' > '123'
  '321'.charCodeAt(0) == 51
  '32'.charCodeAt(0) == 51
  '123'.charCodeAt(0) == 49
  ```


#### 类型判断

+ 基本类型 （`string number boolean undefined + function ` 使用 `typeof` 判断即可）

+ `null` 可使用 `String(null)` 

+ `null === null`

+ 其余引用类型可使用 `Object.prototype.toString.call.(variable).slice(8,-1)`

  + `Object` 对象和它的原型链上各自有一个 `toString()` 方法

    ```javascript
    Object.toString() // "function Object() { [native code] }" 函数
    Object.prototype.toString() // "[object Object]" 值类型
    // Array，Function，Date虽然是基于Object进行创建的，但是他们继承的是Object.toString()，而不是Object.prototype.toString()。
    ```

#### 模块化

+ 分类
  + `ES6：import/export`
    + 编译时加载/静态加载
  + `commonjs:require/module.exports/exports`
    + 运行时加载
  + `amd:require/defined`
+ `require` 和 `import` 
  + `require` 支持动态导入，`import` 不支持，正在提案
  + `require` 同步导入，`import` 异步导入
  + `require` 值的拷贝，导出值变化不会影响导入值；`import` 指向内存地址，导入值会随导出值而变化
  + `import` 提升到整个模块的头部
  + `import()` 返回一个 `Promist` 对象

#### 防抖和节流

+ 补充

+ 防抖和节流 执行的都是闭包返回后的函数 标记只在第一次包装函数时存在作用

  ```javascript
  // js中定时器的返回值
  timer = setTimeout(() => {
              fn.apply(this,args)
          },1000)
  // timer 定时器的返回值是一个整型的标识 clearTimeout会以此ID来清除定时器
  ```

+ 防抖

  触发函数执行的间隙时间小于指定时间，函数只执行一次，通过重新设置定时器完成只执行一次目标函数

  + 非立即执行的防抖函数

    ```javascript
    function debounce(fn,wait){
        // 通过闭包缓存一个定时器ID timer
        let timer = null 
        return function(...args) {
            // args 事件对象 清除上一次定时器 不断设置和清除定时器完成防抖
           if(timer) clearTimeout(timer) 
            // wait时间内每次触发事件就设置新的定时器
            timer = setTimeout(() => {
                // timer 整型的ID标识
                // this 事件绑定的结构
                fn.apply(this,args)
            },wait)
        }
    }
    ```

  - 立即执行的防抖函数

    ```javascript
    // 立即执行的防抖函数
    	function debounceimm(fn,wait,immediate) {
    		let timer = null
    		return function(...args) {
    			if(timer) clearTimeout(timer)
    			if(immediate&&!timer){
                    fn.apply(this,args)
                }
                timer = setTimeout(() => {
                    fn.apply(this,args)
                },wait)
    		}
    	}
    ```

+ 节流

  连续触发事件但是在n秒中只执行一次函数，节流会稀释函数的执行频率（每次触发小于既定值，函数节流会每隔这个时间调用一次）

  - 时间戳版

    ```javascript
    // 节流函数时间戳
    	function throttle(func,wait) {
         	// 闭包缓存previous
    		let previous = 0
    		return function(...args) {
    			let now = Date.now()
                // 两次执行时间的间隔与指定时间进行比较
    			if(now - previous > wait) {
    				func.apply(this,args)
    				previous = now
    			}
    		}
    	}
    ```

  - 定时器版

    ```javascript
    // 节流函数定时器
    	function throttletime(func,wait) {
    		let timeout
            // 通过定时器来确定函数执行次数
    		return function(...args) {
    			if(!timeout) {
    				timeout = setTimeout(() => {
    					timeout = null
    					func.apply(this,args)
    				},wait)
    			}
    		}
    	}
    ```
  
  防抖是将多次执行变为最后一次执行，节流是将多次执行变为每隔一段时间执行

#### 递归函数

+ 函数内部调用自身的函数

  ```js
  // 1*2*3*4*n 的阶乘
      function fn(n) {
        if (n === 1) {
          return 1
        }
        return n * fn(n - 1)
      }
      console.log(fn(3))
  // 菲波那切数列 1,1,2,3,5,8,13,21...求指定位置的值
      function fei(n) {
        if (n === 1 || n === 2) {
          return 1
        }
        return fei(n - 1) + fei(n - 2)
      }
      console.log(fei(8))
  // 深层遍历
  let data = [{
        id: 1,
        name: 'fruit',
        classify: [{
          id: 11,
          name: 'peach'},{
          id: 22,
          name: 'apple'
        }]
      }, {
        id: 2,
        name: 'water'
      }]
      function search(data, id) {
        data.forEach(function(item){
          if(item.id === id) {
            console.log(item)
          } else if(item.classify && item.classify.length > 0) {
            search(item.classify,id)
          }
        })
      }
  ```

#### 函数执行时的this

+ this：指向与函数运行时的上下文有关，当前执行代码的环境对象

  + `window`

  + 严格模式下为 `undefined` 

  + 调用对象

  + this 永远指向最后调用它的那个对象

    ```javascript
    	let name = 'window'
    	let fna = {
    		name: 'innera',
    		fun1: function() {
    			console.log(this.name)
    		},
    		fun2: function() {
    			setTimeout( function() {
    				this.fun1()
    			},100)
    		}
    	}
    	fna.fun2() 
    	// fna.fun2() 返回 this.fun1() 此时的执行环境为window
    	// this.fun1 is not a function
    	// 最后调用时的对象是 window 
    ```

+ 箭头函数

  + 当前的词法作用域覆盖了`this` 本来的值，作用域链的上一层继承`this` 
  
  ```javascript
      let name = 'window'
      let fna = {
          name: 'innera',
          fun1: function() {
              console.log(this.name)
          },
          fun2: function() { 
              // this指向执行时的上下文环境 指向fna这个对象
              setTimeout( () => {
                  this.fun1()
              },100)
          }
      }
      fna.fun2() // innera
  ```
  + 改变函数运行时上下文(this指向)（call，apply, bind）
  
    ```javascript
     let a = {
          name:'a'
      }
      getVal(name) {
          console.log(this.name)
      }
    ```
    
  + 手写 `call` 方法
  
    + `Function.prototype`
    + 接受的参数从第二个开始
    + 函数内部的 `this` 指向该对象
    
    ```javascript
    // Function.prototype 函数的原型上
    Function.prototype.mycall = function(context) {
    		// context 绑定this的目标 
    		var context = context || window
    		// this 调用context的函数 函数内部的 `this` 指向该对象
    	 	context.fn = this		
    	 	// 获取参数 接受的参数从第二个开始
    	    var args = [...arguments].slice(1)
    	    // 保存执行的结果
    	    var result = context.fn(...args)
    	    // 删除context.fn
    	    delete context.fn
    	    return result
    	}
    ```
  
  + 手写 `apply` 方法
  
    ```javascript
    Function.prototype.myapply = function(context) {
    		// context 绑定this的目标 
    		var context = context || window
    		// this 调用context的函数
    	 	context.fn = this		
    	 	// 获取参数
        	var result
            // 判断是否传参
    	    if(arguments[1]) {
                result = context.fn(...arguments[1])
            }else {
                result = context.fn()
            }
    	    delete context.fn
    	    return result
    ```
  
  - 手写 `bind` 方法
  
      ```js
  let btn = document.querySeletor('button')
    btn.onclick = function() {
      this.disabled = true
        setTimeout(() => {
            console.log(this) // btn
           this.disabled = false 
        }, 3000)
        setTimeout( function(){
            console.log(this) // window
            this.disabled = false
          },2000)
        let _this = this // 保存this 对象
          setTimeout( function(){
            console.log(_this) // window
            _this.disabled = false
          },2000)
        setTimeout( function(){
            console.log(this) // btn
            this.disabled = false
          }.bind(this),2000) // bind 绑定this
    }
    ```
    
    - 返回一个和原函数具有相同函数体和作用域的新函数
    
    ```js
    // bind会创建一个函数的实例
    Function.prototype.mybind = function(context) {
    		if(typeof this !== 'function') {
    			throw new TypeError('Error')
    		}
    		var _this = this
    		var args = [...arguments].slice(1)
    		return function F() {
    			if(this instanceof F) {
    				return new _this(...args,...arguments)
    			}
    			return _this.apply(context,args.concat(...arguments))
    		}
    	}
    ```

#### 函数柯里化

+ 在一个函数中，首先填充几个参数，然后再返回一个新的函数的技术，称为函数的柯里化。

  ```javascript
  const add = function add(x) {
  	return function (y) {
  		return x + y
  	}
  }
  const add1 = add(1)
  add1(2) === 3
  add1(20) === 21
  // 初步封装
  var currying = function(fn) {
      // args 获取第一个方法内的全部参数
      var args = Array.prototype.slice.call(arguments, 1)
      return function() {
          // 将后面方法里的全部参数和args进行合并
          var newArgs = args.concat(Array.prototype.slice.call(arguments))
          // 把合并后的参数通过apply作为fn的参数并执行
          return fn.apply(this, newArgs)
      }
  }
  
  // 支持多参数传递
  function progressCurrying(fn, args) {
      var _this = this
      var len = fn.length;
      var args = args || [];
      return function() {
          var _args = Array.prototype.slice.call(arguments);
          Array.prototype.push.apply(args, _args);
          // 如果参数个数小于最初的fn.length，则递归调用，继续收集参数
          if (_args.length < len) {
              return progressCurrying.call(_this, fn, _args);
          }
          // 参数收集完毕，则执行fn
          return fn.apply(this, _args);
      }
  }
  ```




#### 字符串方法

+ `indexOf` : 提取字符串的位置并返回，没有返回 -1
+ `slice` : 提取相关位置的字符串并返回，不改变原字符串
+ `substr`: 截取指定长度位置的字符
+ `substring`: 截取开始索引到结束位置的字符串（末尾不截取）


#### 栈和堆

+ 内存中分为栈区（`stack`）和堆区（`heap`）

+ 简单数据类型
  1. 栈中开辟空间存储
  2. 数据改变内存中的数据改变
  3. 赋值会新开辟一块空间，存放数据
  4. 两个数据互相独立不影响
+ 复杂数据类型
  	1. 对象时会在堆区开辟空间存储数据，通过栈中的指针（地址）指向堆区，变量存储在栈区，数据存在堆区
   2. 赋值给另一个新对象时，就是把栈区中的地址传递给新对象
   3. 两个对象同时共享堆区中的同一块空间，修改其中一个会相互影响
   4. 重新赋值就会产生新的地址和空间，互不影响
#### `let const`

+ `const` 需要初始化，只读

```js
// 暂时性死区 let const 声明之前使用不可使用
// const 指向变量声明的地址 地址是不可变的 声明对象时不可变的是这个地址，对象本身是可以改变的
// 理解为 声明的变量在栈中的地址不可变，定义对象时，地址的指针指向变量存储的内存空间，可以赋值
const foo = {}
foo.a = 1
foo = {a:1} // 报错 改变了变量的地址
```

#### 变量

+ 函数中定义变量没有添加关键字变量必须在函数执行一次后才会变为全局变量

+ 函数声明的变量提升高于普通变量提升

  ```javascript
  function sayhello () {
  		msg = 'hello'
  		console.log(msg)
  	}
  	sayhello()
  	alert(msg) // sayhello 未执行时 msg 不是全局变量访问不到
  
  ```

####  数据类型

+ `typeof` 操作符

  ```javascript
  // 判断正则表达式时 返回 object
  let reg = /^w+/
  console.log(typeof reg)
  // 判断null 返回object 可以看做null是对空对象的引用 原理是null在底层的二进制开头为0b开始 错误的判断为object
  ```

+ `NaN`  
  + 非数值 与任何值都不相等包括自身
  + `isNaN` 判断参数是否为数值，判断对象是先执行对象的 `valueOf()` 方法，再执行 `toString()` 方法

#### 一元操作符

+ 前置操作符

  + 会在执行表达式求值**之前**对值进行操作

  ```javascript
  var age = 29;
  var anotherAge = --age + 2; // age 先减1 再 + 2
  alert(age); // 输出 28
  alert(anotherAge); // 输出 30 
  ```

+ 后置操作符

  + 表达式求值之后对值进行操作

  ```javascript
  var num1 = 2;
  var num2 = 20;
  var num3 = num1-- + num2; // 等于 22 先+num2 再减1
  var num4 = num1 + num2; // 等于 21 
  ```

+ 不同类型数据使用一元操作符

  ```javascript
  var s1 = "2";
  var s2 = "z";
  var b = false;
  var f = 1.1;
  var o = {
   valueOf: function() {
   return -1;
   }
  };
  s1++; // 值变成数值 3
  s2++; // 值变成 NaN
  b++; // 值变成数值 1
  f--; // 值变成 0.10000000000000009（由于浮点舍入错误所致）
  o--; // 值变成数值-2 
  ```

#### 运行时

+ `runtime` 
  + 堆（`heap`）：内存存储空间，堆中保存了所有正在被使用的变量和对象和没有被垃圾回收机制回收的帧。
  +  栈（`stack`）：执行时所有的帧，栈的数据结构为一个后进先出的对象集合
  + 队列（`queue`）：包含一个待执行信息的列表，栈为空时队列中的信息会被取出并处理
  + 帧（`frame`）：一个函数被执行时会在栈中生成一个帧，保存了特殊函数的参数和局部变量，函数返回时，帧从栈中推出。帧堆满栈中出现栈溢出错误

#### 正则表达式

+ ```js
  let reg = /\{\{(\w+)\}\}/;
      let template = '我是{{name}}，年龄{{age}}，性别{{sex}}';
      let data = {
        name: '姓名',
        age: 18
      }
      // test 正则匹配的结果
      let a = reg.test(template)
      // exec 返回正则匹配的结果 数组/null
      let b = reg.exec(template)[1]
      // replace 替换匹配的第一个参数
      let c = template.replace(reg, b)
      // RegExp.$1 正则分组匹配的结果 name
      console.log(a,RegExp.$1,RegExp.$2)
      console.log(b)
      console.log(c)
      function render(template, data) {
        if(reg.test(template)) {
          const name = reg.exec(template)[1]
          template = template.replace(reg, name)
          return render(template, data)
        }
        return template
      }
      let result = render(template, data)
      console.log(result)
  ```

  

#### `proxy` 

+ 代理操作，拦截（`object.definproperty`）

  ```js
  const preson = {
        name: 'wokemeiyoukong',
        age: 12
      }
      const personProxy = new Proxy(preson, {
        get(target, key) {
          console.log('get --- proxy')
          return target[key].toUpperCase()
        },
        set(target, key, value) {
          console.log('set --- proxy')
          return target[key] = value.trim()
        }
      })
      console.log(personProxy.name) // get方法 
  	personProxy.name = '    i love you   ' // set I LOVE YOU
  
  // 
  const handler = {
        set(target,key,value) {
          target[key] = value.match(/[0-9]/g).join('')
        },
        get(target,key){
          return target[key].replace(/(\d{3})(\d{4})(\d{4})/,'$1-$2-$3')
        }
      }
      const numberProxy = new Proxy({},handler)
      numberProxy.home = '130 8551 0025'
      numberProxy.work = '130 855 10025'
  	numberProxy.home // 130-855-10025 操作拦截
  ```



#### `js` 优先级

+ ```js
  <script>
      // function () {
      //   console.log('ddd')
      //   return this;
      // }
      var Task = {}
      Task.t = task
  
      task.getName = function () {
        this.a = 'cool'
        console.log('kk')
      }
  
      task.prototype.getName = function () {
        this.a = 'pu'
        console.log(3);
      };
  
  
      /*
       * 优先级问题 new 无参数时为18
       * 成员操作符优先级19 new() 优先级为19 (a+b)*c 20
       *
       **/
      new Task.t.getName()
      //  new Task 18 Task.t 19 t.getName()19
      //  == new task.getName()  new task  18 task.getName() 19
      //  task.getName() 先执行
      new Task.t().getName()
      // new Task 18 Task.t() 19
      //  == new task().getName()
      //  new task()19 task().getName() 19
      //  == new task() 先执行 生成实例 实例调用getName() 原型链查找
    </script>
  ```

#### 两个数之间的随机数

+ 包括两个数在内

  ```js
  Math.floor(Math.random()*(max-min+1))+min
  ```

#### 事件捕获事件冒泡

+ 事件捕获 `addEventListener('click',fn,usecapture=true)`

+ 事件冒泡  `addEventListener('click',fn,usecapture=false)`

+ 目标对象 处于目标对象时的 捕获和冒泡对应的 `fn` 都会触发，执行顺序和定义的上下文有关

  ```html
  <style>
      #a {
        width: 300px;
        height: 300px;
        background: pink;
      }
  
      #b {
        width: 200px;
        height: 200px;
        background: blue;
      }
  
      #c {
        width: 100px;
        height: 100px;
        background: yellow;
      }
    </style>
  </head>
  <body>
    <div id="a">
      <div id="b">
        <div id="c"></div>
      </div>
    </div>
    <script>
      var a = document.getElementById("a"),
        b = document.getElementById("b"),
        c = document.getElementById("c");
        c.addEventListener("click", function (event) {
        console.log("c2");
      }, true);
      c.addEventListener("click", function (event) {
        console.log("c1");
        // 注意第三个参数没有传进 false , 因为默认传进来的是 false
        //，代表冒泡阶段调用，个人认为处于目标阶段也会调用的
      },false);
      // b.addEventListener("click", function (event) {
      //   console.log("b1");
      // },false);
      // a.addEventListener("click", function (event) {
      //   console.log("a1")
      // },false);
  
      // b.addEventListener("click", function (event) {
      //   console.log("b2");
      // }, true);
      // a.addEventListener("click", function (event) {
      //   console.log("a2");
      // }, true);
      // a.addEventListener("click", function (event) {
      //   console.log("a1");
      // }, true);
      // a.addEventListener("click", function (event) {
      //   console.log("a3");
        // event.stopImmediatePropagation();
      // }, true);
    </script>
  </body>
  
  ```



#### 纯函数

+ 函数的输出结果，只和传入的参数有关
+ 执行过程中无副作用