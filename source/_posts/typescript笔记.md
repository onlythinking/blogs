---
title: Typescript笔记
date: 2021-06-29 10:41:49
tags: 
	- typescript
categories: 前端
copyright:
typora-root-url: ../
typora-copy-images-to: ../images
---

## 为什么使用Typescript？

微软推出TypeScript主要是为实现两个目标：

- 为Javascript提供可选的类型系统；
- 兼容当前及未来的JavaScript的特性。

静态类型带来的好处：

- 有利于代码重构，它在编译器编译的时候就能捕获错误。
- 类型明确有利于阅读。

## JavaScript常见语法

TypeScript是JavaScript的“超集”，typescript将javascript语法标准化。

![typescript](/images/typescript.jpeg)

1. == 与 ===

   使用==进行比较时，会进行隐式转换。

   ```javascript
   2 == 2 true
   2 == '2' true
   2 === '2' false
   ```

2. 引用

   除字面量外，JavaScript中的任何对象（包括函数、数组、正则表达式等）都是一个引用。

3. null和undefined

   变量没有初始化：undefined。变量不可用为null。

   ```javascript
   undefined == undefined true
   undefined == null true
   // 检查变量是否初始化
   if( typeof val !== 'undefined'){}
   ```

4. this

   this关键字指向调用上下文

   ```javascript
   function foo(){console.log(this)} /** this --> window **/
   var obj = {foo:function(){console.log(this)}} 
   obj.foo() /** this --> obj */ 
   ```

5. 闭包

   内部函数访问外部变量，此时外部函数的变量被内部函数绑定，称为闭包。

   ```javascript
   function outer(){
     var outerVal = '1'
     function inner(){
       console.log(outerVal)
     }
     outer()
   }
   /** inner 绑定了 outerVal */
   ```

6. 数字

   JavaScript中数值是双精度的64位的number

   ```javascript
   console.log(.1 + .2) // 0.30000000000000004
   ```

   内置整数类型限制 Number.MAX_SAFE_INTEGER-Number.MIN_SAFE_INTEGER

   金融计算中一般使用 big.js

   NaN，计算出的结果不是合法数值时返回NaN，检测NaN，Number.isNaN。

7. thuthy

   ![](/images/thuthy.jpeg)

   ！！双感叹号，第一个！是将值转为布尔值，第二个逻辑反转。

   

## ES6语法

1. class

   ```javascript
   /*ES6*/
   class Point {
     x: number
     y: number
     constructor(x: number, y: number) {
       this.x = x
       this.y = y
     }
     add(point: Point) {
       return new Point(this.x + point.x, this.y + point.y)
     }
   }
   
   /*编译后ES5*/
   var point = function(){
     function Point(x, y){
       this.x = x;
       this.y = y;
     }
     Point.prototype.add = function(point){
      return  new Point(this.x + point.x, this.y + point.y)
     }
     return Point;
   }()
   ```

   

   继承

   ```javascript
   /*ES6*/
   class Point3D extends Point {
     z: number
     constructor(x: number, y: number, z: number) {
       super(x, y)
       this.z = z
     }
     add(point: Point3D) {
       var point2D = super.add(point)
       return new Point3D(this.x + point2D.x, this.y + point2D.y, this.z + point.z)
     }
   }
   
   /*编译后ES5*/
   var point3D = function(_super){
     __extends(Point3D, _super)
     function Point3D(x, y, z){
       _super.call(this, x, y)
       this.z = z;
     }
     Point3D.prototype.add = function(point){
   	 var point2D = _super.prototype.add.call(this, point)
      return new Point3D(this.x + point2D.x, this.y + point2D.y, this.z + point.z)
     }
     return Point;
   }(Point)
   
   /**__extends typescript 编译 extends 时生成*/
   var __extends = (this && this.__extends) || (function () {
       var extendStatics = Object.setPrototypeOf ||
           ({ __proto__: [] } instanceof Array && function (d, b) { d.__proto__ = b; }) ||
           function (d, b) { for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p]; };
   /*
   d表示派生类，b表示基类
   */
     return function (d, b) {
           extendStatics(d, b); // 拷贝静态变量
           function __() { this.constructor = d; } // 保留派生构造器
           d.prototype = b === null ? Object.create(b) : (__.prototype = b.prototype, new __()); //d.prototype.__proto__ = b.prototype
       };
   })();
   ```

   

   区分 __ proto __ prototype

   javascript 中所有对象都有属性__ proto __

   ```javascript
   /** 查找属性顺序 
   obj.property
   obj.__proto__.property
   obj.__proto__.__proto__.property
   **/
   ```

   javasript 所有的函数都有属性 prototype，prototype有个指向函数本身的构造器constructor。

   ```javascript
   function Foo(){
     this.name = "xm"
   }
   var foo = new Foo();
   /**通过函数创建的对象，会将函数prototype传给对象__proto__*/
   console.log(foo.__proto__ === Foo.prototype)
   ```

