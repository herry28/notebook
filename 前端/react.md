# 创建基本的webpack4.x项目

- 运行npm init -y：快速初始化项目
- 在项目根目录创建src源代码目录和dist产品目录
- 在src目录下创建index.html
- 安装webpack
- webpack4.x提供了约定大于配置的概念，目的是为了减少配置文件的体积
  - 打包的入口：src--->index.js
  - 打包的输出文件：dist--->main.js

## webpack中启动jsx语法

webpack默认只能打包处理.js的文件，其他无法打包，需要配置babel。

- 安装babel插件：
  - npm i babel-core babel-loader babel-plugin-transform-runtime -D
  - npm i babel-preset-env babel-preset-stage-0 -D
- 安装能够识别jsx语法的包：
  - npm i babel-preset-react -D
- 根目录下添加.babelrc配置文件
- webpack.config.js中添加babel-loader配置项







# react概述

react：==一切基于js==并且实现了组件化开发思想（通过==js==实现）。

- 工程化：一条龙服务

- 模块化：从==代码==角度进行分析，将可复用的代码抽离为单个模块，便于项目的维护和开发
- 组件化：从==ui（代码+资源）==角度进行分析，将可复用的ui元素抽离为单个组件
  - 组件化流程：
    - 拆分组件
    - 实现静态组件
    - 实现动态组件：
      - 动态显示初始化数据（数据类型、名称、保存到哪个组件）
      - 交互（绑定事件）

## react特点：

- ==声明式==
- 组件化
- 单向数据流

## uuid库

唯一识别码



# react脚手架

- 全局安装create-react-app脚手架：npm install -g create-react-app
  - 不想全局安装create-react-app脚手架：==npx== create-react-app 项目名
- 创建项目：create-react-app 项目名
  - react：react的顶级库
  - react-dom：因为react有很多的运行环境，如app端的reactive-native，web端的react-dom
  - react-scripts：包含运行和打包react应用程序的所有脚本及配置









# jsx（==js+xml==）

jsx是react定义的一种类似于xml的==js扩展语法==。可以把js和html混合书写的一种语法。

jsx作用：简化创建虚拟dom对象，jsx语法产生的是一个==js对象==。

jsx语法本质：==React.createElement()==的语法糖（并不是直接把jsx渲染到页面上，而是在运行时通过==babel==把jsx语法转换成React.createElement形式）

jsx语法规则：

- 标签名、属性名==任意==
- ==js表达式==要写到=={ }==中
  - js表达式：一个表达式会产生一个值，如arr.map()
  - js语句：可理解成一个行为

- jsx中的注释，推荐使用{/**/}
- 标签必须闭合

- jsx中的元素添加class类名，需要使用==className==代替class

- 在jsx创建dom时，所有的节点必须有==唯一的根元素==进行包裹









# 虚拟dom

dom：是浏览器中的概念，用js来表示页面上的元素，并提供了操作dom的api

虚拟dom：是框架中的概念，用js对象来模拟页面上的dom和dom嵌套

- 目的：提高dom元素的高效更新
- 本质：==js中的一般Object对象==
- 虚拟dom和真实dom的区别：虚拟dom比较轻，因为虚拟dom上无需过多的API。真实dom比较重。



## 创建虚拟dom

### 纯js（一般不用）

==React.createElement()==

- 参数1：创建的元素类型，字符串，表元素名称
- 参数2：是一个对象或null，表当前dom元素的属性
- 参数3：子节点
  - 如：let vDOM=React.createElement('h3'，{ }，哈哈)

### jsx

如：let vDOM=<h3 id={title}>{ } </h3>



## 将虚拟dom渲染到页面上

==ReactDOM.render()==

- 参数1：纯js或jsx创建的虚拟dom对象
- 参数2：指定页面上的dom元素（容器）



## 虚拟dom中的key

key是虚拟dom树的标识。当==数据发生变化时，react会生成新的虚拟dom==，随后react会将旧的虚拟dom与新的虚拟dom进行==diff算法==比较。

虚拟dom树对比的最小的力度是==标签元素==。

- 旧的虚拟dom中==找到了==相同的key
  - 若==虚拟dom没变==，==直接使用原来的真实dom==，不去刷新页面的真实dom
  - 若虚拟dom变了，先更新虚拟dom，随后渲染真实dom到页面
- 旧的虚拟dom中==未找到==相同的key
  - 创建新的虚拟dom，随后渲染真实dom到页面

key为index可能产生的问题：

- 添加、删除、排序时，会产生没有必要的真实dom更新   ===> 页面没问题，但效率低
- 若页面还有输入类dom，会产生错误的真实dom更新   ===>页面有问题







# diff算法

tree diff：新旧2颗dom树逐层对比的过程，就是tree diff。

component diff：在进行tree diff时，每一层中，组件级别的对比就是component diff。

element diff：在进行组件对比时，如果2个组件类型相同，则需要进行元素级别的对比，就是element diff。







# 组件

组件的本质：==函数==

高阶函数：参数是函数==或者==返回值是函数的函数

