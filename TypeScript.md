## 一，简介
1. 开发者/企业：微软
2. 与JavaScript的关系：JavaScript 的一个超集，是一种面向对象的编程语言
3. 设计目标：开发大型应用，它可以编译成纯 JavaScript，编译出来的 JavaScript 可以运行在任何浏览器上

## 二，安装

```
npm install -g typescript
##查看版本
$ tsc -v
Version 3.2.2
```
创建TypeScript文件，.ts作为 TypeScript 代码文件的扩展名

```
tsc test.ts
##将 TypeScript 转换为 JavaScript 代码
$ node test.js
##使用 node 命令来执行 test.js 文件
```

## 三，语法
TypeScript是类JavaScript，只介绍与JavaScript的不同之处
##### 1，Any类型：类型不明确的变量使用的一种数据类型，类似JavaScript未定义的var类型

##### 2，变量声明
（1）声明
```
1. var uname:string = "Runoob";
2. var uname:string;
3. var uname = "Runoob";  //与JavaScript一致
```
（2）断言：类似于Java里的强制类型转换

（3）类型推断：声明变量时未指定变量类型，变量类型将由第一次赋值操作的值的类型决定，再次赋值时必须是同类型的，否则会发生编译错误

（4）变量作用域

```
var global_num = 12; 
##全局变量，任何地方可用
class Numbers { 
   num_val = 13;  
   ##类变量，通过类实例访问
   static sval = 10;         
   ##静态变量，通过类名直接访问
   storeNum():void { 
      var local_num = 14;    
      ##局部变量
   } 
}

```
##### 3，函数
（1）函数参数

```
function func_name( param: type) {   
    ...
}
```
（2）可选参数

```
##可选参数用问号?标识
function buildName(firstName: string, lastName?: string) {
    ...
}
```
（3）默认参数

```
##直接给默认参数一个默认值，当不传时，参数取默认值
function function_name(param1: type,param2: type = default_value) { 
    ...
}
```
（4）剩余参数

```
function buildName(firstName: string, ...restOfName: string[]) {
    ...
}
```
（5）匿名函数，箭头函数，构造函数，递归函数等都与JavaScript一致，另TypeScript引入函数重载

##### 4，元组
与数组结构相同，比数组更强大，存储的元素数据类型可以不同，其余操作与数组一致
##### 5，联合类型与联合数组
通过管道符“|”给变量或者数组设置多种数据类型，且变量值只能是设置的其中一种类型

```
##联合类型变量
var val:string|number;
##联合类型数组
var arr:number[]|string[]; 
```
##### 6，接口
（1）接口定义

```
interface interface_name { 
    firstName:string, 
    lastName:string, 
    sayHi: ()=>string
}
```
（2）接口继承

```
interface IParent1 { 
    v1:number 
} 
 
interface IParent2 { 
    v2:number 
} 
 
interface Child extends IParent1, IParent2 { } 
var Iobj:Child = { v1:12, v2:23} 
```
==注意：接口不能转换为 JavaScript。 它只是 TypeScript 的一部分==

##### 7，类
TypeScript的类（class）与JavaScript的类是一致的，相比JavaScript，TypeScript类增加了访问控制修饰符，实现接口（implements ）等，与Java类似














