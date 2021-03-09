#### `Express`

+ 基于 `node.js`  `web` 开发框架

  ```js
  npm i express --save
  ```

+ 启动

  ```js
  //引包
  const express = require('express');
  //创建Server
  const app = express();
  //设置可访问目录
  //开发公共资源 访问public文件夹下的静态文件
  app.use('/public/', express.static('./public'));
  
  //根据请求配置 根路径返回内容
  app.get('/', function (req, res) {
    res.send('Hello Express');
  });
  
  app.get('/about', function (req, res) {
    console.log(req.query);
    res.send('我是 Express！！！！！');
  });
  
  //监听端口
  app.listen(3000, function () {
    console.log('express is Start!!!');
  });
  ```

+ `npm install -g express-generator`

+ `express` 生成器

  ```js
  // npm install -g express-generator
  
  $ express -h
  
    Usage: express [options] [dir]
  
    Options:
  
      -h, --help          输出使用方法
          --version       输出版本号
      -e, --ejs           添加对 ejs 模板引擎的支持
          --hbs           添加对 handlebars 模板引擎的支持
          --pug           添加对 pug 模板引擎的支持
      -H, --hogan         添加对 hogan.js 模板引擎的支持
          --no-view       创建不带视图引擎的项目
      -v, --view <engine> 添加对视图引擎（view） <engine> 的支持 (ejs|hbs|hjs|jade|pug|twig|vash) （默认是 jade 模板引擎）
      -c, --css <engine>  添加样式表引擎 <engine> 的支持 (less|stylus|compass|sass) （默认是普通的 css 文件）
          --git           添加 .gitignore
      -f, --force         强制在非空目录下创建
      
    // 生成项目  --view=pug 对应的编译模板
      express --view=pug myapp
      
   // 启动项目
      set DEBUG=myapp:* & npm start
  ```

  