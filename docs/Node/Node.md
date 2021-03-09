#### Node

+ 单线程
+ 非阻塞`I/O`
+ 基于 `chrome v8`引擎
+ 轻量高效的 `js` 运行环境 （运行在服务端的 `js`）

####  文件读取同步异步

+ `readFileSync`: 同步
+ `readFile`： 异步

#### 非阻塞式 `IO`

+ 进程： `CPU` 执行任务的模块
+ 线程： 模块中的最小单元

+ ​	事件驱动模型，`web Server` 接受请求，不影响下一个 `web` 请求，一直接受请求不等待任何读写操作。

#### 全局对象

+ `__filename` : 输出文件所在路径的绝对路径
+ `__dirname`: 当前脚本的所在目录

#### 工具

+ `inherits` : 继承函数

#### 模块、文件 导入导出

- `自定义模块导出`

- 导出查找的属性为 `module.exports`

  ```js
  // 自定义文件 导出
  const defaultStr = 'default'
  const hello = (arg = defaultStr) => {
      return arg
  }
  exports = module.exports // true 指向内存中的同一块地址
  
  // module.exports = {}
   module.exports = {
       hello // 导入对应的属性 {hello: hello}
   }
  // exports.hello = hello
  exports.hello = hello // 上述相同 导出为对象 {hello: hello}
  // module.exports
  module.exports = hello // 直接导出 hello函数
  
  exports = hello // {} 空对象
  // 导入文件
  const A =  require('./file.js')
  // 1 2 相同
  console.log(A) // {hello: function()}  对象下有个hello属性
  // 3
  console.log(A()) // 直接执行函数
  ```

#### 文件读取

- `node` 文件读取

  ```js
  // 引入 fs node内置模块
  const fs = require('fs')
  
  // 同步读取 path 读取文件的路径 [, options] 配置项 encoding: utf-8 文件内容的不同类型
  const content = fs.readFileSync(path, { encoding: 'utf-8' })
  
  // 异步读取 callback 的方式返回内容 异步读取不会阻塞进程
  fs.readFile(path, { encoding: 'utf-8' }, (err, data) => {
    if (err) return
    console.log(data)
  })
  
  // 封装promise方法
  function  fsRead(path) {
    return new Promise((resolve, reject) => {
      fs.readFile(path, { encoding: 'utf-8' }, (err, data) => {
        if (err) {
        	reject(err)
        }else {
            resolve(data)
        }
      })
    })
  }
  
  // async 返回 promise 对象
  async function start(data) {
    // console.log(await asyncFun(data))
    const res = await asyncFun(data)
    // console.log(res)
  }
  
  start('asynchronous')
  ```

#### 文件写入

+ `writeFile` 异步的文件写入

  ```js
  // writeFile(path, info, [, options], callback)
  // 参数为  路径 写入的内容 配置项 回调函数
  // 配置项为 {falg: mode,  encoding: coding} mode: a 追加 w 覆盖写入 r 读取 coding: 编码模式
  fs.writeFile(path,'this is something that i write just now' , { flag: 'a', encoding: 'utf-8' }, (err) => {
    if(err) return
  })
  
  // 封装promise
  function fsWrite(path, info, mode='a', coding='utf-8') {
    return new Promise((resolve, reject) => {
      fs.writeFile(path, info, { flag: mode, encoding: coding }, (err) => {
        if (err) {
          reject(err)
        } else {
          resolve('success')
        }
      })
    })
  }
  
  // 执行
  async function writeFsAsync() {
    await writeFs(path, 'info one\n')
    await writeFs(path, 'info twe\n')
    await writeFs(path, 'info three\n')
  }
  
  writeFsAsync()
  ```

#### `buffer` 缓存区

+ 数据无法存储二进制数据

  ```js
  // 字符串存储为二进制数据
  const str = 'info about this fils'
  const buf = Buffer.from(str)
  console.log(buf)
  
  // 内存中开辟出一个缓存区用来存储数据
  const bufAloc = Buffer.alloc(10)
  bufAloc[0] = 16
  
  console.log(bufAloc) // 16进制展示 <Buffer 10 00 00 00 00 00 00 00 00 00>
  ```

