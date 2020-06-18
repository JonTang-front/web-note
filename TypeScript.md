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
> JS是一门弱类型语言，而TS是一门强类型语言，在定义变量时需要指定数据类型

> 变量声明方法：let/const <变量名>：<类型>

> 其中，类型可以是基础类型（boolean，number，string，undefined，null，never，void，any等），也可以是引用类型（Object，Array...）以及自定义类型等
#### 基础类型
1. boolean
```
let isDone: boolean = false;
typeof isDone===boolean   //true

//注意，使用构造函数 Boolean 创造的对象不是布尔值
let createdByNewBoolean: boolean = new Boolean(1);  //Error
//Boolean对象不是boolean值
```

2. number
```
//支持二进制，八进制，十进制，十六进制
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

3. string
```
//支持字符串模板
let myName: string = 'Tom'
let sentence: string = `Hello, my name is ${myName}.`

//同样，new String()生成的对象实例不是string类型
let test: string = new String('test')  //Error

//注意new String()和String()的区别
let test: string = String('test')  //true

```

4. undefined
```
let u: undefined = undefined;
```
默认情况下null和undefined是所有类型的子类型，也就是说可以把null和undefined 赋给任何类型的变量。然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自
5. null
```
let n: null = null;
//与undefined一致
```
6. any（任意值）
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
7. never：表示的是那些永不存在的值的类型。例如总是会抛出异常的表达式，根本不会有返回值的函数，永远为false的变量等。never是任何类型的子类型，即never可以赋值给任何类型，而任何其他类型的值都不可以赋值给never类型。在TS中作用不大
```
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}
```
8. void：空类型，例如没有返回值的函数。在TS中作用不大
```
function warnUser(): void {
    console.log("This is my warning message");
}

let unusable: void = undefined;
//void类型变量，只能被赋值为undefined或者null
```
9. 枚举类型：对JavaScript标准数据类型的一个补充
```
enum Color {Red, Green, Blue}
let c: Color = Color.Green;
//默认枚举编号从0开始，也可以自定义枚举编号，如
enum Color {Red = 1, Green = 2, Blue = 4}
let c: Color = Color.Green;
//与Object不同的是，枚举类型可以过值（value）查找对应的名字（key）。如
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];
console.log(colorName); //Green
```
#### 引用类型
1. Array
```
//在元素类型后面直接添加[]
let list: number[] = [1, 2, 3];

//或者使用数组泛型
let list: Array<number> = [1, 2, 3];
```
2. 元组（Tuple）：表示一个已知元素数量和类型的数组，各元素的类型不必相同
```
let x: [string, number];
//在对元组进行初始化时，初始化值类型必须一一对应。如
x = ['hello', 10]; // true

x = [10, 'hello']; //false
```
3. interface（接口）：查看接口部分

#### TS新增数据规则
##### 1，类型推论
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


##### 2，类型断言
开发者手动告诉编译器，自己定义的变量类型，类似于Java/C++中的强制类型转换，但是不进行特殊的数据检查，并且只在编译阶段起作用，对运行没有影响，是一种为编译器提供关于如何分析代码的方法，不是类型转换

类型断言的两种写法：
```
let someValue: any = "this is a string";
//写法一
let strLength: number = (<string>someValue).length;
//写法二
let strLength: number = (someValue as string).length;
```
JSX中只支持第二种写法
##### 3，联合类型
变量定义的时候，可以为变量指定多个类型:联合类型。各类型之间用管道符 (|) 连接
```
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```
连接类型变量的属性和方法， 是多个联合类型的共有属性和方法

### 函数
##### 1，函数声明 && 函数表达式
TS规定，函数必需参数个数和类型必须一致，且返回类型
```
function func_name( param: type1)：type2 {   
    ...
}
//参数的type1表示参数类型
//type2表示函数返回类型，类似于Java里的int，void等
```
函数表达式，形如：
```
let mySum = function (x: number, y: number): number {
    return x + y;
};
//采用类型断言，省略了mySum的类型定义，完整的写法应该是
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};
//注意区分TS中的=>与ES6中的=>，TS中的=>表示函数声明，ES6中的=>表示箭头函数
```

##### 2，可选参数 && 参数默认值
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
function buildName(firstName: string, ...restOfName: any[]) {
    ...
}
```
##### 4，this参数
当我们在window执行环境下执行闭包，闭包内部的this被赋值成window，而不是闭包定义时的环境。为了解决这个问题：
- ES6采用箭头函数定义闭包，闭包的this===定义环境的this；
- TS在编译时会报错，this被编译成any类型，需要手动指定this。如：
```
let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}
let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();
```

