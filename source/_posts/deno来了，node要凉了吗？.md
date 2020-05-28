---
title: Deno来了，Node要凉了吗？
date: 2020-05-27 11:33:56
tags:
	- node
	- deco
categories: 资讯
copyright: true
top: 1
typora-root-url: ../
typora-copy-images-to: ../images
---

## 背景故事

最近Deno讨论比较热门，并且有说 “Deno很可能是下一个前端的大事件”，所以关注下。

> Node.js 和 Deno 的起源作者都是**Ryan Dahl**
>
> 他从2009年开始从事Node项目，但是几年后却退出了该项目。在2018年的时候，他发表了**我对Node.js感到遗憾的10件事**，另外他宣布了创建全新的Deno项目 。
>
> 作者GitHub https://github.com/ry



Ryan DahI 提到的**Node十个设计错误**，很多都是基层方面的设计错误，要深入理解需要有专业功底。本人作为小白，只是整理罗列。

#### **后悔 没有坚持使用Promise** 的结果是导致Node里面充满了async / await和promise的不同async API设计，直到现时都极难整合。

![0_node](/images/0_node-0567328.png)

![1_node](/images/1_node-0567328.png)



#### 后悔 没有从GYP加固系统转到GN

#### 后悔 继续使用GYP，没有提供FFI

#### 后悔 在任何地方也可以require（“ somemodule”）

#### 后悔 package.json提供了错误的“ module”观念

#### 后悔 没有注重安全性（Security）

#### 后悔 设计了软件界黑洞node_modules

​	有的时候 npm install 要等很久，然后发现应用下载了几百mb的node_module。



> 附上演讲视频地址：https://www.bilibili.com/video/BV1fp4y1Q7bi



## Deno

**Deno**是使用JavaScript和TypeScript编写应用程序的新平台。两种平台具有相同的理念-事件驱动架构和异步非阻塞工具来构建Web服务器和服务。

#### Node 和 Deno 有何不同？

这两个平台具有相同的目的，但是使用不同的机制。Deno使用ES模块作为默认模块系统，而Node.js使用CommonJS。外部依赖项是使用URL加载的，类似于浏览器。也没有包管理器和集中式注册表，可以在Internet上的任何位置托管模块。与Node.js相反，Deno在沙箱中执行代码，这意味着运行时无法访问网络，文件系统和环境。需要明确授予访问权限，这意味着更好的安全性。Deno开箱即用地支持TypeScript，这意味着我们不需要手动安装和配置工具来编写TypeScript代码。另一个区别是Deno提供了一组内置工具，例如测试运行器，代码格式化程序和捆绑程序。



#### Deno不需要npm包管理

```javascript
# Deno
import { serve } from "https://deno.land/std@0.53.0/http/server.ts";

# Node 
const server requrie('server')
```

Deno通过URL导入代码，可以在互联网上的任何地方托管模块。无需集中注册表即可分发Deno软件包。也不需要package.json文件和依赖项列表，因为所有模块都是在应用程序运行时下载，编译和缓存的。



#### Deno 真的会取代node?

> **Krzysztof Piechowicz：Deno**的目标不是取代Node.js，而是提供替代方案。其中一些差异颇具争议，很难预测它们是否将以正确的方式格式化。我建议所有Node.js程序员都注意这个项目。我不确定该项目是否会成功，但这是观察Node.js如何以不同方式实现的绝佳机会。



> 演讲视频地址：https://www.bilibili.com/video/BV1hi4y1s7e7



## 尝试一下

附录资源

> 官网：https://deno.land
>
> 源码：https://github.com/denoland/deno



```bash
# 安装
curl -fsSL https://deno.land/x/install/install.sh | sh

# 运行Demo
deno run https://deno.land/std/examples/welcome.ts
```

例子

```javascript
import { serve } from "https://deno.land/std@0.53.0/http/server.ts";
const s = serve({ port: 8000 });
console.log("http://localhost:8000/");
for await (const req of s) {
  req.respond({ body: "Hello World\n" });
}
```



## 总结

Deno 是否会带来变革，我们拭目以待！！