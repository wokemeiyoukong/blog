#### `html` 转化生成 `pdf`

+ `html2canvas`

+ `jsPDF`

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/html2canvas/0.5.0-beta4/html2canvas.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/jspdf/1.5.3/jspdf.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
  </head>
  <style>
    .title {
      color: red;
    }
    .info span {
      color: greenyellow;
    }
  </style>
  <body>
    <div id="area">
      <div class="title">title</div>
      <div class="info">
        <span>words area </span>
        <span>words area </span>
        <span>words area </span>
        <span>words area </span>
        <span>words area </span>
        <span>words area </span>
      </div>
    </div>
    <button class="btn">click</button>
  </body>
  <script>
    const btn = document.querySelector('.btn')
    const doc = new jsPDF('p', 'mm', 'a4')
    // doc.internal.scaleFactor = 10;
    // 配置项
    var options = {
             pagesplit: true, //设置是否自动分页
            "background": '#FFFFFF'   //如果导出的pdf为黑色背景，需要将导出的html模块内容背景 设置成白色。
        };
    var printHtml = $('#area').get(0);
    btn.addEventListener('click', () => {
      doc.addHTML(printHtml,15, 15, options,function() {
        doc.save('1.pdf');
        });
    })
  </script>
  </html>
  ```

  