##### 5，函数重载
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

### 类
##### 定义 && 继承
TS中定义类与ES6中定义类基本类似，只是定义类时，所有的属性和方法（包括参数和返回值）都要指定具体的类型。如：
```
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}
```
与ES6一样，TS也使用extends实现类的继承。除类private标识的属性和方法，其余的属性和方法都可以被继承
##### 访问修饰符
> public：标识共有属性和方法，可以在任何地方被访问。TS中成员都默认为 public

> private：标识私有属性和方法，只能在声明属性和方法的类内部访问，且不能被继承，也不能在派生类（子类）中访问。如：
```
class Animal {
    private name: string;
    constructor(theName: string) { 
        this.name = theName; 
    }
}
class Rhino extends Animal {
    constructor() { 
        super("Rhino"); 
    }
    sayName() {
        return this.name;
    }
}
let rhino = new Rhino();
rhino.sayName(); //Error，不能被继承
```
> protected：标识受保护的属性和方法，可以被继承，但是只能在类以及子类中访问，不能在类外访问protected标识的属性和方法。如：
```
class Person {
    protected name: string;
    constructor(name: string) { this.name = name; }
}
class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name)
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}
let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch()); //可以在子类的实例方法中访问protected属性
console.log(howard.name); //Error  不能在类外访问
```
> readonly：标识只读属性，且只读属性必须在声明时或构造函数里被初始化。如：
```
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
```
参数属性：声明并初始化一个属性。如：
```
class Octopus {
    constructor([public/private/protected/readonly] name: string) {
    }
}
//相当于
class Octopus {
    [public/private/protected/readonly] name: string;
    constructor(theName: string) {
        this.name = theName;
    }
}
```
##### 存取器
TS类中的存取器与ES6中的一致。如：
```
class Employee {
    private _fullName: string;
    get fullName(): string {
        return this._fullName;
    }
    set fullName(newName: string) {
        this._fullName = newName;
    }
}
```
##### 静态属性
与ES6一样，TS类中也采用static标识静态属性和方法。如：
```
class Employee {
    static _fullName: string;
    hello: string = 'hello'
    static sayHello(): string{
        return this.hello + this._fullName
    }
}
```
##### 抽象类
TS采用abstract关键字定义抽象类和抽象方法，抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。如：
```
abstract class Animal {
    abstract makeSound(): void;
    move(): void {
        console.log('roaming the earch...');
    }
}
let animal = new Animal(); //Error，不能用抽象类创建实例
```
不能用抽象类创建实例
##### 类的类型
与其他语言一样，实例的类型是创建实例的构造函数。如：
```
typeof Array === 'function' //true

typeof Function === 'function'  //true

typeof Object === 'function'  //true

//类似的
class Greeter{}
typeof Greeter === 'function';  //true
Greeter.constructor === Function;  //true
let greeter = new Greeter();
typeof greeter === 'object';  //true
greeter instanceof Greeter;  //true
greeter.constructor === Greeter;  //true
```
⚠️typeof Greeter 与 typeof greeter 的区别