#### 文件夹读取

+ `readdir` 读取文件夹下的目录文件

  ```js
  // readdir(path, callback)
  
  const fs = require('fs')
  // 定义的异步读取函数
  const { fsRead, fsWrite } = require('./utils')
  
  const path = '../02'
  const source_path = path + '/'
  const target = 'target.txt'
  // 
  fs.readdir(path, (err, files) => {
    if (err) return
    files.forEach(async (file, i) => {
      let info = await fsRead(source_path + file)
      await fsWrite(target, info)
    })
  })
  ```

#### 逐行读取

+ `readline`

  ```js
  const readline = require('readline')
  // 创建接口对象
  const rl = readline.createInterface({
    // 可写流
    output: process.stdout,
    // 可读流
    input: process.stdin
  })
  // answer 用户交互输入的内容
  rl.question('how are you?', (answer) => {
    console.log('replay:' + answer)
    rl.close()
  })
  
  rl.on('close', () => {
    process.exit(0)
  })
  
  // 用户输入输出文件
  const readline = require('readline')
  const { fsWrite } = require('./utils')
  
  const rl = readline.createInterface({
    output: process.stdout,
    input: process.stdin
  })
  
  // rl.question('how are you?', (answer) => {
  //   console.log('replay:' + answer)
  //   rl.close()
  // })
  
  function rlInfo(question) {
    return new Promise((resolve, reject) => {
      rl.question(question, (answer) => {
        resolve(answer)
      })
    })
  }
  
  async function createFile() {
    let name = await rlInfo('tell me your name?')
    let age = await rlInfo('tell me your age?')
    let job = await rlInfo('tell me your job?')
    let address = await rlInfo('tell me your address?')
  
    let info = `{
      "name" : "${name}",
      "age": "${age}",
      "job": "${job}",
      "address": "${address}"
    }`
  
    await fsWrite('./desc.json', info)
    rl.close()
  }
  
  createFile()
  rl.on('close', () => {
    process.exit(0)
  })
  ```

#### 流式文件

+ `stream`

  ```js
  const fs = require('fs')
  // ws 写入流对象 createWriteStream(path, [,options])
  const ws = fs.createWriteStream('hello.txt', { flags: "w", encoding: 'utf-8' })
  console.log(ws)
  // 监听文件打开
  ws.on('open', () => {
    console.log('files open')
  })
  // 监听文件文件关闭
  ws.on('close', () => {
    console.log('filse done')
  })
  // 写入流
  ws.write('some words 1', (err) =>{
    if (err) return
    console.log('success')
  })
  // 写入结束
  ws.end(() => {console.log('end')})
  
  
  // 读取流对象 createReadStream(path, [,options]) 
  const rs = fs.createReadStream('hello.txt', { flags: 'r', encoding: 'utf-8' })
  
  // console.log(rs)
  
  
  rs.on('open', () => {
    console.log('open')
  })
  // 读取的文件片段 chunk.length 片段的长度 chunk 流的片段
  rs.on('data', (chunk) => {
    console.log('data:' + chunk.length)
  })
  
  rs.on('close', () => {
    console.log('close');
  })
  // 管道流 读取流与写入流
  rs.pipe(ws)
  ```

####  `Node`事件

+ 发布订阅

  ```js
  const fs = require('fs')
  const path = './hello.txt'
  fs.readFile(path, { flag: 'r', encoding: 'utf-8' }, (err, data) => {
    if (err) return
    // 订阅 $emit('事件名')
    obServer.$emit('success', data)
  })
  // 定义一个总线对象 存储发布订阅的方法 和事件池
  const obServer = {
    eventArr: {
      // 事件池 系列事件的执行
      // event: []
    },
    // 监听对应事件
    $on: function(event, fn) {
      if (this.eventArr[event]) {
        this.eventArr[event].push(fn)
      } else {
        this.eventArr[event] = [fn]
      }
    },
    // 触发订阅的事件
    $emit: function(event, info) {
      if (this.eventArr[event]) {
        this.eventArr[event].forEach(item => {
          item(info)
        })
      }
    }
  }
  // 事件触发
  obServer.$on('success', (info) => {
    console.log('1:' + info)
  })
  
  obServer.$on('success', (info) => {
    console.log('2:' + info)
  })
  
  obServer.$on('success', (info) => {
    console.log('3:' + info)
  })
  ```

