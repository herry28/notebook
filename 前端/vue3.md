

# vue3新特性

- 性能比vue2快1.2-2倍
- 按需编译，体积比vue2小
- 组合API（类似React Hooks）
- 更好的ts支持
- 暴露了自定义渲染API
- 更先进的组件
- 数据响应式重新实现
- 源码使用typescript重新编写（更好的类型推导）
- 虚拟dom新算法
- 提供了composition  api，为了更好的逻辑复用和代码组织
- 自定义渲染器
- fragment，模板可以有多个根元素







# 创建vue3项目

- vue-cli

- vite

- - vite是vue作者开发的一款意图取代webpack的工具，其实现原理是利用es6的import会发送请求去加载文件的特性，拦截这些请求，做一些预编译，省去webpack冗长的打包时间
  - npm install -g create-vite-app








# vue3是如何变快的？

- - diff算法优化：

  - - vue3新增了静态标记

  - 静态提升：

  - - vue2中无论元素是否参与更新，每次都会重新创建
    - vue3中对于不参与更新的元素，只会被创建1次，之后在每次渲染时被不停的复用

  - 事件监听器缓存：

  - - 默认情况下onClick会被视为动态绑定，所以每次都会去追踪它的变化，但是因为是同一个函数，所以没有追踪变化，直接缓存起来复用即可

  - ssr渲染：

  - - 当有大量静态内容时，这些内容会被当做纯字符串推进一个buffer中。即使存在动态绑定，会通过模板插值嵌入进去。这样会比虚拟dom渲染快很多
    - 静态内容大到一定量级时，会用_createStaticNode()在客户端去生成一个static node，这些静态节点会被直接innerHtml。就不需要创建对象，然后根据对象渲染。




# 响应式原理

## vue3响应式原理

vue3使用ES6中的==Proxy==语法实现响应式数据。底层是用Proxy的effect、trigger。

- 优点：
  - 可以监测到==代理对象属性的动态添加和删除==
  - 可以监测到==数组下标和length属性==的变更
- 缺点：
  - ES6的Proxy语法对低版本浏览器不支持

## vue2响应式原理

vue2使用ES5中的Object.defineProperty()实现响应式数据

- 缺点：
  - ==无法监测到对象属性的动态添加和删除==
  - 无法监测到==数组下标和length属性==的变更
- 解决方案：
  - vue2提供了Vue.set()，用于动态添加对象属性
  - vue2提供了Vue.delete()，用于动态删除对象属性
  - ==重写了数组的方法，用于监测数组的变更==







# **Composition API**

- ref函数只能监听简单类型的变化，不能监听复制类型变化

- - ref和reactive一样，都是用来实现响应式数据的方法
  - ref的本质：底层还是reactive，系统会自动根据我们给ref传入的值将它转换为ref(xx)--->reactive({**value**：xx})
  - 在vue中使用ref的值不用通过value获取
  - 在js中使用ref的值**必须**通过value获取

- reactive：vue3中提供的实现响应式数据的方法

- - reactive参数必须是对象（json、arr）
  - reactive的本质：将传入的数据包装成**Proxy**对象
  - 若给reactive传递了其他对象，默认情况下修改对象界面不会自动更新；若想更新，可以通过重新赋值

- ref和reactive的区别：

- - 如果在template中使用ref类型的数据，那么vue会自动帮我们添加.value

  - - vue在解析数据前，会自动判断这个数据是否是ref类型的，若是则自动添加.value，若不是则不会自动添加
    - 若当前数据有__v_ref属性并且取值为true，那么就是ref类型数据

  - 若过在template中使用reactive类型的数据，那么vue不会自动帮我们添加.value

## ==所有composition API的导入都是函数的调用==

## composition API & options API

options API（==选项==api）：

- 优点：容易学习和使用，代码有明确的书写位置
- 缺点：相似逻辑不容易复用，在大项目中尤为明显
- 可以通过==mixins==提取相同逻辑，但是容易发生命名冲突且来源不清晰

composition API（==组合==api）：

- composition api是根据==逻辑功能来组织代码==的，一个功能所有api放到一起
- 即便项目很大，功能很多，也可以快速定位到该功能所有的api
- composition api提供了代码==可读性和可维护==性
- vue3推荐使用composition api，也保留了options api



## setup

