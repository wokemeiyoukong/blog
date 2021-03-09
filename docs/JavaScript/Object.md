#### 对象

+ `key: value` 键值对

+ 创建对象

  + 构造函数

  + 对象字面量

    ```js
    const user = new Object()
    const user = {}
    ```

+ `in` 关键字

  + 判断对象的自有属性和继承来的属性

    ```js
    const user = {
        name: 'xx'
    }
    'name' in user // true
    'age' in user // false
    ```

+ `hasOwnProperty` 方法

  + 自有属性不包含继承属性

    ```js
    const user = {
        name: 'xx'
    }
    user.hasOwnProperaty('name') // true
    user.hasOwnProperaty('age') // false
    user.hasOwnProperaty('toString') // false 继承属性
    ```

+ `undefined` 

  + 判断自有属性和继承属性

    ```js
    const user = {
        name: 'xx',
        name: undefined
    }
    user.name !== undefined // true
    user.age !== undefined // false
    user.toString !== undefined // true
    ```

+ 循环对象

  + `for in`

    ```js
    const user = {
        name: 'xx',
        age: 18
    }
    for (let i in user) {
        console.log(i, user[i]) // key value
    }
    ```

  + `Object.keys(obj) ` 
    
    + 返回对象所有可遍历属性（不含继承的）的键名 组成的数组
    
      ```js
      let o = {id:1,name:'xx',age:18,sex:'female'}
      let arr = Object.keys(o)
      // arr = [ 'id', 'name', 'age,' 'sex']
      
      / simple array
      var arr = ['a', 'b', 'c'];
      console.log(Object.keys(arr)); // console: ['0', '1', '2']
      
      // array like object
      var obj = { 0: 'a', 1: 'b', 2: 'c' };
      console.log(Object.keys(obj)); // console: ['0', '1', '2']
      
      // array like object with random key ordering
      var anObj = { 100: 'a', 2: 'b', 7: 'c' };
      console.log(Object.keys(anObj)); // console: ['2', '7', '100']
      
      // getFoo is a property which isn't enumerable
      var myObj = Object.create({}, {
        getFoo: {
          value: function () { return this.foo; }
        } 
      });
      myObj.foo = 1;
      console.log(Object.keys(myObj)); // console: ['foo']
      
      // getOwnPropertyNames 可获取对象的所有属性包括不可枚举的
      
      // tips
      Object.keys("foo");
      // TypeError: "foo" is not an object (ES5 code)
      
      Object.keys("foo");
      // ["0", "1", "2"] (ES2015 code)
      ```
    
  + `Object.values(obj)`
    
    + 返回对象所有可遍历属性的（不包含继承）键值组成的数组
    + 参数不是对象返回空数组
  + `Object.entries(obj) `
    
  + 键值对数组包含的数组
    
      ```js
      function entires(obj) {
          const temp = []
          for(let i in Object.keys(obj)) {
              temp.push([i, obj[i]])
          }
          return temp
      }
      
      // Generator 
      function* entries(obj){
          for (let k of Object.keys(obj)){
              yield [k ,obj[k]];
          }
      }
      ```
    
  + `Object.getOwnPropertyNames(Obj)`
    
    + 包含所有拥有的属性数组
  