#### `Path` 模块

+ 路径相关

  ```js
  const path = require('path')
  
  // console.log(path)
  path.resolve(str) // 拼接路径
  path.join() // 
  // __dirname 获取当前文件所属目录的绝对路径 文件夹的路径
  console.log(__dirname)
  // __filename 获取当前文件的绝对路径 文件的路由
  console.log(__filename)
  ```

#### `url` 模块

+ `url`

  ```js
  const url = require('url')
  
  const source = 'https://www.bilibili.com/video/BV1i7411G7kW?p=9'
  // parse 解析源目标网址
  const urlObj = url.parse(source)
  
  console.log(urlObj)
  
  /* Url {
    protocol: 'https:',
    slashes: true,
    auth: null,
    host: 'www.bilibili.com',
    port: null,
    hostname: 'www.bilibili.com',
    hash: null,
    search: '?p=9',
    query: 'p=9',
    pathname: '/video/BV1i7411G7kW',
    path: '/video/BV1i7411G7kW?p=9',
    href: 'https://www.bilibili.com/video/BV1i7411G7kW?p=9'
  } */
  
  // resolve 拼接网址
  const target = 'http://www.wokemeimeiyoukong.com'
  
  const concatUrl = './fangmj?name=wokemeiyoukong'
  
  const newUrl = url.resolve(target, concatUrl)
  
  console.log(newUrl) // http://www.wokemeimeiyoukong.com/fangmj?name=wokemeiyoukong
  ```

#### 下载图片

+ `cheerio`: 操作 `dom` 第三方库

  ```js
  const axios = require('axios')
  const cheerio = require('cheerio')
  const fs = require('fs')
  const path = require('path')
  
  const imgPath = './img'
  
  // axios 简易封装
  function handleFecth({method='get', url, data}) {
    return axios({
      method,
      url,
      data: {
        ...data
      }
    })
  }
  
  async function axiosFecth(url) {
    try {
      // 首页的dom
      const res = await handleFecth({ url })
      const { status, data } = res
      if (status === 200) {
        // cheerio.load 返回一个 jq封装的对象
        const $ = cheerio.load(data)
        // jq 查找dom 元素
        $('#home .col-sm-9>a').each((i, ele) => {
          const imgHerf = $(ele).attr('href')
          let title = $(ele).find('.random_title').text()
          // 正则匹配
          const reg = /(.*?)\d/igs
          title = reg.exec(title)[1]
          fs.mkdir(`${imgPath}/${title}`, (err) => {
            if(err) return
          })
          imgFecth(imgHerf, title)
        })
      }
    } catch (error) {
      console.log(error, 'axios require fault')
    }
  }
  
  async function imgFecth(url, title) {
    try {
      // 详情页的dom
      const res = await handleFecth({url})
      const { status, data } = res
      if (status === 200) {
        const $ = cheerio.load(data)
        $('.pic-content img').each((i, ele) => {
          const imgSrc = $(ele).attr('src')
          const extname = path.extname(imgSrc)
          const imgName = `${imgPath}/${title}/${title}-${i}${extname}`
          // 创建写入流 imgName 写入流的名称 path
          const ws = fs.createWriteStream(imgName)
          axios.get(imgSrc, { responseType: 'stream' }).then(res => {
            // 写入内容
            res.data.pipe(ws)
            console.log(`${title} downLoad success`)
            res.data.on('close', () => {
              ws.close()
            })
          })
        })
      }
    } catch (error) {
      console.log(error)
    }
  }
  const source = 'https://www.doutula.com/'
  
  axiosFecth(source)
  ```

#### 爬取音乐

