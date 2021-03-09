#### 浏览器下的事件循环（`Event Loop`）

+ 事件循环：执行一个宏任务，然后执行清空微任务列表，循环再执行宏任务，再清微任务列表
  + 微任务 `microtask(jobs)`: `promise / ajax / Object.observe(该方法已废弃)/mutationobserver`

    + `promise` 一旦被定义就会立即执行 `rejecet resolve` 是异步执行的回调会在主函数执行完和`setTimeout` 前调用 `then` 返回的会放到任务队列中
    + `await`执行完后会让出线程，`async` 标记的函数会返回一个`promise ` 对象

  + 宏任务 `macrotask(task)`: `setTimout / script / IO / UI Rendering`

    ```javascript
        setTimeout(function () {
            console.log('timeout1');// 7
        }, 1000);
    	console.log('start'); // 1
    	Promise.resolve()
        	.then(function () {
        	console.log('promise1'); // 3
        Promise.resolve()
            .then(function () {
            console.log('promise2');// 4
        });
        setTimeout(function () {
            Promise.resolve().then(function () {
                console.log('promise3'); // 6
            });
            console.log('timeout2')// 5
        }, 0);
  });
    console.log('done'); // 2

    // async await promise settimeout
    	async function async1() {
          console.log('async1 start') // 2
          await async2() // async2 被await 标记 返回一个promise 放入回调队列中 后续代码也等待执行
          console.log('async1 end') // 7 6 返回顺序不确定
        }
        async function async2() {
          console.log('async2') // 3
        }
        console.log('script start') // 1
        setTimeout(function () {
          console.log('setTimeout') // 放入event loop 中 8
        }, 0)
        async1(); // 执行
        new Promise(function (resolve) {
          console.log('promise1') // 4
          resolve();
        }).then(function () {
          console.log('promise2') // 6 7
        })
        console.log('script end') // 5
    /*
    *  script start
    *  async1 start
    	async2
    	promise1
    	script end
    	async1 end
    	promise2
       setTimeout
    **/
    ```


  ```javascript
  for (var i = 0; i < 5; i++) {
    console.log(i);
  }
  // 0 1 2 3 4
  for (var i = 0; i < 5; i++) {
    setTimeout(function() {
      console.log(i);
    }, 1000 * i);
  }
  // 输出5 一秒一个5 5 5 5
  for (var i = 0; i < 5; i++) {
    (function(i) {
      setTimeout(function() {
        console.log(i);
      }, i * 1000);
    })(i);
  }
  // 闭包 0 1 2 3 4（定时输出）保存变量
  for (var i = 0; i < 5; i++) {
    (function() { // 没有传入参数 i 为全局变量
      setTimeout(function() {
        console.log(i);
      }, i * 1000);
    })(i);
  }
  // 输出5 一秒一个5 5 5 5 5
  for (var i = 0; i < 5; i++) {
    setTimeout((function(i) {
      console.log(i);
    })(i), i * 1000);
  }
  // setTimeout可以接受函数或者字符串作为参数 传入立即参数等于传入undefined 立即函数会立即执行 立即输出 0 1 2 3 4

  setTimeout(function() {
    console.log(1)
  }, 0);
  new Promise(function executor(resolve) {
    // Promise，里面的函数是直接执行的 会先输出2 和3
    console.log(2);
    for( var i=0 ; i<10000 ; i++ ) {
      i == 9999 && resolve();
    }
    console.log(3);
  }).then(function() {
    console.log(4);
  });
  console.log(5);
  // 2 3 5 4 1
  ```



#### 输入`url` 到展示的过程

+ `DNS` 域名解析

  + 域名解析成对应的服务器 `IP` 地址

    ```javascript
    // 浏览器客户端向本地DNS服务器发送一个含有域名www.cnblogs.com的DNS查询报文。本地DNS服务器把查询报文转发到根DNS服务器，根DNS服务器注意到其com后缀，于是向本地DNS服务器返回comDNS服务器的IP地址。本地DNS服务器再次向comDNS服务器发送查询请求，comDNS服务器注意到其www.cnblogs.com后缀并用负责该域名的权威DNS服务器的IP地址作为回应。最后，本地DNS服务器将含有www.cnblogs.com的IP地址的响应报文发送给客户端。
    // 从客户端到本地服务器属于递归查询，而DNS服务器之间的交互属于迭代查询。
    ```

