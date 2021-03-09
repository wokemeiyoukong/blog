#### 五大算法

+ 算法
  + 解题方案的准确而完成的描述，一系列解决问题的清晰指令 对一定规范的输入 有限时间内获得所要求的输出

1. 贪心算法：局部最优解法
2. 分治算法：分成多个小模块，与原问题性质相同
3. 动态规划：每个状态都是过去历史的一个总结
4. 回溯法：发现原先选择不优时，退回重新选择
5. 分支限界法

#### 基础排序算法

+ 冒泡算法：两两比较

  + 时间复杂度：平均时间复杂度 `O(n*n)` 、最好情况 `O(n)` 、最差情况 `O(n*n)`指的是一个算法执行所耗费的时间
  + 空间复杂度：`O(1)` 指运行完一个程序所需内存的大小

  ```javascript
  	function bubleSort(arr) {
  	    var len = arr.length;
          // 控制循环次数
  	    for (let outer = len ; outer >= 2; outer--) {
              // 数据两两进行交换 inner <=outer - 1
  	        for(let inner = 0; inner <=outer - 1; inner++) {
  	            if(arr[inner] > arr[inner + 1]) {
  	                [arr[inner],arr[inner+1]] = [arr[inner+1],arr[inner]]
  	            }
  	        }
  	    }
  	    return arr;
  	}
  ```

  

#### 数据结构

+ 存储和组织数据的方式

+ 物理结构（逻辑结构）

  + 顺序存储结构
    + 数据元素地址连续的存储到单元
  + 链式存储结构
    + 指针存储地址

+ 逻辑结构（数据关系）

  + 集合结构
  + 线性结构
    + 一对一的关系
  + 树形结构
    + 一对多的关系
  + 图形结构
    + 多对多的关系

+ 栈结构

  + 后进先出

    ```js
    6个元素以654321顺序进栈，不合法的出栈序列
    1. 543612
    2. 453216
    3. 346521
    4. 234156
    
    // 1 5出栈的前提是6压栈 5出栈 4进栈出栈 3进栈出栈 6出栈 21进栈1出栈 2出栈
    
    // 2 4出栈 456依次在栈底 4出栈 5出栈 3进栈出栈 2进栈出栈 1进栈出栈 6出栈
    
    // 3 3出栈 3456依次在栈内 3出栈 4出栈 56在栈内 6出栈的前提是5出栈 错误
    
    // 4 2出栈 23456依次在栈内 2出栈 3出栈 4出栈 1进栈出栈 5出栈 6出栈
    ```

  + 栈的相关属性

    ```js
    function Stack() {
        this.items = []
    	// push 栈中保存元素
        Stack.prototype.push = function(ele) {
          this.items.push(ele)
        }
    	// 栈中弹出元素
        Stack.prototype.pop = function() {
          return this.items.pop()
        }
    	// 返回栈顶的元素
        Stack.prototype.peek = function() {
          return this.items[this.items.length - 1]
        }
    	// 栈是否为空
        Stack.prototype.isEmpty = function() {
          return this.items.length === 0
        }
    	// 栈的长度
        Stack.prototype.size = function() {
          return this.items.length
        }
    	// 栈转换为string
        Stack.prototype.toString = function() {
          let result = ''
          for( let i=0; i<this.items.length; i++) {
            result += this.items[i] + ' '
          }
          return result
        }
      }
    
      const stack = new Stack()
      
      
      // 十进制转二进制算法
      
      function dec2bin(decNumber) {
        // 栈中压入参数除以2的余数
        const stack = new Stack()
        
        while(decNumber > 0) {
          stack.push(decNumber % 2)
          decNumber = Math.floor( decNumber / 2)
        }
    
        let bin = ''
        // 栈内不为空时 元素全部弹出
        while(!stack.isEmpty()) {
          bin += stack.pop()
        }
    
        return bin
      }
    
      console.log(dec2bin(100)) // 1100100
    
    ```

    