+ 爬虫网络音乐

  ```js
  const axios = require('axios')
  const fs = require('fs')
  const path = require('path')
  const {handleFecth} = require('./utils/tools')
  
  const httpUrl = 'http://www.app-echo.com/api/recommend/sound-day?page='
  
  async function fetchMusic(page) {
    try {
      const url = `${httpUrl}${page}`
      const res = await handleFecth({ url })
      const { status, data } = res
      const { list } = data
      if (status === 200) {
        list.forEach((item, i) => {
          const { name, source } = item.sound
          const info = `${name}-${source}\n`
          const filename = path.parse(source).name
          const ext = path.parse(source).ext.slice(0, 4)
          console.log(filename)
          // 获取到的info 写入 txt 中
          // fs.writeFile('./static/info.txt', info, { flag: 'a', encoding: 'utf-8' }, (err, data) => {
          //   if(err) return
          //   console.log('success' + name)
          // })
          musicSave(source, filename, ext)
        })
      }
    } catch (error) {
      console.log(error)
    }
  }
  
  async function musicSave(url, filename, ext) {
    // 指定文件类型为 流数据
    const res = await axios.get(url, { responseType: 'stream'})
    const info = `./static/mp3/${filename}${ext}`
    // createWriteStream 参数路径下对应的文件名称 
    const ws = fs.createWriteStream(info)
    // 管道流写入文件
    res.data.pipe(ws)
    res.data.on('close', () => ws.close())
  }
  
  fetchMusic(1)
  ```

  

#### `http` 模块

+  本地服务器

  ```js
  // http 模块
  const server=require('http');
  const myserver =server.createServer();
  // 端口打开请求地址
  myserver.on('request',function(request,response){
    // res.end 返回内容 设置请求头返回字符 图片 html
    // Content-Type: text/html text/plain
    // response.setHeader('Content-Type','image/jpeg');
    response.end('get request index');
  });
  
  // listen开启服务 可以开启多个端口，但是开启的端口不能重复
  myserver.listen(3000,function(){
  
    console.log('服务器启动成功');
  });
  
  ```

+ 读取静态文件

  ```js
  const http = require('http')
  const server = http.createServer()
  const fs = require('fs')
  const path = require('path')
  const url = require('url')
  function resolve(dir) {
    return path.join(__dirname, dir)
  }
  const targetPath = resolve('static')
  
  server.on('request', (req, res) => {
    const reqUrl = req.url
    let contentType = ''
    const pathObj = url.parse(reqUrl)
    let filePath = targetPath + pathObj.path
    // 设置请求头
    if (reqUrl.indexOf('image')) {
      contentType = 'image/jpeg'
      filePath = filePath + '.jpg'
    } else {
      contentType = 'text/html; charset=utf-8'
    }
    // fs.createReadStream(filepath).pipe(res) 流式写入读取文件
    fs.readFile(filePath, (err, data) => {
      if (err) {
        res.end('not found')
      } else {
        res.setHeader('Content-type', contentType)
        res.end(data)
      }
    })
  })
  
  server.listen('2223', () => {
    console.log('server start');
  })
  ```

#### `node/mysql`

+ `node` 中执行 `mysql` 相关操作

  ```js
  // npm package mysql 
  const mysql = require('mysql')
  // 连接数据库的配置项
  let options = {
    host: 'localhost',
    port: '3306',
    user: 'root',
    password: '123456',
    database: 'fangmj' // database已创建时使用
  }
  // 创建连接对象
  const local = mysql.createConnection(options)
  // 连接数据库
  local.connect((err) => {
    if (err) return err
    console.log('Database connection successful');
  })
  
  // sql查询语句
  let sqlStr = 'select * from fangmj_user'
  
  local.query(sqlStr, (err, result, fields) => {
    if (err) return err
    console.log(result)
    // [
    //   RowDataPacket { id: 1, name: 'xx', age: 18, job: 'coder' },
    //   RowDataPacket { id: 2, name: 'yy', age: 20, job: 'model' }
    // ]
    console.log(fields)
  }) 
  // 删除表
  let sqldel = 'drop table fangmj_user'
  
  local.query(sqldel, (err, result, fields) => {
    if (err) return err
    console.log(result);
  }) 
  // 删除库
  let sqldelbase = 'drop database fangmj'
  
  local.query(sqldelbase, (err, result, fields) => {
    if (err) return err
    console.log(result);
  })
  
  // 创建库
    let sqlcrebase = 'create database local'
  
    local.query(sqlcrebase, (err, result, fields) => {
      if(err) return err
      console.log(result);
    })
  
    // 表中插入数据
    let sqlinsert = "insert into user_info (id, name, password, age) values (1, 'fangmj', '123654', '26')"
  
    local.query(sqlinsert, (err, result, fields) => {
      console.log(err);
      console.log(result);
    })
  
    // 占位符填充
    let sqlinsert = "insert into user_info (name, password, age) values (?,?,?)"
  
    local.query(sqlinsert,['xx', '123', '25'], (err, result, fields) => {
      console.log(err);
      console.log(result);
    })
  ```



