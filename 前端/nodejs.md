# Nodejs概述

nodejs是基于chrome的V8引擎构建的==js运行环境==。

js中有一个特殊的对象，成为全局对象，它及所有属性都可以在程序的任何地方访问，即全局变量。

- 浏览器中，window是全局对象
- node中，`global`是全局对象，所有全局变量（除了global）都是global的属性
  - node中声明的变量`不会`被挂载到global上
  - 可以向global添加属性，使其在任何地方都可用
  - nodejs执行js文件时，this指向`当前模块`



## process：

- process.argv：
  - 可得到解释器的路径、当前js文件路径，执行js文件时的参数
  - 一般用于开发命令行项目
- process.arch：打印系统位数







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



## url（url解析）



## querystring（查询参数解析）





## fs（文件）





## http（网络）