+ 建立 `TCP` 连接（三次握手）

  + 拿到服务器的 `IP` 地址：

    ```javascript
    // 客户端发送一个带有SYN标志的数据包给服务端，服务端收到后，回传一个带有SYN/ACK标志的数据包以示传达确认信息，最后客户端再回传一个带ACK标志的数据包，代表握手结束，连接成功。
    // 客户端 ------>（请求连接SYN数据包）服务端 ----->（确认SYN/ACK数据包）客户端 ----->（握手结束ACK数据包）服务端
    /*
    1.客户端发送 syn(同步序列编号) 请求，进入 syn_send 状态，等待确认
    2.服务端接收并确认 syn 包后发送 syn+ack 包，进入 syn_recv 状态
    3.客户端接收 syn+ack 包后，发送 ack 包，双方进入 established 状态
    */

    ```

+ 发送 `HTTP` 请求

  ```javascript
  // 请求行包括请求方法、URI、HTTP版本。首部字段传递重要信息，包括请求首部字段、通用首部字段和实体首部字段。我们可以从报文中看到发出的请求的具体信息。
  ```

+ 服务器处理请求

  ```javascript
  // 服务器端收到请求后的由web服务器（准确说应该是http服务器）处理请求，诸如Apache、Ngnix、IIS等。web服务器解析用户请求，知道了需要调度哪些资源文件，再通过相应的这些资源文件处理用户请求和参数，并调用数据库信息，最后将结果通过web服务器返回给浏览器客户端。
  ```

+ 服务器返回响应结果

  ```javascript
  // 返回请求的数据和内容 在响应结果中都会有个一个HTTP状态码，比如我们熟知的200、301、404、500等。通过这个状态码我们可以知道服务器端的处理是否正常，并能了解具体的错误。
  ```

+ 关闭 `TCP` 连接（四次挥手）

  ```javascript
  // 客户端 ------>（FIN）服务端 ----->（ACK FIN 发送两次）客户端 ----->（ACK）服务端
  /*
  客户端 -- FIN --> 服务端， FIN—WAIT
  服务端 -- ACK --> 客户端， CLOSE-WAIT
  服务端 -- ACK,FIN --> 客户端， LAST-ACK
  客户端 -- ACK --> 服务端，CLOSED
  */
  
  /*
    客户端 -> FIN 到服务端 标识不再发送数据 可接收数据 
    服务端 -> ACK 数据未传输完毕
    服务端 -> ACK+FIN 传入完毕 可关闭
    客户端 -> ACK 结束
  */
  ```

+ 浏览器解析 `HTML`

  ```javascript
  // 浏览器通过解析HTML，生成DOM树，解析CSS，生成CSS规则树，然后通过DOM树和CSS规则树生成渲染树。 执行js脚本是可能会停止解析后续HTML
  ```

+ 浏览器布局渲染

  ```javascript
  // 浏览器绘制各个节点 绘制过程中会产生 重绘/回流
  ```

  - 重绘
    - 当元素样式的改变不影响布局时，浏览器将使用重绘对元素进行更新，此时由于只需要 `UI` 层面的重新像素绘制，因此损耗较少
  - 回流
    - 当元素的尺寸、结构或触发某些属性时，浏览器会重新渲染页面，称为回流。此时，浏览器需要重新经过计算，计算后还需要重新页面布局，因此是较重的操作。会触发回流的操作:
  - 回流必然会导致重绘，重绘不一定触发回流

#### `DOM` 加载执行

+ `document.ready()` `DOM` 完全就绪是可以被调用 可以多个依次执行
+ `window.onload()` `DOM` 文档树加载完和所有文件（包括图片等非文字媒体文件） 加载完之后执行 只能写一个会覆盖

#### `requestAnimationFrame`

+ 关键帧动画，重绘前调用指定的回调函数更新动画，每秒60次，实现高性能的帧动画设计的`api`

+ 返回函数：`cancelAnimationFrame` 取消函数执行 `requestAnimationFrame` 返回一个`ID` 用于取消