纯函数：只要是同样的输入（实参），必定得到同样的输出（返回值）。必须遵循以下约束：

- 不得改写参数数据
- 不会产生任何副作用，如网络请求，输入输出设备
- 不能调用Date.now()、Math.random()等不纯的方法

高阶组件：参数是组件==并且==返回值是新组件的函数。高阶组件是一个特别的高阶函数，是react中==复用组件逻辑==的一种高级技巧。

- Form.create()(组件)
- withRouter(组件)
- connect()(组件)



## 创建组件

==组件名首字母必须大写==。在react中，事件回调函数需传参时，外面需要再==包裹一个箭头函数==。

怎么传递props数据？

- 属性={属性值}
- 组件中可以通过==展开运算符(...)==简化传递props数据的过程：=={ ...obj }==（react+babel底层做了处理）

### 通过==构造函数==创建组件（==无状态==组件）（hook）

尽量用函数组件。函数组件中的==this是undefined==。

通过function关键字创建的组件中，若想使用外界传递props数据，需要在构造函数的形参中使用==props==来接收；必须return 一个合法的jsx创建的虚拟dom

- 当渲染标签时首字母是小写的：尝试着寻找html与之对应的标签进行渲染，如果未找到则报错
- 当渲染标签时首字母是大写的：尝试着寻找组件是否被定义--->定义了，调用组件标签同名的构造函数从而获取返回值

### 通过==class==创建组件（==有状态==组件）

一般很少用。类组件中的==构造器和render==方法里的==this是类组件的实例对象==；其他实例方法中的this是undefined。

使用class创建组件，必须继承==React.Component==类，在组件内部必须有==render(){}==函数，在render函数中必须==return 合法的jsx虚拟dom==

- render的调用次数：1+n次

通过class关键字创建的组件中，若想使用外界传递过来的props数据，不需接收，直接通过==this.props.xxx==访问即可

- 创建一个==组件实例==--->通过实例调用了render()，从而获取了返回值



## 有状态组件 & 无状态组件

受控组件 & 非受控组件：是否==自动维护==到state中。

无论是哪种方式创建的组件，它们的props都是==只读==的。

有状态组件和无状态组件的本质区别：==有无state属性和生命周期函数==

使用class关键字创建的组件，==有自己的私有数据（this.state）和生命周期函数==

- 私有数据在构造器中：
  - this.state={}就相当于vue中的data(){return {}}
  - this.state中的数据都是可读可写的

使用function关键字创建的组件，==只有props==，==没有==自己的私有数据和生命周期函数

==props & state==：

- props中的数据都是外界传递过来的，只读的
  - 通过==静态属性==设置默认值
  - ==props.children==：组件嵌套
  - prop-types包：props类型验证
- state中的数据都是组件私有的，可读可写的
  - 在react中，若想修改state中的数据，推荐使用==this.setState({ })==
  - ==state在哪个组件，更新state的方法就在哪个组件==
  - state中的数据==不能直接修改、不能直接更新==
    - 禁止 this.state.xxx=xxx
    - 禁止 let {xxx} from this.state，
      - 获取原状态：let xxx=[...this.state.xxx] （获取state中的==对象类型数据==） 

```
function 组件名(props){
	return 合法的jsx语法
}


class 组件名 extends React.Component{
	constructor(){
		super()
		this.state={}//this.state={}就相当于vue中的data(){return {}}
	}
	render(){
		return 合法的jsx语法
	}
}
```



## 组件实例对象的3大属性（state、props、refs）

state（==状态==）是类组件==实例对象==最重要的属性。

state简写方式：

- 简写初始化状态：直接在类组件中写state={}
- 简写实例方法：fn=()=>{}

refs：

- this.refs.xxx
- 回调ref
- ==React.createRef()==：返回一个保存ref的Object容器，且该容器专人专用（==只能保存一个ref==）



## 组件间通信

- 通过props传递

  - 通过props可以传递==一般==数据和==函数==数据，只能一层层传递，兄弟组件通信必须借助父组件
    - 一般数据--->父组件向子组件---->子组件读取数据
    - 函数数据--->子组件向父组件---->子组件调用函数

- 使用消息发布订阅机制（pubsub-js库）

  - npm install pubsub-js 
  - import PubSub from 'pubsub-js'
  - PubSub.subscribe('xxx'，(data)=>{})：订阅（相当于绑定事件监听）
  - PubSub.publish('xxx'，data)：发布（相当于触发事件）

- redux：集中式管理多个组件共享的状态

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  





# 样式

## style

## class

有时候需要根据不同条件添加不同样式，推荐使用classnames包

## styled-components

styled-components是针对react写的一套css-in-js包，简单来讲就是在js中写css。







# react事件绑定机制

点击事件：onClick={}，只接收一个函数作为参数

文本框改变事件：onChange={}

