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

## 三，基础
### 数据类型
#### 基本数据类型
ts中定义变量时需要指定数据类型
```
let <变量名>：<类型>;

let isDone: boolean = false;
//boolean类型
//注意，使用构造函数 Boolean 创造的对象不是布尔值
let createdByNewBoolean: boolean = new Boolean(1);
//Boolean对象不是boolean值
let decLiteral: number = 6;
//number类型
let myName: string = 'Tom';
//string类型
let u: undefined = undefined;
//undefined类型
let n: null = null;
//null类型
let unusable: void = undefined;
//void类型，只能被赋值为undefined或者null
```
#### TS新增数据规则
##### 1，任意值类型
类似于js中的var和let定义的变量
```
let myFavoriteNumber: any = 'seven';
//任意值类型变量可以被赋值为任意类型的值
myFavoriteNumber = 7;

##未声明类型的变量默认是任意类型
let myFavoriteNumber;
myFavoriteNumber = 'seven';
//等价于
let myFavoriteNumber: any;
myFavoriteNumber = 'seven';
```
##### 2，类型推论
未指定类型的变量，在编译的时候，会根据其赋值推断变量类型
```
let myFavoriteNumber = 'seven';
//等价于
let myFavoriteNumber: string;
myFavoriteNumber = 'seven';

myFavoriteNumber = 7;//报错
```
注意类型推论和任意类型之间的区别:
1. 任意类型是在变量声明的时候未定义初始值， 系统默认为any;
2. 类型推论是在变量声明的时候定义初始值， ts编译的时候根据初始值推论其类型
##### 3，联合类型
变量定义的时候，可以为变量指定多个类型:联合类型。各类型之间用管道符 (|) 连接
```
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```
连接类型变量的属性和方法， 是多个联合类型的共有属性和方法
#### 对象类型（接口）
在TS中，使用接口（Interfaces）来定义对象的类型。接口只负责定义，不负责实际的操作和赋值
```
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```
在实现接口类型的变量时，必须与接口声明的保持一致，接口属性不能多或者少
```
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}
```
1. readonly 标记只读属性，只能在实现接口的时候初始化该属性；
2. ? 标记可选属性，实现接口时可以选择初始化或者不初始化该属性；
3. [propName: string]: any 标记任意属性

#### 函数类型
##### 1，函数参定义
TS规定，函数必需参数不能多也不能少，且参数类型必须匹配
```
function func_name( param: type1)：type2 {   
    ...
}
//参数的type1表示参数类型
//type2表示函数返回类型，类似于Java里的int，void等
```
##### 2，可选参数和参数默认值
可选参数与接口可选属性一致，问号 “?” 标识。
==TS规定，可选参数必须接在必需参数后面==
```
function buildName(firstName: string, lastName?: string)：number {
    ...
}
```
TS将添加了默认值的参数识别为可选参数，且不再受「可选参数必须接在必需参数后面」的限制了
```
function buildName(firstName: string = 'Tom', lastName: string)：string {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');
```
##### 3，剩余参数

```
function buildName(firstName: string, ...restOfName: string[]) {
    ...
}
```
##### 4，函数重载
TS引入函数重载，与Java中的函数重载概念一样：==参数个数、参数类型或者返回类型不一样的同名函数==
```
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```
TS会优先从最前面的函数定义开始匹配，如果多个函数定义有包含关系，带包含关系的函数定义在最后面
#### 类型断言
```
<类型>变量名
##或者
<变量名> as <类型>
```
注：在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种

型断言纯粹是一个编译时语法，同时，它也是一种为编译器提供关于如何分析代码的方法，不是类型转换


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

## 四，进阶












