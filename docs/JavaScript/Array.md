#### 数组的相关属性

+ `Array.includes` 判断数据是否包含一个指定的参数， 返回值 `true/ false`

  ```js
  function printAnimals(animal) {
     const animals = ['dog', 'cat', 'hamster', 'turtle']; 
     // 包含返回 true
     if (animals.includes(animal)) {
       console.log(I have a ${animal});
     }
  }
  
  console.log(printAnimals('hamster'))
  ```

+ `Array.every/Array.some` 数组是否 全部/至少有一个 满足指定条件，返回值 `true/false`

  ```js
  const isBelow = (value) => value < 40
  const arr = [1, 30, 29, 10]
  console.log(arr.every(isBelow)) // true
  
  const array = [1, 2, 3, 4, 5]
  const even = (element) => element % 2 === 0
  console.log(array.some(even)) // true
  ```

+ `map` : 遍历数组，返回回调值组成的新数组

  ```js
  // map 返回经过callback执行后的新函数
  function _map (arr,callback) {
      if( Array.isArray(arr) || !arr.length || typeof callback !== 'function' ) {
          return []
      } else {
          let result = []
          for ( let i = 0; i<arr.length; i++ ) {
              result.push(callback(arr[i],i,arr))
          }
          return result
      }   
  }
  
  //
  [1,2,3].map(parseInt) => [1,NaN,NaN]
  /*
  parseInt(string, radix)   
  将一个字符串 string 转换为 radix 进制的整数， radix 为介于2-36之间的数。 默认为10
  map 传递三个参数 the element the index the array
  parseInt 接受两个参数 第三个忽略
  parseInt(1,0) => 1
  parseInt(2,1) => NaN
  parseInt(3,2) => NaN
  */ 
  ```

+ `forEach`: 无法`break`，可以用`try/catch`中`throw new Error`来停止 元素执行给定的函数 返回值 `undefined`

  ```js
  arr.forEach((value,index,array) =>{})
  ```

+ `for in` 遍历 `index` (用来遍历对象) 

+ `for of` 遍历数组的 `value`

  ```js
  var arr = [0, 1, 2]
  	arr[10] = 10
  	console.log(arr[3])
  	// for (let i = 0; i<arr.length; i++) {
  	// 	console.log(arr[i])
  	// }
  	for( let i in arr) {
  		console.log(i)
  	}
  // 0 1 2 10 其他的key值是不存在的
  ```

+ `filter`: 过滤，返回满足条件的新数组

  ```js
  // map 返回经过callback执行后的新函数
  function _filter (arr,callback) {
      if( Array.isArray(arr) || !arr.length || typeof callback !== 'function' ) {
          return []
      } else {
          let result = []
          for ( let i = 0; i<arr.length; i++ ) {
              if(callback(arr[i],i,arr)){
                  result.push(arr[i])
              }
          }
           return result
      }  
  }
  ```

+ `reduce / reduceRight(fn(prev, cur), defaultPrev)`: 两两执行，`prev` 为上次化简函数的`return`值，`cur`为当前值(从第二项开始)

  ```js
  arr.reduce(callback[, initialValue])
  // reduce接受两个参数, 一个回调, 一个初始值.
  // 回调函数接受四个参数 previousValue, currentValue, currentIndex, array
  // 需要注意的是 If the array is empty and no initialValue was provided, TypeError would be thrown.
  // 所以第二个表达式会报异常. 第一个表达式等价于 Math.pow(3, 2) => 9; Math.pow(9, 1) =>9
  ```

+ `some`: 有一项返回`true`，则整体为`true`，满足立即终止循环

+ `every`: 有一项返回`false`，则整体为`false`

+ `find()` 方法返回数组中满足提供的测试函数的第一个元素的值。否则返回 `undefined`

+ `join`: 通过指定连接符生成字符串

+ `push / pop`: 末尾推入和弹出，改变原数组， 返回推入/弹出项

  + `push` 和 `unshifit` 返回值为数组的长度
  + `pop` 和 `shift` 返回值为删除的项

+ `unshift / shift`: 头部推入和弹出，改变原数组，返回操作项

+ `sort(fn) / reverse`: 排序与反转，改变原数组

  + `sort` 排序转换为字符串进行比较

+ `concat`: 连接数组，不影响原数组， 浅拷贝

+ `slice(start, end)`: 返回截断后的新数组，不改变原数组

+ `splice(start, number, value...)`: 返回删除元素组成的数组，value 为插入项，改变原数组

+ `indexOf / lastIndexOf(value, fromIndex)`: 查找数组项，返回对应的下标

  + 比较时会使用全等操作符

+ 扁平化数组

  ```js
  // reduce ie不兼容flat polyfill
  function flatten(arr = []) {  
      return arr.reduce((result, item) => {
          return result.concat(Array.isArray(item) ? flatten(item) : item)
      }, [])
  }
  
  // (toString | join) & split(利用数组的toString或者join,将数组转化为字符串)
  function flatten(arr = []) {
    return arr.toString().split(',').map(item => Number(item))
  }
  
  // es6
  Array.flat(Inifity)
  ```

+ 数组乱序排列

  ```js
  var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  arr.sort(function () {
      return Math.random() - 0.5;
  });
  ```

+ 稀疏数组

  ```js
  console.log(Array(100).map(x => 1))
  // [empty*100] Array(n) 将会创建一个稀疏数组 稀疏数组内含非真实元素
  ```

  