+ 对象拷贝

  + 基本数据类型

    + 栈内存
    + 拷贝 开辟新的内存空间

  + 引用数据类型

    + 堆内存 栈内存中存储着对象的标识符和指向堆内存的地址的指针索引
    + 拷贝 指针的拷贝 指针指向的内存地址指向同一对象

  + 对象比较

    + `==/===` 变量引用同一对象 返回 `true`
    + 空对象不相等

  + 浅拷贝

    + 新对象直接拷贝已存在的对象的对象属性的引用

    + 第一层进行了拷贝

      + 基础数据类型不影响原对象
      + 根属性（对象的第一层级）创建了新的对象
      + 复杂数据类型是对地址指针的拷贝 两个属性值指向同一块内存地址

    + `Object.assign(target, ...source)`

      ```js
      // Object.assign(target, ...source) 返回拷贝后的对象 将所有可枚举属性从一个或多个对象拷贝到目标对象 继承属性和不可枚举属性不拷贝 异常会打断后续拷贝
      const user = {name: 'xx', info: {job: 'coder', age: 18}}
      const target = Object.assign({}, user)
      target.name = 'yy'
      target.info.age = 20
      log(user.name) // xx 
      log(user.info.age) // 20 浅拷贝
      
      // undefined/null 不能作为目标参数
      Object.assign(null) // error
      Object.assign(undefined) // error
      ```

    + `Array.prototype.slice(begin[, end])`

      + 返回由 `begin/end` 新数组对象（浅拷贝） 原数组对象不会改变

        ```js
        const user = ['xx', 'yy', {name: 'zz'}]
        const target  = Array.prototype.slice.call(user);
        // const target = user.slice(1)
        
        target[0] = '11'
        target[2]['name'] = '33'
        
        log(user[0]) // xx
        log(user[2]['name']) // 33 改变
        ```

    + `Array.prototype.concat(arr1[, arr2 ...])`

      + 合并两个或多个数组 不会改变现有数组 返回新数组

        ```js
        let user  = [{name: "leo"},   {age: 18}];
        let user1 = [{age: 20},{addr: "fujian"}];
        let user2 = user.concat(user1);
        user1[0]["age"] = 25;
        console.log(user);  // [{"name":"leo"},{"age":18}]
        console.log(user1); // [{"age":25},{"addr":"fujian"}]
        console.log(user2); // [{"name":"leo"},{"age":18},{"age":25},{"addr":"fujian"}]
        ```

    + `...` 扩展运算符

      + 对象属性的展开

        ```js
        let user = { name: "leo", skill: { JavaScript: 90, CSS: 80}};
        let leo = {...user};
        leo.name = "leo1";
        leo.skill.CSS = 90;
        console.log(leo.name);      // "leo1" ⚠️ 差异！
        console.log(user.name);     // "leo"  ⚠️ 差异！
        console.log(leo.skill.CSS); // 90
        console.log(user.skill.CSS);// 90
        ```

    + 实现浅拷贝

      + 新对象是对已有属性的复制和对象属性的引用，复杂类型地址指针的拷贝

        ```js
        function cloneShallow(source) {
            if(typeof source !== 'object') return source
            let target = {}
            for(let i in  source) {
                // hasOwerProperty 返回boolean 判断key是否为source自身属性 忽略继承属性
                if(Object.hasOwerProperty.call(source, key)) {
                    target[key] = source[key]
                }
            }
            return target
        }
        ```

  + 深拷贝

    + 拷贝后的对象与已有对象完全隔离 互不影响

    + `JSON.parse(JSON.stringify(obj))`

      + 序列化后对象以字符串形式保存，再反序列化生成新的对象

      + 若对象中含有 `function/symbol/undefined` 序列化后这些键值对会消失

      + 无法拷贝不可枚举属性 无法拷贝对象中的原型链

        ```js
        let user = { name: "leo", skill: { JavaScript: 90, CSS: 80}};
        let leo = JSON.parse(JSON.stringify(user));
        leo.name = "leo1";
        leo.skill.CSS = 90;
        console.log(leo.name);      // "leo1" ⚠️ 差异！
        console.log(user.name);     // "leo"  ⚠️ 差异！
        console.log(leo.skill.CSS); // 90 ⚠️ 差异！
        console.log(user.skill.CSS);// 80 ⚠️ 差异！
        
        // json.stringify 
        JSON.stringify([undefined]) // [null] undefined转化为null
        ```
    
    
    
  + 实现深拷贝
    
    ```js
    const isObject = obj => typeof obj === 'object' && obj !== null
    
    function clonedeep(source) {
    	// 判断传入的参数是否是对象类型
        if(!isObject(source)) return source
        // 返回值类型和传入参数类型一致
        const target = Array.isArray(source)? [] : {}
        for(let i in source) {
            // 判断属性是否为自身属性
            if(Object.hasOwerProperty.call(source, key)) {
                // 再判断source[key] 的值是否为复杂类型 递归调用
                if(isObject(source[key])) {
                    target[key] = clonedeep(source[key])
                }else {
                    target[key] = source[key]
                }
            }
        }
        return target
    }
    ```
    
    
    
  + 浅拷贝：将对象的每个属性依次复制，若对象的属性是引用类型时，则只拷贝引用类型的指针地址，值改变会互相影响
  + 深拷贝：复制变量值，层层拷贝，值改变不会互相影响