+ 执行机制是由系统决定回调函数地自动计算

  ```js
  // requestAnimationFrame 取代 延时函数执行动画
  setInterval(() => {
            // 清除图像
            ctx.clearRect(x, y, width, height)
            x += speedX
            // 运动边界进行判断x为矩形左上角的点根据它的大小判断矩形左右边界
            if (x > can.width - width) {
              speedX *= -1
            } else if (x < 0) {
              speedX *= -1
            };
            y += speedY
            if (y > can.height - height) {
              speedY *= -1
            } else if (y < 0) {
              speedY *= -1
            }
            // console.log(y)
            ctx.fillRect(x, y, width, height)
          }, 20);
  <script>
      function draw() {
        let can = document.querySelector('canvas')
        if(can.getContext) {
          let ctx = can.getContext('2d')
          let x = 0
          let y = 0
          let width = 100
          let height = 100
          ctx.beginPath()
          ctx.fillStyle = '#ccc'
          ctx.fillRect(x,y,width,height)
          let speedX = 2
          let speedY = 2
          function move() {
            ctx.clearRect(x,y,width,height)
            x += speedX
            if(x > can.width - width) {
              speedX *= -1
            }else if(x < 0){
              speedX *= -1
            }
            y += speedY
            if(y > can.height - height) {
              speedY *= -1
            }else if(y < 0){
              speedY *= -1
            }
            ctx.fillRect(x,y,width,height)
            // requestAnimationFrame 关键帧动画
            window.requestAnimationFrame(move)
            // window.cancelAnimationFrame
          }
          move()
        }else {
          alert('brower no support canvas')
        }
      }
      window.onload = draw()
    </script>
  ```

#### 存储

+ 短暂性存储：存储在内存中
+ 持久性存储：
  + 浏览器端：
    + `cookie`： 通常用于存储用户身份，登录状态，`http` 中自动携带，体积上限为 `4K` ，可自行设置过期时间。第一次访问网站的时候，浏览器发出请求，服务器响应请求后，会将cookie放入到响应请求中，在浏览器第二次发请求的时候，会把cookie，带过去，服务端会辨别用户身份，当然服务器也可以修改cookie内容，cookie不可跨域，
      + `xss`：用户通过各种方式将恶意代码注入到其他用户的页面中。就可以通过脚本获取信息，发起请求，之类的操作。
      + `csrf`：跨站请求攻击，简单地说，是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并运行一些操作（如发邮件，发消息，甚至财产操作如转账和购买商品）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户操作而去运行。这利用了web中用户身份验证的一个漏洞：简单的身份验证只能保证请求发自某个用户的浏览器，却不能保证请求本身是用户自愿发出的。`csrf` 并不能够拿到用户的任何信息，它只是欺骗用户浏览器，让其以用户的名义进行操作。

    + `localStorage/sessionStorage` ：长久存储/窗口关闭删除，体积限制为 `4~5M`

      + `sessionStorage`: 页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会保持原来的页面会话。在新标签或窗口打开一个页面时会复制顶级浏览会话的上下文作为新会话的上下文；用于临时保存同一窗口(或标签页)的数据，在关闭窗口或标签页之后将会删除这些数据

        ```js
        var inlue = document.querySelector('.input')
          console.log(inlue.value)
          // 浏览器刷新情况下会保留输入框的内容
          if (sessionStorage.getItem('autosave')) {
            inlue.value = sessionStorage.getItem('autosave')
          }
          inlue.addEventListener('change', function (e) {
            // console.log(e.target.value)
            inlue.value = e.target.value
            sessionStorage.setItem('autosave', inlue.value)
          })
        ```

  + 服务器端：
    + 分布式缓存
    + 数据库

#### `Web Worker`

+ 现代浏览器为`JavaScript`创造的 **多线程环境**。可以新建并将部分任务分配到`worker`线程并行运行，两个线程可 **独立运行，互不干扰**，可通过自带的 **消息机制** 相互通信。

+ 用法：不同线程中处理不同任务

  ```javascript
  // 创建 worker
  const worker = new Worker('work.js');

  // 向主进程推送消息
  worker.postMessage('Hello World');

  // 监听主进程来的消息
  worker.onmessage = function (event) {
    console.log('Received message ' + event.data);
  }
  ```

+ 限制
  + 同源限制
  + 无法使用 `document` / `window` / `alert` / `confirm`
  + 无法加载本地资源

#### `V8` 垃圾回收机制

+ 垃圾回收: 将内存中不再使用的数据进行清理，释放出内存空间。`V8 ` 将内存分成 **新生代空间** 和 **老生代空间**。

#### 内存泄漏

+ 意外的**全局变量**: 无法被回收
+ **定时器**: 未被正确关闭，导致所引用的外部变量无法被释放
+ **事件监听**: 没有正确销毁 (低版本浏览器可能出现)
+ **闭包**: 会导致父级中的变量无法被释放
+ `dom`引用: `dom`元素被删除时， 内存中的引用未被正确清空

#### 跨域

