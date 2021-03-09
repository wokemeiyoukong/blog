#### 盒模型

  + 标注模型和IE模型

    - 标准模型：

      ​	` box-sizing:border-box`

      ​	元素宽高：内容 content 的width

    - IE模型：

      ​    `box-sizing:content-box`

      ​	元素宽度：元素内容的width+内边距padding*2+border的宽度

+ `JavaScript` 获取宽高

  - `dom.getBoundingClientRect().width/height`: 元素位于视窗的位置信息的集合

    根据元素在视窗中的绝对位置获取宽高

  - `dom.offsetWidth/offsetHeight`： 返回元素包含 `padding border width` 的宽度

  - `offsetLeft`: 元素相对于（定位的父级元素，父级没有相对于页面）父级元素左侧的绝对位置

  - `offsetTop`: 元素相对于（定位的父级元素，父级没有相对于页面）父级元素顶部的绝对位置

  - `clientX、clientY`: 鼠标事件相对于可视区域X和Y的坐标

  - `pageX pageY`: 鼠标事件相对文档页面的x和y坐标

  - `screenX screenY`: 鼠标事件相对于电脑屏幕的x和y坐标

  - `innerWidth innerHeight`: 当前页面的宽高（滚动页面时获取）

  - `window.pageYoffset`: 页面被卷去的高度

  - `scrollTop`: 元素被滚动的高度

+ 边距重叠

  - 普通元素垂直方向的边距会进行重叠

+ `BFC` 解决边距重叠问题

  1. float 属性不为 none （元素脱离文档流）

  2. position 为 absolute 或者 fixed

  3. display 为 `inline-block,table-cell,table-caption,flex,inine-flex`

  4. overflow 不为 visible

  5. 根元素

     （tips: IE 下为 Layout，可通过 `zoom:1` 触发）

#### 元素水平垂直居中

 + 定宽高元素

   ```html
   <div id='box'>
       <div class='content'></div>
   </div>
   <style>
     /*方法一 负margin：子元素宽高的一半*/
     #box{
         width:200px;
         height:200px;
         background:#ccc;
         position:relative;
       }
     .content{
         width:100px;
         height:100px;
         background:#ff0000;
         position:absolute;
         top,left：50%；
         // 子元素宽高的一半
         margin-left: -50px;
         margin-top: -50px;
       }
   </style>
   ```



 + 不定宽高元素

   ``` html
   <div id='box'>
       <div class='content'></div>
   </div>
   <style>
     /*方法一 margin：auto；自动居中*/
     #box{
         width:100%;
         height:200px;
         background:#ccc;
         position:relative;
       }
     .content{
         width:100px;
         height:100px;
         background:#ff0000;
         position:absolute;
         top,left,right,bottom:0;
         margin:auto;
       }
     /*方法二 css3中属性 兼容性*/
     #box{
         width:100%;
         height:200px;
         background:#ccc;
         position:relative;
       }
     .content{
         width:100px;
         height:100px;
         background:#ff0000;
         position:absolute;
         top,left:50%;
         transform:translateX(-50%) translateY(-50%);
       }
       /*方法二 css3中属性 兼容性*/
     #box{
         width:100%;
         height:200px;
         background:#ccc;
         display:flex;
         justify-content:center;
         align-items:center;
       }
       /* grid 布局*/
        #box{
         width:100%;
         height:200px;
         background:#ccc;
         display:grid;
       }
       .content{
           margin: auto;
       }
   </style>
   ```

#### 选择器优先级

+ 样式的优先级
  + `!important` > 行内样式 > `#id` > `.class` > `tag` > * > 继承 > 默认
  + `vue` 中的样式穿透：>>>（`stylus`）/deep/（`scss/sass`）::v-deep

#### 清除浮动

+ 清除浮动为了解决子元素浮动导致父元素内容高度为0 的情况

+ 新增标签 `<div style='clear:both;'></div>` 解决浮动问题

+ 父元素添加 `overflow:hidden` （触发 `BFC`）

+ 伪元素

  ```css
  /* 单伪类after*/
  .clearfix:after{
  	content: '';
  	display: block;
  	height:0;
   	clear:both;
  	visibility: hidden;
  }
  /* 触发IE下的haslayout*/
  .clearfix{
  	*zoom:1
  }
  /* 使用before和after双伪元素清除浮动*/
  .clearfix:after,.clearfix:before{
  	content:'';
  	display: table;
  }
  .clearfix:after{
  	clear:both;
  }
  .clearfix{
  *zoom:1;
  }
  ```