#### 垃圾回收机制

+ 自动的存储管理机制，当某个程序占用的内存空间不再被这个程序访问时，这个程序会借助垃圾回收机制归还这部分内存空间

#### 对象方法和 `this`

+ 对象的属性为函数方法时

+ `this` 对象方法需要使用对象的属性是，`this` 关键字执行调用本身

  + 取决于执行时的 上下文环境

  + 全局环境下 `this` 指向全局对象

  + 箭头函数的 `this` 继承自上下文执行环境

  + 使用 `call/apply/bind` 手动绑定this

    ```js
    fun.call(thisArg, param1, param2, ...)
    fun.apply(thisArg, [param1,param2,...])
    fun.bind(thisArg, param1, param2, ...)
    ```

#### 构造函数和 `new` 运算符

+ 构造函数

  ```js
  function User(name) {
      this.name = name
    	this.isShow = false
      this.say = () => {
          console.log(this.name)
      }
  }
  
  // new 关键字 实例化构造函数 返回一个对象
  let uesr = new User('xx')
  user.name // xx
  user.say() // xx
  ```

+ `new` 关键字
  
  + 空的对象被创建并分配给 `this`
  + 构造函数执行，修改 `this` 并为空对象添加新属性
  + 返回 `this`

#### 可选链 `?.`

+ 对象的属性不存在时返回 `undefined`

  ```js
  const user = {}
  user?.name?.toString() // user对象上存在name属性时执行toString()方法 否则返回 undefined
  ```

#### `null` 判断符

+ `null` 和 `undefined` 设置默认值

  ```js
  const val = res.name || 'default' // res.name 不存在是设置默认值 忽略 name 为 0 false 空字符串的情况
  const val = res.name ?? 'default' // res.name 为null/undefined 时 设置默认值
  ```

#### `Symbol`

+ 新的原始数据类型， 独一无二的值，防止命名冲突

+ 都是不相等的

+ 键值为`Symbol`的属性不参与 `for in/of` 循环

+ 可转化为布尔值，不能转换为数值

+ 作为对象属性名时用 `[]` 访问

+ `getOwnPropertySymbols` 返回当前对象中所有属性名为 `symbol`的值

+ `Reflect.ownKeys` 可返回所有类型的键名

  ```js
  let fan = Symbol()
  // 传入参数作为 Symbol的名
  let s = Symbol('s')
  typeof s // "symbol"
  ```

#### 遍历

+ `for of` 遍历没有迭代器的对象时报错
+ `for in` 遍历对象的可枚举键值 键为 `symbol` 时不可枚举

#### `defineProperty`

