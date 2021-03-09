#### 节点滚动

+ `scrollTop`

+ 元素滚动对应的是节点滚动条的位置 子元素超出父元素 滚动条设置在父元素上 对应的 `sctollTop` 应设置的父元素上

+ 获取 节点的 `sctollTop`

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
      #wrap {
        width: 200px;
        height: 400px;
        overflow: auto;
      }
      .child {
        width: 100%;
        height: 1000px;
        background-color: #ccc;
        display: flex;
        justify-content: center;
        align-items: center;
      }
    </style>
  </head>
  <body>
    <div id="wrap">
      <div class="child">
        zhanshi enzi
      </div>
    </div>
    <button>click</button>
    <script>
     	// wrap 
      const wrap = document.getElementById('wrap')
      const btn = document.querySelector('button')
      btn.onclick = function() {
        wrap.scrollTop += 10
        debugger
      }
      // 获取节点的 sctollTop
        const dis = document.documentElement.sctollTop || window.pageYoffSet || document.body.sctollTop
        
      // scrollingElement 标准模式和混杂模式 获取均可
    </script>
  </body>
  </html>
  ```

  