==setup(props，ctx){}==函数是一个新的组件选项，是Composition API的==起点==。

- 从生命周期钩子的角度来看，setup会在beforeCreate==之前==执行
- setup函数中的this是undefined
  - 通过props参数来获取组件实例的属性
  - 通过ctx参数来获取组件实例的方法和其它内容
- ==setup只能是同步的，不能异步==
- 在setup中定义的变量和方法，要想被外界使用，必须==return==



## 响应式系统API

### reactive 

reactive()接收一个普通对象（json、arr），返回该对象的响应式代理Proxy对象。

- reactive数据在setup==内部==可以修改，若定义方法返回到外部则不可修改
  - 可以通过toRefs()强制将reactive数据改为ref数据

### ref

ref()接收一个==简单类型的值==，返回一个==响应式的ref对象==（有==唯一==的==value属性==）。

- 在==template==中使用ref类型的数据，vue会自动帮我们添加.value（==自动解套==），不需要额外的.value
- 在==js==中使用ref类型的数据，必须通过ref对象的==value==属性获取

ref数据在setup==内部或外部==都可以修改

### computed

computed()是一个函数，有2种用法：

- 传入一个==get函数==，返回一个==只读==的计算属性
- 传入一个==对象==（包括get、set），可以创建一个可修改的计算属性

### watch

watch()是一个函数，要接收3个参数：

- 参数1：监视的数据源，可以是一个==ref==或==函数==（返回reactive数据）
- 参数2：==回调函数==
- 参数3：额外的配置，是==一个对象=={deep：true，immediate：true}

### readonly

readonly()接收一个对象（响应式或普通）或ref，返回一个原始对象的==只读==代理

- 只读代理是==深层的==，对象内部任何嵌套的属性都是只读的
- 作用：可以防止对象被修改



## 依赖注入

vue3提供了==provide()和inject()==提供依赖注入，用于实现组件之间的通信。类似于vue2中的provide和inject

- vue3提供的provide()和inject()可以用于跨多级组件进行通信



## 生命周期钩子函数

vue3提供的生命周期钩子只能在setup中使用。

- beforeCreate--->setup
- created--->setup
- beforeMount--->onBeforeMount
- mounted--->onMounted
- beforeUpdate--->onBeforeUpdate
- updated--->onUpdated
- beforeDestroy--->onBeforeUnmount
- destroyed--->onUnmounted
- errorCaptured--->onErrorCaptured







## 响应式系统工具集

### toRefs

toRefs()用于把一个响应式对象转换成普通对象，该普通对象的每个属性都是一个ref

- reactive的响应式功能是赋予给对象的，但是如果给==对象结构或展开时，会让数据丢失响应式==
- 使用toRefs()可以保证该对象结构或展开的每个属性都是响应式的







## 高级响应式系统PAI

- ==ref()==：==复制==，修改响应式数据不会影响以前的数据；数据发生改变时，界面==会自动==更新
- toRef()：==引用==，修改响应式数据会影响以前的数据；数据发生改变时，界面==不会自动==更新
- toRefs()：把一个对象的==多个属性==修改为响应式数据
- customRef()：返回一个ref对象，可以显式的控制依赖追踪和触发响应
- ==reactive()==



## 递归监听

- 通过==ref()、reactive()==创建的数据默认是递归监听的。如果数据量非常大时，非常耗性能。

- ref、reactive类型数据的特点：==响应式==，即每次修改都会被追踪，都会更新ui界面。但这样非常的耗性能，若有一些操作不需要追踪，不需要更新ui界面，这个时候就可以通过toRaw()拿到它的原始数据，对原始数据进行修改，这样就不会更新ui界面

- toRaw()：从响应式数据中获取原始数据

  - 若想拿到ref类型的原始数据（创建proxy时传入的数据），就必须明确的告诉toRaw()，要获取的是.value值

- markRaw()：不会追踪

  

## 非递归监听

- shallowRef()、shallowReactive()，当监听数据量比较大时使用

  - shallowRef的本质：shallowRef(10)--->shallowReactive({value:10})
  - 如果是通过shallowRef创建的数据，vue监听的是==.value==的变化，因为底层本质上value才是第一层。

- - vue3中提供了triggerRef方法（可以主动触发界面更新），没有提供triggerReactive方法（如果是reactive类型数据，无法主动触发界面更新）