+ 定义属性 修改已有属性 返回这个对象

  ```js
  let obj = {}
  Object.definProperty(obj, 'name', {value: 'xx'}) // {name: 'xx'}
  
  // obg 定义的对象 prop 定义的属性  descriptor 属性描述符
  Object.defineProperty(obj,prop,descriptor)
  
  descriptor : {
  	// 数据描述符
      value: 值
      writible: 默认false,true的时候value才能被修改
      enumerable: 是否可枚举 默认false true 时才可枚举
      configurable: 是否可配置和改变删除 默认false true 时
      // 存取描述符
      get: 一个给属性提供 getter 的方法，如果没有 getter 则为 undefined。当访问该属性时，该方法会被执行，		方法执行时没有参数传入，但是会传入this对象（由于继承关系，这里的this并不一定是定义该属性的对象）。
      set: 一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。当属性值修改时，触发执行该方法。该方法将接受唯一参数，即该属性新的参数值。
  }
  
  // 
    const car = {}
    let val = 3000
    Object.definProperty(car, 'price', {
        enumerable: true,
        configurable: true,
        get() {
            log('read')
            return val
        },
        set(newval) {
            log('change')
            val = newval
        }
    })
  
  // getOwnPropertyDescriptor 返回给定的属性的属性描述符
  const obj2 = {}
  Object.defineProperty(obj2, 'name', {
    enumerable: true,
    writable: true,
    value: 'asd'
  })
  
  console.log(Object.getOwnPropertyDescriptor(obj2, 'name')) // { value: 'asd', writable: true, enumerabke: true, configurable: false }
  
  // Object.keys() 获取对象的key时， 属性描述符 enumerable: true 设置为false时， 返回空数组
  const obj2 = {}
  Object.defineProperty(obj2, 'name', {
    enumerable: false,
    writable: true,
    value: 'asd'
  })
  
  console.log(Object.keys(obj2)) // []
  
  // hasOwnProperty 检测对象是否含有特定的自身属性 for in 原型链继承属性 枚举属性为true的属性
  const obj2 = {}
  Object.defineProperty(obj2, 'name', {
    enumerable: true, // 枚举
    writable: true,
    value: 'asd'
  })
  
  const obj3 = Object.create(obj2) // obj2为obj3的原型创建
  obj3.age = 20
  for (key in obj3) {
    console.log(key) // name age 枚举属性为true的属性 false时只打印 age
  }
  for (key in obj3) {
      // 检测自身属性 排除继承属性
    if(obj3.hasOwnProperty(key)) {
      console.log(key) // age
    }
  }
  ```

#### `proxy`

+ 修改某些操作的默认行为，在目标对象之前设置一层拦截，外界对对象的访问，都需要通过这一层拦截，这层拦截可以对外界访问进行改写和过滤

  ```js
  // target 拦截的目标对象 handler 定义拦截行为 拦截必须通过对实例 proxy 进行操作访问
  const proxy = new Proxy(target, handler)
  
  const obj = new Proxy({}, {
      get(target, propkey, receiver) {
          console.log(`get ${propKey}`)
      },
      set(target, propkey, value, receiver) {
          console.log(`set ${propKey}`)
      }
  })
  
  obj.count = 1 // set count
  ++obj.count // get count set count 
  ```

+ `proxy` 拦截操作

  ```js
  const proxy = new Proxy({},{
      // 对象属性的读取 可继承
      get(target, propKey, receiver) {},
      
      // 属性的设置
      set(target, propKey, value, receiver) {},
      
      // 拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)
      apply(target, object, args) {},
      
      // 拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。
      construct(target, args) {},
      
      // 拦截propKey in proxy的操作
      has(target, propKey) {},
      
      // 拦截delete proxy[propKey]的操作
      deleteProperty(target, propKey) {},
      
      // 拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性
      ownKeys(target){},
      
      // 拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
      getOwnPropertyDescriptor(target, propKey) {},
      
      // 拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
      defineProperty(target, propKey, propDesc) {},
      
      // 拦截Object.preventExtensions(proxy)，返回一个布尔值。
      preventExtensions(target) {},
      
      // 拦截Object.getPrototypeOf(proxy)，返回一个对象
      getPrototypeOf(target) {},
      
      // 拦截Object.isExtensible(proxy)，返回一个布尔值
      isExtensible(target) {},
      
      // 拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
      setPrototypeOf(target, proto)() {}
  })
  ```

  