- 当为文本框绑定value值后，要么同时提供一个readOnly属性，要么提供onChange处理函数
- 手动实现表单的双向绑定：
  - 手动监听onChange事件
  - 在onChange事件中拿到最新的value
    - 通过事件参数e：e.target.value
    - ref：this.ref.xxx
  - 调用this.setState({})手动把最新的value同步到state中



# 表单



# react组件生命周期函数

## 组件创建(初始化)

此阶段的触发条件：ReactDOM.render()

- componentWillMount（废弃）

- ==render==：提供虚拟dom，可能会调用1+n次

- ==componentDidMount==：发ajax，只执行1次

## 组件运行(更新)

触发条件：this.setState({})。根据props属性或state状态的改变，有选择性的执行0-n次

- componentWillReceiveProps（废弃）

- shouldComponentUpdate

- componentWillUpdate（废弃）

- render

- componentDidUpdate

## 组件销毁阶段

触发条件：ReactDOM.unmountComponentAtNode()

- componentWillUnmount

## 新增的钩子

getDerivedStateFromProps：根据props生成state，相当于之前的componentWillMount和componentWillUpdate

getSnapshotBeforeUpdate：介于componentDidUpdate和render之间







# react-router-dom

## 路由

一个路由就是==一个映射关系==，一对key-value组合

- 后端路由：

  - key：==请求方法+请求的URI（虚拟路径）==
  - value：函数

- 前端路由：当请求路由路径时，浏览器并没有发送http请求，但界面会更新相对应的路由组件

  - key：==请求的URI（虚拟路径）==
  - value：组件




## router将所有组件分成2类

- 一般组件，一般保存在components文件夹下
- ==路由组件==，一般保存在pages文件夹下
  - 路由组件的==实例对象==上props多出来的属性：
    - history
      - push()
      - replace()
      - goBack()
      - goForward()
    - location
      - pathname
    - match
      - params



## react路由的底层实现（基于==history==库）

history是管理浏览器会话历史的工具库，包装的是原生BOM中window.history和window.location.hash

history API:

- History.createBrowserHistory()：得到封装window.history的管理对象
- History.createHashHistory()：得到封装window.location.hash的管理对象
- history.push()：添加一个新的历史记录
- history.replace()：用一个新的历史记录替换当前的记录
- history.goBack()：回退
- history.goForword()：前进
- history.listen(location=>{})：监视历史记录的变化



## react-router-dom的组件

路由链接--->url变化--->路由器监测--->路由匹配--->展示对应的路由组件

- Link
- NavLink：编写路由链接，当点击时默认会给路由链接高亮效果
  - to属性
  - activeClassName属性：可修改路由链接高亮

- BrowserRouter（路由器）
- HashRouter：hash路由(==#==)，h5提出的，表示#后面所有的跟服务器无关

- Route：注册路由
  - path：路由路径，默认为模糊匹配（是否完整的包含在地址栏的url），==不要把/放在第一位==
  - component：路由组件
  - ==/:参数==：动态路由
  - exact：精准匹配，一般不用
- Redirect：重定向
  - to
- ==Switch==：当匹配到了路由就不会往后匹配了



## 嵌套路由

## 动态路由

## 路由跳转

- 路由链接
- 编程式路由导航







# redux

redux是一个独立、专门用于做状态管理的js库（不是react插件库）

- 作用：==集中式管理==react应用中多个组件==共享==的状态



## redux核心API

- createStore()：创建包含指定reducer的store对象

- store对象：redux库最核心的管理对象，内部维护着==state、reducer==

  - getState()：得到state
  - dispatch(action)：分发action，触发reducer调用，产生新的state
  - subscribe(listener)：注册监听，当产生了新state时，自动调用

  

## redux3个核心概念

- action：标识要执行行为的对象
  - type：标识属性，值为字符串，唯一、必要属性
  - xxx：数据属性，值类型任意，可选属性
- reducer：根据旧state和action，产生新state的==纯函数==
  - 返回一个新的状态
  - 不要修改原来的状态
- store：将state、reducer、action联系在一起的对象







# react-redux

react-redux是一个react插件库，专门用来简化react应用中使用redux



## react-redux将所有组件分成2类

- UI组件
  - 只负责ui的呈现，不带任何业务逻辑
  - 通过props接收数据（一般数据和函数）
  - 不使用任何redux的API
  - 一般保存在components文件夹下
- 容器组件
  - 只负责管理数据和业务逻辑，不负责ui的呈现
  - 使用redux的API
  - 一般保存在containers文件夹下



## react-redux的API

- Provider：让所有组件都可以得到state数据
- connect()：用于包装UI组件，生成容器组件
- mapStateToprops()：将外部的数据（state对象）转为UI组件的标签属性
- mapDispatchToProps()：将分发action的函数转为UI组件的标签属性





# hooks

hooks的本质：==js函数==，但使用它们会有2个额外的规则：

- 只能在==函数最外层==调用hook。不要在循环、条件判断或子函数中调用
- 只能在react的函数组件中调用hook。不要在其他js函数中调用

hooks最主要的作用：

- 让组件内部的状态变得更易于编写和维护
- 解决组件逻辑的复用





# antd组件库