### 接口（interface）
> 在TS中，接口（Interfaces）的作用就是定义数据类型。接口只负责定义，不负责实际的操作和赋值。使用接口定义变量时，相当于生成一个对应类型的签名，如object，function，array，class类型等
#### 定义普通的对象类型（Object）
使用接口定义普通的对象类型时，与JavaScript中的对象类似，区别是在对象属性后面添加具体的属性类型，并且新增了一些TS规范。如：
```
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}
let p1: Person = {
    id: 1,
    name: 'p1',
    age: 27,
    addr: '浙江省杭州市西湖区',
    email: 'tuya.com'
}
```
其中：
1. 对象字面量属性，实现接口时必须出现，且属性名和属性类型要一致。如 name: string；
2. readonly 标记只读属性，只能在实现接口的时候初始化该属性，后期不能修改。如 readonly id: number；
3. ? 标记可选属性，实现接口时可以选择初始化或者不初始化该属性。如果初始化，属性类型必须一致。如 age?: number；
4. [propName: string]: any 标记任意拓展属性，如 addr, email等；

在实现接口类型的变量时，必须与接口声明的保持一致。
编译器只会检查：
1. 必需的属性（包括只读属性）是否存在，并且其类型是否匹配；
2. 可选属性是否出现，如果出现，类型必须一致

#### 定义函数类型（Function）
接口定义的函数类型是只有参数列表和返回值类型的调用签名。如：
```
interface SearchFunc {
  (source: string, subString: string): boolean;
}
//使用接口
let mySearch: SearchFunc = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}
```
在使用接口函数定义变量索引时：
1. 函数参数类型要对应匹配，返回参数类型可省略，TypeScript的类型系统会推断出参数类型；
2. 参数名不需要与接口函数定义一致

#### 定义索引类型（Array）
与使用接口描述函数类型差不多，可索引类型具有一个 索引签名，它描述了对象索引的类型和相应的索引返回值类型。如：
```
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
TypeScript支持两种索引签名：字符串和数字。数字索引的返回值必须是字符串索引返回值类型的子类型。如：
```
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}
interface NotOkay {
    [x: string]: Animal;
    [x: number]: Dog;
}
```

#### 类实现接口
与Java，C++语言一样，class采用implements字段去实现接口定义。如：
```
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```
#### 继承接口
和类一样，接口也可以相互继承。常用于提取多个接口里可重用的模块。如：
```
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

#### 混合类型
所谓混合类型接口，即一个对象可以同时具有上面提到的多种类型（普通对象类型，函数等）。如：
```
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}
function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}
```
#### 接口继承类
### 泛型
> 在不确定未来使用的数据类型时，可以利用泛型定义通用的接口、函数、类等，用户可以根据自己的数据类型来使用通用接口。签名后面使用<>定义泛型

⚠️ 泛型与any类型的区别：泛型定义的接口、函数等在使用过程中都是同一种类型（传入的数据类型），而any类型无法保证这一点。相当于泛型是被定制化的any类型
#### 泛型函数

```
function identity<T>(arg: T): T {
    return arg;
}
//调用
let output = identity<string>("myString");
//<string>可以省略，编译器会根据传入的参数进行类型推论，自动确定T的类型
let output = identity("myString");
```
#### 泛型接口
类似于使用接口定义函数。如：
```
interface GenericIdentityFn {
    <T>(arg: T): T;
}
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn = identity;
```
也可以把泛型提升到接口层，整个接口的所有变量和函数都使用同一个泛型。如：
```
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```
#### 泛型类
泛型类与泛型接口差不多，使用<>添加泛型跟在类名后面，整个泛型类的实例部分（包括实例属性和方法）都使用同一个泛型。如：
```
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```
⚠️ 类的静态属性不能使用这个泛型类型
#### 泛型约束
泛型约束用于约束传入参数的类型或者包含某些特殊的参数。一般我们都会定义一个接口描述约束条件，然后使用extends继承接口实现约束。如：
```
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
loggingIdentity(3);//error，3是number类型，不具有length属性
//一下具有length属性的入参都可以正常调用
loggingIdentity([3]);//true
loggingIdentity('3');//true
loggingIdentity({length: 10, value: 3});//true
```

## 四，进阶
