# vue

`$mount()`的本质：将`vdom转换为dom`

## 开发环境的跨域（`反向代理`）

开发环境的跨域，vue-cli为我们在本地开启了一个服务，通过这个服务`代理请求`，解决跨域。

- 当请求地址中有/api时，就会`触发代理机制`
- 本地前端--->本地后端--->代理我们向另一个服务器发请求

## mvvm（vue）

mvvm是==双向数据绑定==的。即数据改变影响视图，视图改变影响数据。

- m：model，数据层
- v：view，视图层
- vm：viewModel，数据和视图监听层
  - 每当创建一个vue实例，就相当于创建了一个viewModel监听器（可以监听对应视图或数据的相互改变）



## mvc（react）

mvc是==单向数据绑定==的。即数据改变影响视图，但视图改变不能影响数据，需要自己在控制层基于change事件实现数据的更改。

## vue数据响应式

1. Object.defineProperty()
2. Vue.util.defineReactive()
3. Vue.observable()

## 创建vue组件

1. Vue.extend()：返回`组件构造函数`
2. Vue.component()
3. new Vue()：借鸡生蛋
   - $mount的本质：将`vdom转为dom`

## 动态组件

- <component :is=''/>

## 组件通信

1. props
2. $emit/$on
3. event bus
   - 事件的核心是`发布订阅`，事件的派发者和监听者是`同一个角色`。
4. vuex
5. $parent/$children
6. $root/$refs
7. provide/inject（不是响应式的，但可传递响应式对象）
   - provide(){return { f orm:this }}，传递响应式组件实例
   - inject：{ }，类似props
8. `非prop`特性：（`爷孙`通信，父亲`原封不动`的传递）
   - $attrs：`v:bind="$attrs"`，展开属性，并且原封不动的传递给孙子
     - inheritAttrs：false，禁用`根元素`的attribute继承
   - $listeners：`v-on="$listeners"`，展开事件，，并且原封不动的传递给爷爷





## 插槽

插槽：`内容分发`

具名插槽：将内容分发到子组件指定位置

作用域插槽：分发内容要用到`子组件中的数据`。

## 修饰符

- sync：`this.$emit('update：prop'，值)`，sync修饰符是子组件改变父组件数据的一个语法糖
- native：原生事件





# vue-cli

@表示src目录，若需在样式表中使用@别名，需要加上一个`~`

# vue-router

路由别名：`alias`

嵌套路由： 嵌套路由里的path`不要加/`

二级路由的path为空时，此时它表示二级路由的默认路由。

动态路由：this.$route.params

get传参：this.$route.query



路由跳转：

- this.$router.push({name:""，query:{ }}) //get传值
- this.$router.push({name:""，params:{ }})    //动态传值
- this.$router.push({path:""，params:{ }}) 

## 路由导航守卫

- next()：放行

- next(false)：终止跳转

- next(地址)：跳到某个地址

  

  

  

  

  

  

  

  

  

# vuex

## state

## getters

## mutations

- this.$store.commit('mutations'，payload)

## actions

- this.$store.dispatch('actions'，payload)

## modules

模块中的命名空间：

- `namespaced:true`：开启命名空间
- 默认情况下，`模块内部`的`mutations、actions、getters`是注册在`全局命名空间`的。

访问命名空间：

- 直接调用：this.$store.dispatch('模块名/方法')
- 辅助函数：...mapMutations('模块名'，['mutations'])
- 创建基于某个命名空间的辅助函数：`createNamespacedHelpers('模块名')`









# CSR & SSR

## CSR（客户端渲染）

- 服务器返回一个空的页面结构
- 客户端加载执行对应的js
- 客户端操作dom完成解析替换



## SSR（服务端渲染）

- 在服务端完成对页面的解析替换

- 直接把结果发给客户端

- 客户端只需负责展示

  - 模板引擎的本质：==字符串的解析替换==

  - res.render(模板文件，模板数据)：

    - 读取模板文件
    - 调用模板引擎解析替换
    - 把结果发送给客户端

    

## SEO

- 客户端渲染不利于SEO，因为爬虫不是浏览器，它拿到的是未处理的页面
- 服务端渲染利于SEO



## SPA（单页面应用程序）

- 单页面应用程序都是客户端渲染出来的
- 首次访问比较慢