+ `sql` 查询语句

  ```sql
   select * from table where fields condition
    
     -- 从user_info表中 查询 字段 age大于 26的数据
    select * from user_info where age > 26;
    
     -- 从user_info表中 查询 字段 age 在 26 - 30 间的数据
    select * from user_info where age between 26 and 30
    
     -- 从user_info表中 查询 字段 author 为 xx或者yy 的数据
    select * from user_info where author in ('xx', 'yy')
    
     -- 从user_info表中 查询 字段 author 为 xx或者yy 的数据
    select * from user_info where author='xx' or author='yy'
    
     -- 从user_info表中 查询 字段 name 包含 'xx' 的数据 %任意个字符
    select * from user_info where name like '%xx%'
    
     -- 从user_info表中 查询 字段 name 以'xx' 开头的数据 %任意个字符
    select * from user_info where name like 'xx%'
    
    -- 从user_info表中 查询 字段 name 以'xx' 开头的+3个字符数据 _一个字符 几个_ 几个字符
    select * from user_info where name like 'xx___'
    
     -- 从user_info表中 查询 字段 name 为空的数据
    select * from user_info where name is NUll
    
     -- 从user_info表中 查询 字段 name 不为空的数据
    select * from user_info where name is not NUll
    
    -- 更新表中的数据 set 设置更改的字段 where 定位到具体数据
    update user_info set name = 'xx' where id = 1
  ```

+ 关系型数据库的范式
  + 第一范式
  + 第二范式
  + 第三范式
  + 范式为了更好建立表中的数据关系，减少多余依赖，表的结构冗余

+ 外键

  + 多张表的数据之间存在关联，通过外键建立联系

+ 连表查询

  + 表与表存在关联数据时

  + `A inner join B` 全关联

    ```sql
    -- A inner join B A B表中的数据存在关联 A.id = B.oth_id 关联条件
    select * from A inner join on B A.id = B.oth_id  
    -- 带条件查找数据
    select * from A inner join on B A.id = B.oth_id where A.id = 'xx' 
    
    ```

  + `A left join B`  以 `A` 表 为主  不存在的字段以 `null` 填充

  + `A right join B`  以 `B` 表 为主  不存在的字段以 `null` 填充

    ```sql
    -- A left join B 
    select * from A left join on B A.id = B.oth_id  
    -- 带条件查找数据
    select * from A right join on B A.id = B.oth_id where A.id = 'xx' 
    ```

  + 多表关联查询 

    ```sql
    -- 嵌套查询
    select * from A 
    inner join on B A.id = B.oth_id 
    inner join C on C.id = = B.oth_id 
    where C.id = 1 
    ```

  + 同一张表的自关联查询

    ```sql
    -- 同一张表中存在自关联数据
    select * from A  as a1
    inner join on A as a2 a1.id = a2.oth_id 
    where a1.id = 1 
    ```

  + 子查询

    + 查询语句中嵌套另一个查询语句

    + `exist` 关键字

      ```sql
      select * from A  as a1
      inner join on A as a2 a1.id = a2.oth_id 
      in (select * from C where age < 20)
      ```

      

  



