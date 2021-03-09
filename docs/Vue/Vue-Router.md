#### 路由守卫

+ 修改页面的`title`

  ```js
  {
      path: '/title',
      meta: {
  		title: 'title'
      },
          // 路由独享的守卫
      beforeEnter: (to, from, next) => {
          // ...
      },
      component: () => import('@/title')
  }
  // 全局路由守卫
  Router.beforeEach(to,from,next) {
      if(to.meta.title) {
          window.title = to.meta.title
          next()
      }
      next()
  }
  ```

+ 权限判断（登录跳转）

  ```js
  {
      path: '/title',
      meta: {
  		login: true
      },
      component: () => import('@/title')
  }
  // 全局路由守卫
  Router.beforeEach(to,from,next) {
      if(to.meta.login) {
          next({
               path: '/login',
              // query 登录后返回原界面
               query: { redirect: to.fullPath }
          })
      }
      next()
  }
  ```

+ 组件路由守卫

  ```js
  const Foo = {
    template: `...`,
    beforeRouteEnter (to, from, next) {
      // 在渲染该组件的对应路由被 confirm 前调用
      // 不！能！获取组件实例 `this`
      // 因为当守卫执行前，组件实例还没被创建
    },
    beforeRouteUpdate (to, from, next) {
      // 在当前路由改变，但是该组件被复用时调用
      // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
      // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
      // 可以访问组件实例 `this`
    },
    beforeRouteLeave (to, from, next) {
      // 导航离开该组件的对应路由时调用
      // 可以访问组件实例 `this`
        const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
        if (answer) {
          next()
        } else {
          next(false)
        }
    }
  }
  ```

#### 路由

+ 命名视图

  ```js
  const routers = [
      {
          path: '/a',
          components:{
              default: 'PageA',
              footer: 'Footer'
          }
      },
      // 路由通配符 路由不匹配时加载 404页面
      {
          path: '*,
          components: '404
      }
  ]
  // 使用
  <router-view /> // 默认渲染PageA
  <router-view name='footer'/> // 渲染Footer组件
  ```

+ 动态参数

  ```js
  const routers = [
      {
          path: '/a:id',
          component: 'PageA',
          props: true
      }
  ]
  // 匹配为 /a/123
  this.$route.parmas.id // 123
  props: ['id']
  this.id // 123
  
  // /a/123?name=xx&age=18
  this.$route.query // 参数对象
  ```

#### 路由跳转页面滚动

+ 浏览器原生的前进后退 在栈中获取地址 `url` 会保存上次浏览的位置

+ 支持 `history.pushState` 的浏览器中可以

+ `scrollBehavior `  `keep-alive` 包裹的组件无法定位滚动，页面缓存了位置信息

  ```js
  // 在路由文件中新增
  // savedPosition popState导航时触发
  // to 路由跳转目标地址 from 源地址
  scrollBehavior (to, from,savedPosition ) {
      // 返回顶部 
  	return {x: 0, y: 0}
      
      // 
      if(savedPosition) {
          return savedPosition
      }
      // 异步滚动
      return new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve({ x: 0, y: 0 })
          }, 500)
        })
  }
  // 全局路由钩子函数中滚动
  router.afterEach((to, from, next) => {
      window.scrollTo(0,0)
  })
  ```

#### `route & router`

+ `route`: 路由对象
  + 当前激活的路由的状态信息，包含了当前 URL 解析得到的信息，还有 URL 匹配到的路有记录
  + 对象传参时刷新页面，路由传参数据解析为`object` 传递时需要 `json` 转义
  
+ `router`： 全部路由信息 路由的跳转 页面的挂载

  + `params & query`

    ```js
    // params 刷新丢失
    // query 拼接url 对象传参刷新 object类型 转字符串存储
    ```

    

#### 登陆验证

+ `token/cookies` 验证用户信息

  ```js
  router.beforeEach((to, from, next) => {
      // 如果有token 说明该用户已登陆
      if (localStorage.getItem('token')) {
          // 在已登陆的情况下访问登陆页会重定向到首页
          if (to.path === '/login') {
              next({path: '/'})
          } else {
              next({path: to.path || '/'})
          }
      } else {
          // 没有登陆则访问任何页面都重定向到登陆页
          if (to.path === '/login') {
              next()
          } else {
              // 携带页面跳转信息 login页面做处理
              next(`/login?redirect=${to.path}`)
          }
      }
  })
  ```

  

#### 页面权限

+ `meta` 中增加角色信息，路由守卫中判断是否拥有访问权限

  ```js
  const routes = [
      {
          ...,
          meta: {
          	roles: ['admin', 'user']
  	    }
      },
          {
              ...,
                  meta: {
                      roles: ['admin']  
                  }
          }
  ]
  
  router.beforeEach((to, from, next) => {
      if(to.meta.roles.includes(role)) {
          next()
      }else {
          next({path: '/404'})
      }
  }) 
  ```

  