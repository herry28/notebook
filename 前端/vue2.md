# vue





## mvvm（vue）

mvvm是==双向数据绑定==的。即数据改变影响视图，视图改变影响数据。

- m：model，数据层
- v：view，视图层
- vm：viewModel，数据和视图监听层
  - 每当创建一个vue实例，就相当于创建了一个viewModel监听器（可以监听对应视图或数据的相互改变）



## mvc（react）

mvc是==单向数据绑定==的。即数据改变影响视图，但视图改变不能影响数据，需要自己在控制层基于change事件实现数据的更改。



## 创建vue组件

1. Vue.extend()：返回`组件构造函数`
2. Vue.component()
3. new Vue()：借鸡生蛋
   - $mount的本质：将`vdom转为dom`



# 组件通信

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

## props

## $emit/$on

## event bus



# 插槽

插槽：`内容分发`

具名插槽：将内容分发到子组件指定位置

作用域插槽：分发内容要用到`子组件中的数据`。







# vue-cli

# vue-router

路由别名：`alias`

嵌套路由： 嵌套路由里的path`不要加/`

动态路由：this.$route.params

get传参：this.$route.query

路由跳转：

- this.$router.push({name:""，query:{ }}) //get传值

- this.$router.push({name:""，params:{ }})    //动态传值

- this.$router.push({path:""，params:{ }}) 

  

  

  

  

  

# vuex

## state

## getters

## mutations

## actions

## modules









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