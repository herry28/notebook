# Nodejs概述

nodejs是基于chrome的V8引擎构建的==js运行环境==。是事件驱动IO服务端。

js中有一个特殊的对象，成为全局对象，它及所有属性都可以在程序的任何地方访问，即全局变量。

- 浏览器中，window是全局对象
- node中，`global`是全局对象，所有全局变量（除了global）都是global的属性
  - node中声明的变量`不会`被挂载到global上
  - 可以向global添加属性，使其在任何地方都可用
  - nodejs执行js文件时，this指向`当前模块`

配置淘宝服务器：npm config set registry https://registry.npm.taobao.org

查看registry配置：npm config get registry



## REPL(交互式解释器)

REPL(Read Eval Print Loop:交互式解释器) 表示一个电脑的环境，类似 Window 系统的终端或 Unix/Linux shell，我们可以在终端中输入命令，并接收系统的响应。

Node 自带了交互式解释器，可以执行以下任务：

- **读取** ：读取用户输入，解析输入的 js数据结构并存储在内存中。
- **执行** ： 执行输入的数据结构
- **打印** ：输出结果
- **循环** ： 循环操作以上步骤直到用户两次按下 **ctrl-c** 按钮退出。



## process：

- process.argv：
  - 可得到解释器的路径、当前js文件路径，执行js文件时的参数
  - 一般用于开发命令行项目
- process.arch：打印系统位数

## 页面中资源路径

相对路径：

- file协议中，相对于当前路径

- http协议中，相对于当前网页的url地址





# Buffer

js中没有二进制数据类型。但在处理文件流时，必须使用二进制数据。因此在nodejs中，定义了一个Buffer类，该类用来创建一个专门存放二进制数据的缓存区。buffer数据需要toString()才能看到真正的数据

创建buffer对象：

- Buffer.from()
- Buffer.alloc()





# 模块化

一个.js文件就是一个模块，模块的作用域是`私有的`。

## CommonJS规范

导出：

- exports.xxx = xxx
- module.exports = { }

导入：require('url')

- 一般用`常量`来接收导入的模块，且常量名与模块名一致
- 模块文件的扩展名可省略



## 模块中this指向

`exports本质上是module.exports的引用`。在nodejs的模块中，this代表当前模块，即this === exports

- 在node的交互模式下，没有exports对象，此时this === window







# nodejs内置模块

## path（路径）

__dirname：本模块所在的路径（不包含文件名）

__filename：本模块所在的路径（包含文件名）

path.join(__dirname，...)：拼接路径，会把所有的参数自动按照系统默认的路径分隔符进行拼接



## url（url解析）



## querystring（查询参数解析）





## fs（文件）

fs.readFile(url，'utf-8'，(err，data) => { })：异步读取

fs.writeFile()：异步写入



## http（网络）

ip：区别`计算机`

端口号：区别同一电脑上的网络`进程`（正在运行的程序就是进程）



## crypto









# 中间件

express中的中间件是一个可以访问`请求对象、响应对象、和调用next()`的`函数`。如果当前的中间件函数没有结束请求-响应周期，则必须调用`next()`将`控制权传递给下一个中间件函数`，否则，该请求将被挂起。

- 执行任何代码
- 修改req请求对象、res响应对象
- 结束请求响应周期
- 调用下一个中间件

## 中间件分类

next()：往后匹配下一个中间件

next('route')：将控制权传递给下一条路由，仅在app.method()或router.method()函数加载的中间件中有效。

`next(err)`：如果传递任意内容（除'route'外），express都会将当前请求视为错误，并`跳过所有剩余的无错误处理路由和中间件函数`

1.  应用程序级别中间件：处理404的中间件一般放最后

   - app.use()

   - app.get()

2. 路由器级别中间件：`路由实例`相当于一个`mini express实例`

   - router.use()
   - router.get()

3. 错误处理中间件：始终带`4个`参数，在所有的中间件之后挂载

   - app.use((err，req，res，next)=>{ })
   - next(err)中的err将会传给给错误处理中间件的err。

4. 内置中间件

   - `express.json()`：解析Content-Type为application/json格式的请求体
   - `express.urlencoded()`：解析Content-Type为application/x-www-form-urlencoded格式的请求体
   - `express.static()`：托管静态资源文件

5. 第三方中间件

   - morgan：日志输出
   - cors：跨域





# 基于jwt的身份认证

jwt是一个很长的`字符串`，中间用`.`分隔成3部分：Header.Payload.Signature

- Header（头部）：json对象，描述jwt的元数据，使用`BASE64URL`算法转成字符串
- Payload（负载）：json对象，用来存放实际需要传递的数据，使用`BASE64URL`算法转成字符串
- Signature（签名）：是对前两部分的签名，防止数据篡改。

jwt的特点：

- jwt默认是不加密的，需要生成原始token后，使用密钥再加密一次。
- jwt不仅可以用于认证，也可以用于交换信息
- jwt应该使用https协议传输









# 模板引擎(`art-template`)

模板引擎：根据特定的规则，进行`字符串的解析替换`





# mongodb数据库