#### link 与 @import

+ `link` （`html` 标签）可引入的内容较多 `@import`（`css` 规则） 只能引入用于加载 `css` 相关的文件（`IE5` 以上才能使用）
+ `link` 同步加载引入的 `css` ,而 `@import` 引入的 `css` 等到页面加载完才会加载
+ 兼容性
+ `link` 支持使用 `JavaScript` 控制修改样式

#### `flex` 布局

+ 单个`flex` 项目上设置 `margin: auto` 项目水平垂直居中

  ```css
.container {
      display: flex // inline-flex
  }
  .item {
  	margin: auto
  }
  /*
    	place-content: align-content/justify-content 的简写
  	place-items: align-items/justify-items 的简写
    */
  ```



+ 主轴与交叉轴/方向 交叉轴垂直于主轴

  + 主轴

    ```css
    /* 主轴方向水平方向 row 方向向右/方向向左 */
        flex-direction: row-reverse / row
        /* 主轴变成Y轴 column 方向从上到下 / 从下到上 */
        flex-direction: column-reverse / column
    ```

  + 交叉轴

    ```css
    /* flex-wrap: wrap 产生多条轴线 交叉轴地概念 每一行flex元素的对齐和分布方式 如果只有一行 无效果 */
    align-content:stretch/space-betewwn/space-around/flex-start/flex-end
    ```

    

  + 容器

    + 父容器

      ```css
      /* 子元素在主轴上的对齐方式 */
      /* 左对齐 / 右对齐 / 居中 / 两端对齐 项目之间间隔相等 / 均匀排列每个元素中间间隔是左右两端的2倍/ 均匀排列每个元素 */
      justify-content: flex-start/flex-end/center/space-between/space-around/space-evenly

      /* 子元素在交叉轴上的对齐方式 */
      /* 交叉轴起点对齐/终点对齐/居中/拉伸/第一个项目文字基线对齐 */
      align-items: flex-start/flex-end/center/stretch/baseline
      ```

+ 子容器

```css
 /* 子容器单独设置如何沿交叉轴排列 */
  align-self:flex-start/flex-end/center/stretch/baseline
```



+ 子元素的：`float/clear/vertical-align` 失效

+ 子元素的相关属性

  ```css
  order 排列顺序
  flex-grow 放大比例 默认为0
  flex-shrink 缩小比例 默认为1
  flex-basic 占据主轴的空间 默认 auto
  align-self 单项目不同的样式
  ```

+ `order`

  + 子项的 `order` 属性控制子项的排列顺序，越小越靠前

#### 网格布局 `grid`

+ 响应式容器

  ```css
  .box{
      display: grid;
      /* 网格一行排列 */
      grid-template-columns: repeat(3, 1fr);
      /* 行间距 列间距*/
      gird-gap: 1rem;
      /* 行高最小值和内部撑起高度*/
      grid-auto-row: minmax(100px,auto);
      /*子项在交叉纵轴和横轴之间的排列方式*/
      justify-items: stretch;
      align-items: stretch;
      /*自定义区域模板*/
      grid-template-areas:
          'a a a',
          'b b b',
          'c c c'
  }
  .items{
      /*子项跨列 第一列-第三列*/
      grid-column: 1/3;
      /* 子项跨行 第二行 - 第四行*/
      grid-row: 2/4;
      /*单个子项的排列方式*/
      justify-items: center;
      align-items: center;
      /*自定义模板是对应的名称*/
      grid-area: a;
  }
  ```



#### `DOM` 拖拽

+ `DOM` 添加 `draggable = true` 属性，并且阻止接受 `DOM` 的默认事件

+ 拖动元素触发事件

  + `ondragstart ` 用户开始拖动元素时触发
  + `ondrag ` 元素正在拖动时触发
  + `ondragend ` 用户完成元素拖动后触发

+ 释放元素时触发

  + `ondragenter ` 当被鼠标拖动的对象进入其容器范围内时触发此事件
  + `ondragover ` 当某被拖动的对象在另一对象容器范围内拖动时触发此事件
  + `ondragleave ` 当被鼠标拖动的对象离开其容器范围内时触发此事件
  + `ondrop ` 在一个拖动过程中，释放鼠标键时触发此事件