+ `JSONP` 利用`<script>`标签不受跨域限制的特点，缺点是只能支持 get 请求

  ```javascript
  function jsonp(url, jsonpCallback, success) {
    const script = document.createElement('script')
    script.src = url
    script.async = true
    script.type = 'text/javascript'
    window[jsonpCallback] = function(data) {
      success && success(data)
    }
    document.body.appendChild(script)
  }
  ```

+ 设置 `CORS:Access-Control-Allow-Origin：*`

#### 前端性能优化

+ `dns` 预解析
+ 减少`http` 请求
+ 开启`gzip`
+ 懒加载
+ `cdn`
+ 浏览器启用缓存
  + 减少页面加载时间
  + 减少服务器负载

#### `H5`秒开方案思考

+ 直出&离线包缓存
  + 优化首屏服务端渲染（直出）
  + 离线包基本思路：通过`webview` 统一拦截`url` ，将资源映射到本地离线包（腾讯的`webso` 和 `Alloykit`离线包方案）
+ `PWA` 直出&预加载
  +  直出 `html`，我们可以配合 `pwa`，将从后台直出的文件，缓存到 `cacheStorage`，在下一次请求时，优先从本地缓存中获取，同时发起网络请求更新本地 `html `文件
+ 用更多的网络、内存和 CPU 换取速度，以空间换时间

#### 浏览器同源策略

+ 域名、协议、端口相同
+ `DOM` 同源策略：禁止对不同源页面的`DOM` 进行操作，主要场景是不同`iframe` 跨域的情况，不同域名的`iframe` 是限制互相访问的
+ `XMLHttpRequest` 同源策略：禁止使用`XHR` 对象向不同源得到服务器地址发起`HTTP` 请求

#### 跨域的请求方法

+ `CORS` （资源跨域共享）
+ `JSONP` 动态创建`script`标签，利用 `scr` 属性（只支持`get` 请求，安全性，判断请求失败问题）
+ 服务器代理

#### `HTTPS`和`HTTP`的区别：

​      `https`协议需要到ca申请证书，一般免费证书很少，需要交费。

​      `http`是超文本传输协议，信息是明文传输，不提供任何数据加密

​	  `https `则是具有安全性的`ssl`加密传输协议。建立信息传递通道，验证网站的真实性

​      `http`和`https`使用的是完全不同的连接方式用的端口也不一样，前者是80，后者是443。

​      `http`的连接很简单，是无状态的。

​      `HTTPS`协议是由`SSL`+`HTTP`协议构建的可进行加密传输、身份认证的网络协议，要比`http`协议安全。

+ `HTTP` 的不足
  + 通信使用明文（未加密） 通信可能被窃听
  + 没有验证通信方的身份 可能遭遇伪装
  + 无法证明报文的完整性 可能会被篡改

#### 文件上传

+ `input file`

  + 图片上传

    ```html
    <div>
        <!--accept="image/*" 接受图片格式-->
        <input type="file" name="fileimg" id="file" accept="image/*">
        <button onclick="uploads()">提交</button>
        <img src="" alt="" id="backimg">
    </div>
    ```

  + 封装 `ajax`

    ```js
    // 封装ajax方法
      function $ajax(options) {
          // assign 源对象的数据copy到目标对象上 首层浅拷贝
        options = Object.assign({
          url: '',
          methods: 'post', // 设置请求头
          data: null,
          headers: {},
          // 上传进度函数
          progress: Function.prototype
        }, options)
        return new Promise((rsolve, reject) => {
            // new 有参数优先级19无参数18
          const xhr = new XMLHttpRequest
          // xhr 监听上传进度
          xhr.upload.onprogress = options.progress
          xhr.open(options.methods, options.url)
            // post 请求需要设置请求头
          Object.keys(options.headers).forEach(key => {
            xhr.setRequestHeader(key, options.headers[key])
          })
          xhr.onreadystatechange = () => {
            if (xhr.readyState === 4) {
              if (/^(2|3)\d{2}$/.test(xhr.status)) {
                rsolve(JSON.parse(xhr.responseText))
                return
              }
              reject(xhr)
            }
          }
          xhr.send(options.data)
        })
      }
    ```

  + 名称 `hash`

    ```js
     // 文件名称的加密hash
      function $formatfilename(filename) {
          // lastIndexOf 返回字符串最后出现的索引
        let Index = filename.lastIndexOf('.')
        let name = filename.substring(0, Index)
        let typename = filename.substring(Index + 1)
        // md5 一个加密方法
        name = md5(name) + new Date().getTime()
        return {
          name,
          typename,
          fullname: `${name}.${typename}`
        }
      }
    ```

  + 监听图片改变事件上传

    ```js
    // 文件类型和大小
      const filetype = ['image/jpeg', 'image/jpg', 'image/png', 'image/gif']
      const filesize = 100 * 1024

      // 监听文件上传进程
      let _data = new Proxy({total:0},{
          set(target,key,value) {
              target[key] = value
              if(_data.total >= 100){
                  // 上传完成
                  progress.innertHTML = '上传完成'
                  return
              }
              progress.innertHTML = `${_data.total}%`
          }
      })
      // 监听图片change事件 发起ajax 请求
      file.onchange = async function () {
        // 文件信息 大小 类型
        let files = this.files[0]
        // 文件转成表单数据结构的键值对 XMLHttpRequest.send(formdata) 发送到服务器
        let formdata = new FormData()
        // 文件信息和hash后的文件名存到formdata
        formdata.append('chunk', files)
        formdata.append('filename', $formatfilename(files.name).fullname)
        // get方法查看 append 的对应的键值对
        console.log(formdata.get('filename'))
        if (!filetype.includes(files.type)) {
          alert('格式错误')
          return
        }
        if (files.size > filesize) {
          alert('不能超过100k')
          return
        }
        let result = await $ajax({
          url: 'http://localhost:8000/file/upload',
          data: formdata,
          progress: ev => {
              // ev 上传文件对象 ev.loaded 已上传大小 ev.total 文件总大小
              _data.total = ev.loaded / ev.total
          }
        })
        if (result.code === 200) {
          backimg.src = result.data
        }
      }
    ```