2. 箭头函数

   ```javascript
   var inc = x => x + 1;
   ```

   箭头函数带来语法简洁和this丢失问题。

3. rest参数

   ...+参数名的形式表示最后一个参数，获取时转为数组。

4. let和const

   var 变量是函数作用域，let 变量块作用域

   ```javascript
   /**闭包中的var*/
   var funcs = []
   for(var i = 0;i < 3; i++){
     funcs.push(function(){
       console.log(i)
     })
   }
   for(var j = 0;j < 3; j++){
     funcs[j]();
   }
   /*输出 3*/
   ```

   const 声明常量值。

5. 解构

   支持对象和数组解构。

   ```javascript
   /*对象解构*/
   var person = {name:'x',age:12,gender:1}
   var {name, age} = person
   console.log(name, age) // x, 12
   var {x, y, ...remaining} = {x: 1, y: 2, z: 4, w: 5} // 看作删除了x,y
   console.log(x,y,remaining) // 1,2 {z:4,w:5}
   ```

6. 扩展运算符

   Function.prototype.apply

   ```javascript
   /*apply调用*/
   function foo(x,y){}
   var args = [0,1]
   foo.apply(null, args)
   // 新语法
   foo(...args)
   // 数组追加元素
   var list = [1, 2]
   list = [...list, 3] //  [1,2,3]
   list = [0,...list,4] // [0,1,2,3,4]
   ```

7. for...of

   ```javascript
   /*for...in*/
   for(var e in [7,8,9]){console.log(e) }// 返回索引
   for(var e of [7,8,9]){console.log(e) }// 返回元素
   ```

8. Promise

   使用Promise处理异步和回调函数。

   ```javascript
   var promise = new Promise((resolve, reject) => {resolve()//回调正确值
                                                  reject() // 异常值
                                                  })
   promise.then(res => {}) // 正常
   .catch(err => {}) // 异常
   ```

   并行流程控制，Promise.all([promise1,promise2]).then(res => [result1, result2])

9. generators

   ```javascript
   function* idGen(){
       let index = 0
       while(index < 3) yield /*暂停*/ index++;
   }
   var id = idGen();
   console.log(id.next());
   console.log(id.next());
   console.log(id.next());
   console.log(id.next());
   ```

10. async/await

   ```javascript
   async function foo(){
       var result = await exPromise() //等待promise执行返回
       console.log(result)
   }
   ```

## TS项目构成