+ 拖动对象之间传递数据

  ```js
  // HTML5为所有的拖动相关事件提供了一个新的属性：
  e.dataTransfer { }
  // 功能：用于在源对象和目标对象的事件间传递数据
  //源对象上的事件处理中保存数据：
  e.dataTransfer.setData( k, v );
  //目标对象上的事件处理中读取数据：
  var v = e.dataTransfer.getData( k );
  ```



  ```html
  <body>
      <h3>从这个盒子</h3>
      <div class="from">
          <div class="move" draggable="true"></div>
      </div>
      <h3>到这个盒子</h3>
      <div class="to"></div>
  </body>
  <script>
      var from = document.querySelector(".from");
      var to = document.querySelector(".to");
      var move = document.querySelector(".move");

      // drag:拖， over：经过， 就是红盒子拖拽到它身上时会调用
      //默认是不接受红盒子的，如果要想让它接收，需要禁用默认事件
      to.ondragover = function(target) {
          //禁用默认事件
          target.preventDefault();
      }
      //drop： 放下， 松手
      to.ondrop = function() {
          to.appendChild(move);
      }
      from.ondragover = function(target) {
          //禁用默认事件
          target.preventDefault();
      }
      //drop： 放下， 松手
      from.ondrop = function() {
          from.appendChild(move);
      }
  </script>
  ```

#### `MutationObserver`

+ `h5` 新增，在所有`dom` 更新后执行回调函数

#### `响应式开发`

+ `rem less/scss` 媒体查询 `1rem = 1 fontsize`

  + `rem` 相对于浏览器根元素的`font-size` 大小动态取值的

  + 以 `750px` 的设计稿为基准，将屏幕划分为15等份，`1rem = 750px/15等份`

  + `less/scss` 设置变量，计算功能可以完成rem的转换 `width px/@baseFont`

  + 通过媒体查询设置不同屏幕的`fontsize` 达到对页面进行比例缩放

    ```css
    @media screen and (min-width: 320px){ font-size: 320px/15}
    @media screen and (min-width: 640px){ font-size: 640px/15}
    @media screen and (min-width: 750px){ font-size: 750px/15}
    ```

+ `flexble.js rem`  媒体查询 手淘团队的`js` 适配方案

  + 将屏幕划分为10等份，动态计算`fontsize` 的值

    ```css
    /*页面超过750px 设置fontsize大小*/
    @media screen and (min-width:750px){font-size:50px}
    ```

+ `vh vw` 方案

  + 将视觉视口宽度和高度 `window.innerWidth/window.innerHeight` 等分为100份

+ 横屏

  + `js` 横屏检测 `orientation`

    ```js
    window.addEventListener("resize", ()=>{
    if(window.orientation === 180|| window.orientation === 0) {
    // 正常方向或屏幕旋转180度
            console.log('竖屏');
    };
    if(window.orientation === 90|| window.orientation === -90){
    // 屏幕顺时钟旋转90度或屏幕逆时针旋转90度
            console.log('横屏');
    }
    });
    ```

  + `css` 横屏

    ```css
    @media screen and (orientation: portrait) {
    /*竖屏...*/
    }
    @media screen and (orientation: landscape) {
    /*横屏...*/
    }
    ```



#### 移动端适配

+ 设备像素比： 物理像素和设备独立像素的比值

+ 视口：`viewport` 代表当前可见的计算机图形区域

  + 布局视口：网页布局的基准窗口 `pc` 中布局视口等于当前浏览器的窗口大小（不包括边框 外边距 滚动条）
    + `document.documentElement.clientWidth/clientHeight` 获取布局视口的大小
  + 视觉视口：通过屏幕真实看到的区域 默认等于当前浏览器的窗口大小（包括滚动条宽度）
    + `window.innerWidth/innerHeight`
  + 理想视口：网站页面在移动端展示的理想大小
    + `screen.width/height`

+ `Meta viewport`

  + `<meta>` 浏览器如何解析页面

    ```html
    <meta
    name="viewport" content="width=device-width; initial-scale=1; maximum-scale=1; minimum-scale=1; user-scalable=no;"
    ```