+ `flash`

+ `HTML file api`

  + `new FileReader()`: 用于读取文件，异步读取文件
    + `readAsDataURL` 返回基于 `Base64` 编码的对象，可用于 `img src` 属性，达到预览图片的效果
    + `readAsText` 返回文本字符串
    + `readAsArrayBuffer` 返回一个 `ArrayBuffer` 对象
  + 提供回调函数
    + `onload` 读取成功触发
    + `onprogress` 读取过程中周期性 触发，可获取读取进度
  + 转换 `base64`

  ```js
  // base64 上传
    function transBase64(name) {
      return new Promise(resolve => {
          // api
        let fileRead = new FileReader()
        // 转换为base64地址
        fileRead.readAsDataURL(name)
        fileRead.onload = ev => {
          resolve(ev.target.result)
        }
      })
    }
  ```

  + 监听上传

  ```js
  file.onchange = async function () {
      let files = this.files[0]
      if(!files)  return
      // base64 转换结果保存
      let base64 = await transBase64(file)
      let result = await $ajax({
        url: 'http://localhost:8000/file/upload',
          // base64 请求头设置
        headers: {
          'Content-Type':'application/x-www-form-urlencoded'
        },
          // encodeURIComponent 转码
        data: `chunk=${encodeURIComponent(base64)}&filename=${$formatfilename(files.name).fullname}`
      })
      if (result.code === 200) {
        backimg.src = result.data
      }
    }
  ```

#### 大文件上传

+ 大文件切片化 ，利用 `file` 是 `Blob` 类的实例，`Blob.prototype.slice` 可以把一个文件切片处理

+ 利用 `HTTP` 可以多个并发请求

+ 切片并发请求后，再服务器发送合并图片的请求

  ```js
     // 监听文件上传进程
    let _data = new Proxy({total:0},{
        set(target,key,value) {
            target[key] = value
            if(_data.total >= 100){
                // 上传完成
                progress.innertHTML = '上传完成'
                return
            }
            progress.innertHTML = `${_data.total}%`
        }
    })

  file.onchange = async function () {
       // files 文件属性
      let files = this.files[0]
      if (!files) return
       progress.innertHTML = '0%'
      // console.log(files)
      // 对图片进行切片处理
      let formdata = new FormData()
      let chunksize = files.size / 5,
        cur = 0,
        i = 0,
        partList = []
      let {
        name,
        typename,
        fullname
      } = $formatfilename(files.name)
      // 循环遍历 保存每个切片文件和命名
      while (i < 5) {
        partList.push({
          chunk: files.slice(cur, cur + chunksize),
          filename: `${name}-${i}.${typename}`
        })
        cur += chunksize
        i++
      }
      // 并发发送切片
      partList = partList.map(item => {
        formdata.append('chunk', item.chunk)
        formdata.append('filename', item.filename)
        // 返回ajax
        console.log(formdata.get('chunk'))
        return $ajax({
          url: 'http://localhost:8000/file/upload/chunk',
          data: formdata
        }).then((res) => {
          if (res.resultcode === 200) {
              // 单个文件上传成功 progerss
              _data.total += 20
            return Promise.resolve(res)
          }
          return Promise.reject(res)
        })
      })
       // Promise.all 等到所有切片发送成功
      await Promise.all(partList)
      // 发送合并请求
      let result = await $ajax({
        url: 'http://localhost:8000/file/upload/merge',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        data: `filename=${fullname}`
      })
      if (result.code === 200) {
        backimg.src = result.data
      }
   }
  ```

