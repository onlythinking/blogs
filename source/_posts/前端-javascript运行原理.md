---
title: 前端-JavaScript运行原理
date: 2021-10-22 11:19:11
tags: 
   - 前端
   - javascript
   - v8
   - 事件循环
categories: 前端
copyright:
top: 1
typora-root-url: ../
typora-copy-images-to: ../images
---

## 简介

理解JavaScript运行原理，我们需要理解以下两方面内容。

- JavaScript引擎。
- JavaScript运行时环境。



## JavaScript引擎

### 什么是JavaScript引擎

JavaScript引擎是一个计算机程序，它的主要作用是JavaScript运行时将源码编译为机器码。

每个主流Web浏览器都有自己的JavaScript引擎，它通常由web浏览器供应商开发。

![ivrpowers-web-browser.007](/images/ivrpowers-web-browser.007.jpeg)

- Google Chrome V8。
- Mozilla Firefox Spider Monkey。
- Safari Javascript Core Webkit。
- Edge (Internet Explorer)

以前的JavaScript引擎主要在web浏览器使用，不过随着nodejs的出现就打破了这种局限。



### V8引擎

 V8包含了解析器（parser），解释器（Ignition），优化编译器（TurboFan ）。

**解析器（parser）**：用于生成抽象语法树。







![JavaScript代码的句法结构的树形表示形式](/images/js_tree.jpeg)



**解释器（Ignition）**：将源码转换为字节码。

![image-20211022160435142](/images/image-20211022160435142.png)

**优化编译器（TurboFan ）**：进行一些优化编译优化处理，比如内联缓存。

 下面是V8引擎的大体工作流程。

1. 首先**解析器**先生成一个抽象语法树。
2. 然后**解释器**根据语法树生成V8格式的字节码。
3. **优化编译器**再将字节码编译成机器码。

![源码转化为机器码流程](/images/v8.jpeg)

## 运行时环境

浏览器运行环境中，浏览器提供了Web API，如：HTTP请求，计时器，事件等。

服务器运行环境中，nodejs提供了API。

下面是JavaScript在浏览器中运行时的架构，它包含一个内存堆、一个内存栈、一个事件循环、一个回调队列。

![js_run](/images/js_run.png)

- 内存栈（`stack`），一个连续的内存区域，为每个执行的函数分配本地上下文。
- 内存堆（ `heap`）， 一个更大的内存区域，存储动态分配的所有内容。
- 调用栈（`call stack`）， 一种数据结构，记录了我们在程序中的位置。
- 回调队列（`callback queue`），存储异步任务的回调函数。
- 事件循环（`event loop`），持续检查调用栈是否为空，如果为空，将回调队列中头部回调函数移动到调用栈执行。

### 运行时的调用栈

下面代码展示了JavaScript执行的调用栈变化。

![js_33](/images/js_33.gif)

```javascript
function add(x, y) {
    return x + y;
}

function print(x, y) {
    console.log('x+y=',add(x, y))
}

print(1, 3)
```



### 异步任务

JavaScript先执行了`print`函数，然后调用Web API `setTimeout()`，Web API存储了`setTimeout()` 的回调函数，3秒后将回调函数添加到回调队列，事件循环发现调用栈为空，于是将队列里的回调函数移至调用栈执行。

![js_22](/images/js_22.gif)

```javascript
function add(x, y) {
    return x + y;
}

function print(x, y) {
    setTimeout(function (){
        console.log('x+y=',add(x, y))
    }, 3000)
}

print(1, 3)

```



## 小结

JavaScript运行主要依靠JavaScript引擎和运行环境，**引擎**将js源码翻译成计算机所理解的机器码，**运行环境**提供了一些与计算机底层通讯的API和运行实现。