+ 浏览器相关属性

  + `window.innerHeight` 浏览器视觉视口高度 （包括垂直滚动条）
  + `window.outerHeight` 浏览器窗口外部的高度 整个浏览器窗口的高度（侧边栏窗口镶边和窗口大小的边框）
  + `window.screen.Height` 屏幕理想视口的高度
  + `window.screen.availHeight` 浏览器窗口可用高度
  + `document.documentElement.clientHeight` 浏览器布局视口的高度 （包括内边距 不包括垂直滚动条 边框 外边距）
  + `document.docementElement.offsetHeight` 包括内边距 滚动条 边框和外边距
  + `document.documentElement.scrollHeight`

+ `1px` 边框

  + `border-image`

    ```css
    .border_1px{
              border-bottom: 1px solid #000;
    }
    @media only screen and(-webkit-min-device-pixel-ratio:2){
    .border_1px{
                    border-bottom: none;
                    border-width: 001px0;
                    border-image: url(../img/1pxline.png) 0020stretch;
    	}
    }
    ```

  + `background-image`

    ```css
    .border_1px{
              border-bottom: 1px solid #000;
    }
    @media only screen and(-webkit-min-device-pixel-ratio:2){
    .border_1px{
                    background: url(../img/1pxline.png) repeat-x left bottom;
                    background-size: 100% 1px;
    	}
    }
    ```

  + 伪类 + `transform`

    ```css
    .border_1px:before{
              content: '';
              position: absolute;
              top: 0;
              height: 1px;
              width: 100%;
              background-color: #000;
              transform-origin: 50% 0%;
    }
    @media only screen and(-webkit-min-device-pixel-ratio:2){
    .border_1px:before{
                    transform: scaleY(0.5);
    	}
    }
    @media only screen and(-webkit-min-device-pixel-ratio:3){
    .border_1px:before{
                    transform: scaleY(0.33);
    	}
    }
    ```



#### border-radius 背景色覆盖

+ 背景色覆盖圆角样式

+ 为了让子节点不超出容器圆角边框，容器的overflow属性必须是除visible之外的其他值（比如 auto, hidden, scroll...）

  ```css
  // 添加 overflow: auto 属性解决
  border-radius: 30upx;
  overflow: auto;
  ```

#### `box-shadow ` 被覆盖

+ 层级

  ```css
  // 层级
  position:relative; z-index:100
  // 旋转
  transform: translate3d(0, 0, 0)
  ```

#### 文字超出省略

+ 多行

  ```css
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 5; // 第五行省略
  ```

#### 宽度自适应

+ 单行的宽度自适应

  ```css
  width: fit-content
  ```

#### 移动端 `1px` 边框

+ `retina` 屏下 屏幕分辨率 `dpr: 2 || 3` 的情况下， `1px` 变粗，像素问题，普通屏 `1css` 像素对应 `1` 个物理像素， 2倍屏 `4`个物理像素

+ `SVG` 方案

  + `postcss-write-svg` 插件将 `css` 中的 `svg` 函数生成的图像处理成 `base64`

  ```css
  /* src/index.css */
  @svg custom-name {
    width: 4px;
    height: 4px;
     @rect {
         fill: transparent;
         width: 100%;
         height: 100%;
         stroke-width: 1;
         stroke: var(--color, black);
      }
  }
  .svg-retina-border {
      border: 1px solid;
      border-image: svg(custom-name param(--color green)) 1 repeat;
  }
  .normal-border {
      border: 1px solid green;
  }
  ```



+ 伪类元素

  ```css
  // index.css
  .retina-border {
  	position: relative;
  }
  .retina-border::before {
  	content: '';
  	position: absolute;
      width: 100%;
      height: 100%;
      transform-origin: left top;
      box-sizing: border-box;
      pointer-events: none;
      border-width: 1px;
      border-style: solid;
      border-color: #333;
  }
  @media (-webkit-min-device-pixel-ratio: 2), (min-resolution: 2dppx) {  .retina-border::before {
  	width: 200%;
      height: 200%;
      transform: scale(0.5);
      }
  }
  @media (-webkit-min-device-pixel-ratio: 3), (min-resolution: 3dppx) {  .retina-border::before {
  	width: 300%;
      height: 300%;
      transform: scale(0.33);
      }
  }
  .normal-border {
  	border: 1px solid #333;
  }
  ```



#### `nth-of-type`

+ 同类型中的第 `n` 个同级元素

#### `word-wrap & word-break`

```css
word-wrap（overflow-wrap）控制长度超过一行的单词是否被拆分换行，是word-break的补充，它有两个值：normal | break-word
word-break，控制单词如何被拆分换行。它有三个值：normal | break-all | keep-all
```