#### `SEO`

+ 网站结构布局优化：尽量简单, 提倡扁平化结构. 一般而言，建立的网站结构层次越少，越容易被“蜘蛛”抓取，也就容易被收录。
+ `img` 标签必须添加“alt”和“title”属性，告诉搜索引擎导航的定位，做到即使图片未能正常显示时，用户也能看到提示文字。
+ 把重要内容HTML代码放在最前搜索引擎抓取HTML内容是从上到下，利用这一特点，可以让主要代码优先读取，广告等不重要代码放在下边。
+ 控制页面的大小，减少`http`请求，提高网站的加载速度。
+ 合理的设计title、description和keywords

- title标题：只强调重点即可，尽量把重要的关键词放在前面，关键词不要重复出现，尽量做到每个页面的title标题中不要设置相同的内容。
- meta keywords页面/网站的关键字。
- meta description网页描述，需要高度概括网页内容，切记不能太长，过分堆砌关键词，每个页面也要有所不同。
- 语义化书写HTML代码，符合`W3C`标准尽量让代码语义化，在适当的位置使用适当的标签，用正确的标签做正确的事。让阅读源码者和“蜘蛛”都一目了然。
- a标签：页面链接，要加 “title” 属性说明，链接到其他网站则需要加上 `el="nofollow"` 属性, 告诉 “蜘蛛” 不要爬，因为一旦“蜘蛛”爬了外部链接之后，就不会再回来了。
- 图标使用`IconFont`替换
- 使用`CDN`网络缓存，加快用户访问速度，减轻服务器压力
- 启用`GZIP`压缩，浏览速度变快，搜索引擎的蜘蛛抓取信息量也会增大
- `SSR`技术
- 预渲染技术

#### `WebSocket`

+ 服务端可以主动向客户端传递消息，客户端也可以主动向服务端发送消息，双向平等对话

+ 优点

  + 用于控制数据包的头部相对原始的轮询机制较小
  + 实时请求 全双工通信 服务端可主动给客户端下发数据
  + 建立连接后 连接状态保持 主动关闭
  + 二进制内容支持
  + 子扩展

+ `HTML5 ` 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。

+ 浏览器通过 JavaScript 向服务器发出建立 `WebSocket `连接的请求，连接建立以后，客户端和服务器端就可以通过 TCP 连接直接交换数据。

  当你获取 Web Socket 连接后，你可以通过 **send()** 方法来向服务器发送数据，并通过 `onmessage ` 事件来接收服务器返回的数据。


```js
// 参数 url, 指定连接的 URL。第二个参数 protocol 是可选的，指定了可接受的子协议。
const socket = new WebSocket(url [, protocol])

<script type="text/javascript">
         function WebSocketTest()
         {
            if ("WebSocket" in window)
            {
               alert("您的浏览器支持 WebSocket!");

               // 打开一个 web socket
               var ws = new WebSocket("ws://localhost:9998/echo");

               ws.onopen = function()
               {
                  // Web Socket 已连接上，使用 send() 方法发送数据
                  ws.send("发送数据");
                  alert("数据发送中...");
               };

               ws.onmessage = function (evt)
               {
                  var received_msg = evt.data;
                  alert("数据已接收...");
               };

               ws.onclose = function()
               {
                  // 关闭 websocket
                  alert("连接已关闭...");
               };
            }

            else
            {
               // 浏览器不支持 WebSocket
               alert("您的浏览器不支持 WebSocket!");
            }
         }
      </script>
```

#### `TCP UTD`

+ `TCP` : 面向连接的、可靠的、基于字节流的传输层协议
+ `UTD`： 面向无连接的传输层协议

****

#### `HTML5` 离线资源缓存

```html
<html manifest="cache.manifest"> 
    cache.manifest 指向缓存文件 离线存储的资源
    离线时 window.applicationCache 
</html>
```