1. 编译上下文

   tsconfig.json配置文件，配置选项：[tsconfig](https://www.typescriptlang.org/tsconfig)

2. 声明空间

   类型声明空间和变量声明空间。

   - **类 (class)**: *类型*, *值*.
   - **接口 (interface)**: *类型*.
   - **枚举 (enum)**: *类型*, *值*.
   - **类别名 (type)**: *类型*.
   - **函数 (function)**: *值*.
   - **变量 (let, const, var, parameters)**: *值*.

3. 模块

   默认情况下声明处于全局命名空间中。使用export变成文件模块。

   模块路径查找

   ```javascript
   /**相对路径**/
   import * as foo from './foo' // 同级目录
   import * as foo from '../foo' // 上级目录
   import * as foo from '../someFolder/foo // 相对目录
   
   /**导入路径不是相对路径时，动态查找**/
   import * as foo from 'foo'
   /*
   查找顺序
   ./node_modules/foo
   ../node_modules/foo
   ../../node_modules/foo
   */
   import * as foo from 'something/foo'
   /**
   ./node_modules/something/foo
   ../node_modules/something/foo
   ../../node_modules/something/foo
   */
   /* place */
   import * as foo from 'foo'
   /**查找顺序*/
   /**
   foo 是文件 foo.ts
   foo 是目录 foo/index.ts
   foo 是目录 foo/package.json 有 types
   foo 是目录 foo/package.json 有 main
   **/
   ```

4. 命名空间

   ```typescript
   //typescript 声明
   namespace Utility {
     export function log(msg) {
       console.log(msg);
     }
     export function error(msg) {
       console.log(msg);
     }
   }
   // usage
   Utility.log('Call me');
   Utility.error('maybe');
   ```

   ```javascript
   var Utility;
   (function (Utility) {
       function log(msg) {
           console.log(msg);
       }
       Utility.log = log;
       function error(msg) {
           console.log(msg);
       }
       Utility.error = error;
   })(Utility || (Utility = {}));
   // usage
   Utility.log('Call me');
   Utility.error('maybe');
   ```

5. 动态导入表达式

   ```typescript
   // 动态加载
   import(/* webpackChunkName: "momentjs" */ 'moment')
     .then(moment => {
       // 懒加载的模块拥有所有的类型，并且能够按期工作
       const time = moment().format();
       console.log('TypeScript >= 2.4.0 Dynamic Import Expression:');
       console.log(time);
     })
     .catch(err => {
       console.log('Failed to load moment', err);
     });
   ```

## 创建TS项目

1. 安装node环境，创建目录进入执行 npm init -y;
2. 安装ts npm install typescript --save-dev ;
3. 创建文件 node.d.ts npm install @types/node --save-dev;
4. 初始化tsconfig.json npx tsc --init --rootDir src --outDir lib --esModuleInterop --resolveJsonModule --lib es6 , dom --module commonjs;
5. 添加ts-node：npm install ts-node --save-dev 实现实时编译和运行;
6. 添加nodemon：npm install nodemon --save-dev，只要文件被改变，它就会调用ts-node。



## TS类型系统

1. 基本概念

   基本类型 number string boolean string[]

   接口 interface

   特殊类型 any、null、undefined和void

   ```typescript
   // 接口
   interface Name{
       first: string,
       last: string
   };
   let n : Name;
   n = {
       first: 'Li',
       last:'p'
   }
   // 泛型
   function contact<T>(items : T[]): T[]{
       //......
       return items;
   }
   // 联合类型
   function join(items : string[] | string){
       //......
   }
   // 交叉类型
   function extend<T, U>(first: T, last : U) :T & U{
       const result = <T & U>{}
       return result
   }
   // 元祖类型
   let nameNum : [string, number]
   //类型别名
   type mind = string | Number
   let m : mind
   ```

2. @types

   仓库 [@types](https://github.com/DefinitelyTyped/DefinitelyTyped) 通过配置tsconfig.json的compilerOptions.types控制全局。

3. 环境声明

   使用关键字declare进行声明，一般放在.d.ts文件。

4. 枚举

   数字枚举和字符串枚举。

5. lib.d.ts

   安装TypeScript时，会顺带安装一个lib.d.ts声明文件。这个文件包含JavaScript运行时及DOM（Document Object Model，文档对象模型）中存在的各种常见的JavaScript环境声明。

6. 函数

   声明函数 **type** inc = (num : number) **=>** number

7. 可调用可实例化

  ```typescript
  // 可调用
  interface ReturnStr{
    () : string;
  }
  declare const foo : ReturnStr
  const str = foo() // str 字符串
  // 内联注解
  const overload: {
    (foo: string): string;
    (foo: number): number;
  } = (foo: any) => foo
  // 可实例化
  interface NewAble {
    new (): string;
  }
  declare const newAble: NewAble;
  const foo = new newAble
  ```
  
  
  
8. 类型断言

   Ts可以以任何方式去重写其推断和分析的类型，称为类型断言。

   ```typescript
   interface Foo{
     Bar : string
   }
   const f1 = {} as Foo;
   f1.bar = "1";
   const f2 = <Foo>{};
   f2.bar = "2";
   ```

   

9. Freshness

   检查字面量类型

   ```typescript
   function hasName(some: { name: string }) {
     some.name = "name"; // 正确
     some.age = 12; // 错误
   }
   ```

   

10. 类型保护

   typeof （typeof x === 'string'）

   instanceof (foo instanceof Foo) 

   in 检查对象是否存在对应属性

11. readonly 

    ```typescript
    type Foo = {
      readonly bar: string;
    };
    const f: Foo = { bar: "x" };
    f.bar = "y"; // 错误，不可修改
    ```

    

12. 泛型

    类的实例成员、类的方法、函数的参数、函数返回值之间的约束。

13. never

    never类型就是TypeScript中的底部类型。用于一个总会抛出错误的函数或一个总会抛出错误的函数。

    never与void的差异，void表示没有任何类型，never表示永远不存在的值的类型。

14. 异常处理

    ```typescript
    try {
      throw new Error("err.");
    } catch (error) {
      console.log(error)
    } 
    ```

## 代码风格约定

1. 使用camelCase形式为变量和函数命名。

2. 使用PascalCase形式为类命名。

3. 使用PascalCase形式为接口命名。

4. 类型别名使用PascalCase形式进行命名。

5. 命名空间使用PascalCase形式进行命名。

6. 枚举类型使用PascalCase形式进行命名。

7. 对于需要明确表明不可用的情况，null和undefined都不建议使用。

8. 格式化

   tsfmt命令格式化，使用单引号，空格两tab，如果想用extends或implements，则建议使用interface。

   

## TS编译原理

.....待续再看