+ 队列结构

  + 受限的线性表 先进先出

  + 表的后端插入 表的前端删除

    ```js
    function Queue() {
        
        this.items = []
    	// 队列中添加元素
        Queue.prototype.enqueue = function(ele) {
          this.items.push(ele)
        }
    	// 队列中删除元素(并返回元素) 删除只能从前端开始
        Queue.prototype.dequeue = function() {
         return this.items.shift()
        }
    	// 查看前端的元素
        Queue.prototype.front = function() {
          return this.items[0]
        }
    	// 队列是否为空
        Queue.prototype.isEmpty = function() {
          return this.items.length === 0
        }
    	// 队列的长度
        Queue.prototype.size = function() {
          return this.items.length
        }
    	// 队列toString
        Queue.prototype.toString = function() {
          let result = ''
          for( let i=0; i<this.items.length; i++) {
            result += this.items[i] + ' '
          }
          return result
        }
      }
    
      const queue = new Queue()
      
      
      // 击鼓传花 最后剩下的人在一群人中的位置 喊出数字的淘汰
    
      function passGame(list, num) {
    
        const queue = new Queue()
        // 玩家依次传入队列中
        list.forEach(item => queue.enqueue(item))
        console.log(queue)
        // 玩家剩余超过1的游戏继续
        while (queue.size() > 1) {
          for(let i = 0; i < num -1; i++) {
            // 安全玩家依次添加到队列的后端
            queue.enqueue(queue.dequeue())
          }
          // 喊中数字的位于队列前端 删除
          queue.dequeue()
        }
        console.log(queue.size())
        // 剩下的元素
        const end = queue.front()
        console.log(end)
        const Index = list.indexOf(end)
    
        return Index
    
      }
      const list = ['1', '2', '3', '4', '5']
      console.log(passGame(list, 3))
    ```



  + 优先级队列

      + 插入元素的时候 会考虑该数据的优先级 

      + 其他数据的优先级进行比较 比较后得出 该元素在队列中的正确位置

      + 每个元素不再只是一个数据 还包含了数据的优先级

      + 添加方式中 根据数据的优先级放入正确的位置

        ```js
        // 优先级队列 元素包含属性和优先级两个属性
        
          function PriorityQueue() {
        
            // 内部类存储元素和优先级
            function QueueElement(ele, priority) {
              this.ele = ele
              this.priority = priority
            }
        
            this.items = []
        
            PriorityQueue.prototype.enqueue = function(ele, priority) {
              
              let queueElement = new QueueElement(ele, priority)
        
              // 空队列时直接添加
              if(!this.items.length) {
                this.items.push(queueElement)
              }else {
                // 不为空时判断优先级顺序 插入队列中
                let added = false // 循环遍历优先级最后直接掺入的尾部
                for (let i = 0; i < this.items.length; i++) {
                  const element = this.items[i];
                  if(queueElement.priority < element.priority) {
                    this.items.splice(i, 0, queueElement)
                    // 插入后循环无进行必要 直接break
                    added = true
                    break
                  }else {
                    // 优先级滞后 直接插入尾部
                  }
                }
               // 插入的元素优先级大于内部元素 added 为false 添加到尾部
                if(!added) {
                  this.items.push(queueElement)
                }
              }
        
            }
          }
        
          const pq = new PriorityQueue()
        ```



#### 链表

+ 链表的特点就是通过next来指向下一个节点

```js
// head指针指向第一个存入的元素节点，每个节点都有next属性指向一下	 一个元素节点，最后一个元素的指针指向null
// 节点元素
class Node {
    constructor(element) {
        this.element = element
        this.next = null
    }
}
// 构建链表
class LinkList {
    constructor(){
        this.head = null; // 表头 默认指向第一个节点，没有为null
        this.length = 0;
    }
    append(element){
        // 追加节点
        const node = new Node(element);
        if(this.head == null){
            this.head = node; // 第一个节点就是表头
        }else{
            let current = this.head;
            // 从第一个节点查找到最后一个节点
            while(current.next){
                current = current.next;
            }
            current.next = node; // 找到最后一个节点添加next指向新增节点
        }
        this.length++; // 每增加一个长度
    }
}

// 链表的插入
insert(position,element){
    // 插入的时候判断插入的位置
    if(position>=0 && position <=this.length){
        const node = new Node(element); // 创建一个节点
        if(position === 0){ // 如果位置是0
            node.next = this.head; // 那么就让当前节点变成头
            this.head = node;
        }else{
            let current = this.head; // 获取头节点查找位置
            let previous = null;
            let index = 0;
            while(index++ < position){ // 查找到节点位置
                previous = current;
                current = current.next;
            }
            node.next = current; // 让当前节点next是刚才找到的节点
            previous.next = node; // 他的上一个节点的next是当前节点
        }
        this.length++;
    }
}

// 链表的删除
removeAt(position){
      if(position > -1 && position < this.length){
          if(position ==0){ // 如果是第一个直接改变指针
              this.head = this.head.next
          }else{
              let index = 0;
              let previous = null;
              let current = this.head;
              while(index++ < position){ // 找到要删除的这一项，直接让上一个指针指向下一个人
                  previous = current;
                  current = current.next
              }
              previous.next = current.next; // 上一个next直接指向下一个next
          }
          this.length--;
      }
  }

// 更新链表的内容
update(position,element) {
    if (position >= 0 && position < this.length) {
        if (position === 0) {
          // 根位置 直接更改跟的内容即可
          this.head.element = element
        }else{
            let index = 0;
            // 查找到要修改的项来更新
            let current = this.head;
            while(index++ < position){
              current = current.next;
            }
            current.element = element;
        }
      }
  }
```

