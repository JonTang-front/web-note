# 基础篇
### 一，let，const 和 var 
#### let 和 var
1，let存在使用作用域，即只在一定作用域内有效，var无作用域

```
//常表现在循环体内
for (var i = 0; i < 10; i++) {
  ...
}
console.log(i);   //i==10
for (let i = 0; i < 10; i++) {
  ...
}
console.log(i);   //ReferenceError: i is not defined
//let声明的s只在当前循环内有效
```
2，let不存在变量提升，不能再变量未声明之前使用，var存在变量提升，可以在变量未声明之前使用，默认值是undefined

```
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```
3，let与所在的作用域绑定（暂时性死区），即使作用域外存在与之同名的变量声明，也不影响作用域内的变量
#### const
本质：const声明的是一个常量，常量的值指向内存地址，所以const声明的值不能被修改。且const声明常量时必须立即初始化。

### 二，解构赋值
数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值，如果等号的右边不是可遍历的结构，将会报错
#### 数组
数组的元素是按次序排列的，变量的取值由它的位置决定
```
//等号左右两边结构相同，完全解构
let [a, b, c] = [1, 2, 3];
let [foo, [[bar], baz]] = [1, [[2], 3]];
let [head, ...tail] = [1, 2, 3, 4];

//等号左右两边结构不同，不完全解构
let [bar, foo] = [1];
//bar===1，foo===undefined
let [a, [b], d] = [1, [2, 3], 4];
//a===1，b===2，d===4

//解构设置默认值
let [foo = true] = [];
// foo===true

let [x, y = 'b'] = ['a']; 
// x='a', y='b'

let [x = 1] = [null];
x // null
//ES6判断x===undefined时，默认值才会生效，null==undefined，但是null!==undefined

```
#### 对象
对象的解析构，变量必须与属性同名

```
let { bar, foo } = { foo: 'aaa', bar: 'bbb' };
// foo==="aaa"
// bar==="bbb"
let { baz } = { foo: 'aaa', bar: 'bbb' };
// baz===undefined

//如果变量名与属性名不一致，必须写成下面这样
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
// baz==="aaa"

//解析构设置默认值
var {x = 3} = {};
//x===3
```
#### 使用场景
1，交换变量的值

```
let x = 1;
let y = 2;

[x, y] = [y, x];
```
2，提取对象的值

```
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;
```
3，函数参数默认值

```
function ajax({
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
    ...
}
```
4，加载模块接口

```
const { SourceMapConsumer, SourceNode } = require("source-map");
```
### 三，字符串扩展
#### 字符的 Unicode 表示法
允许采用\uxxxx形式表示一个字符，其中xxxx表示字符的 Unicode 码点（ASCII编码）

```
//只限于码点在\u0000~\uFFFF之间的字符，超出这个范围的字符，必须用两个双字节的形式表示
"\uD842\uDFB7"
// "𠮷"

"\u20BB7"
// " 7"
```
#### 字符串的遍历器接口

```
//字符串可以被for...of循环遍历
for (let codePoint of 'foo') {
  console.log(codePoint)
}
// "f"
// "o"
// "o"
```
#### 模板字符串
用反引号（`）标识，${变量名或者JavaScript表达式}嵌入变量
```
`Hello ${name}, how are you ${time}?`
```
#### 常用==实例==方法
1. includes()，是否包含参数字符串，返回Boolean值
2. startsWith()，是否以参数字符串开头，返回Boolean值
3. endsWith()，是否以参数字符串结尾，返回Boolean值
4. repeat()，将目标字符串重复参数遍后返回
```
'x'.repeat(3) // "xxx"
//如果参数是小数，向下取整
```
5. trimStart()，trimEnd()与trim()一样，分别表示消除字符串头部和尾部的空格，返回新字符串，不影响原来的字符串
6. matchAll()，返回一个正则表达式在当前字符串的所有匹配
### 四，Number和Math对象扩展
#### Number扩展
1. 分别用前缀0b（或0B）和0o（或0O）表示二进制数和八进制数，使用Number()转换成十进制数
2.  Number.parseInt()， Number.parseFloat()替代ES5的parseInt()，parseFloat()
3. Number.isFinite(), Number.isNaN()判断数字是否为有限的（finite），即不是Infinity和NaN
#### Math扩展
1. Math.trunc()向下取整，与Math.floor()功能一样
2. ES5中的Math.floor()向下取整，Math.ceil()向上取整，Math.round()四舍五入取整
3. 指数运算符（**）
```
2 ** 2 // 4
2 ** 3 // 8
```
### 五，数组扩展
#### 扩展运算符（...）
应用场景：
1. 复制数组
```
//ES5复制数组
const a1 = [1, 2];
const a2 = a1.concat();

//ES6复制更简洁
const a1 = [1, 2];
// 写法一
const a2 = [...a1];
// 写法二
const [...a2] = a1;
```
2. 合并数组
```
const arr1 = ['a', 'b'];
const arr2 = ['c'];
const arr3 = ['d', 'e'];

// ES5 的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6 的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```
3. 与解构赋值结合
```
const [first, ...rest] = [1, 2, 3, 4, 5];
//first===1
//rest===[2, 3, 4, 5]
```
#### Array.from()
用于将可遍历数据结构转换成真正的数组

```
let ps = document.querySelectorAll('p');
Array.from(ps).filter(p => {
  ...
});
//扩展运算符也可以实现
[...ps].filter(p => {
  ...
});
```
#### 实例方法
1. find()&findIndex()，前者用于找出第一个符合条件的数组成员，如果没有符合条件的成员，则返回undefined。后者用于找出第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。参数都是一个回调函数
```
[1, 4, -5, 10].find((value, index, arr) => n < 0);

[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
});
//value当前遍历值，index当前值索引，arr当前遍历数组
```
2. entries()，keys()和values()，都返回一个遍历器对象，可以用for...of循环进行遍历，keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历
3. includes()&indexOf()，表示某个数组是否包含给定的值，方法返回一个布尔值，与字符串的includes方法类似
```
//与ES5 indexOf方法的区别
//ES5内部使用严格相等运算符（===）进行判断，这会导致对NaN的误判
[NaN].indexOf(NaN)
// -1
//ES6不会存在这个问题
[NaN].includes(NaN)
// true
```
4. flat()&flatMap()，前者用于将嵌套的数组“拉平”，变成一维的数组。返回一个新数组，对原数据没有影响。参数表示将多层“拉平”，默认为1，用Infinity关键字作为参数，可以将无论多少层数组都拉平。后者原数组的每个成员执行一个函数，然后对返回值组成的数组执行flat()方法，该方法返回一个新数组，不改变原数组。
```
[1, [2, [3]]].flat(Infinity)
// [1, 2, 3]

// 相当于 [[2, 4], [3, 6], [4, 8]].flat()
[2, 3, 4].flatMap((x) => [x, x * 2])
// [2, 4, 3, 6, 4, 8]
```

5. sort()，数组排序，排序关键字相同的项目，排序前后的顺序不变

### 六，对象扩展
#### 属性简洁表示法

```
//属性简洁写法
{
    x,
    y
}
//等价于
{
    x: x,
    y: y
}

//方法简洁写法
const o = {
  method() {
    return "Hello!";
  }
};
// 等价于
const o = {
  method: function() {
    return "Hello!";
  }
};
```
#### 属性的可枚举性和遍历
Object.getOwnPropertyDescriptor方法可以获取该属性的描述对象
```
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,
//    configurable: true
//  }
```
目前，有四个操作会忽略enumerable为false的属性：

1. for...in循环：只遍历对象自身的和==继承的可枚举的属性==。
2. Object.keys()：返回对象自身的所有可枚举的属性的键名。
3. JSON.stringify()：只串行化对象自身的可枚举的属性。
4. Object.assign()： 忽略enumerable为false的属性，只拷贝对象自身的可枚举的属性。
---
其中，只有for...in会返回继承的属性，其他三个方法都会忽略继承的属性，只处理对象自身的属性。
==尽量不要用for...in循环，而用Object.keys()代替==
#### super 关键字
this关键字总是指向函数所在的当前对象，而super总是指向当前对象的原型对象
```
const proto = {
  foo: 'hello'
};

const obj = {
  foo: 'world',
  find() {
    return super.foo;
  }
};

Object.setPrototypeOf(obj, proto);
obj.find()
```
JavaScript 引擎内部，super.foo等同于Object.getPrototypeOf(this).foo（属性）或Object.getPrototypeOf(this).foo.call(this)（方法）
#### 对象的扩展运算符（...）
##### 对象解构赋值
```
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x // 1
y // 2
z // { a: 3, b: 4 }
```
1. 解构赋值表达式等号右边不能是undefined和null，因为undefined和null无法转换成对象
```
let { ...z } = null; // 运行时错误
let { ...z } = undefined; // 运行时错误
```
2. 解构赋值必须是最后一个参数，否则会报错
```
let { ...x, y, z } = someObject; // 句法错误
let { x, ...y, ...z } = someObject; // 句法错误
let {x, y, ...z} = someObject; //正确
```
3. 解构赋值的拷贝是浅拷贝，即如果一个键的值是复合类型的值（数组、对象、函数）、那么解构赋值拷贝的是这个值的引用，而不是这个值的副本
```
let obj = { a: { b: 1 } };
let { ...x } = obj;
obj.a.b = 2;
x.a.b // 2
```
4. 扩展运算符的解构赋值，不能复制继承自原型对象的属性

##### 数组转对象

```
let z = { a: 3, b: 4 };
let n = { ...z };
n // { a: 3, b: 4 }
//如果扩展运算符后面不是对象，则会自动将其转为对象   ...Object(目标)
```
#### 新增方法
##### Object.is()
用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。
ES5 比较两个值是否相等，只有两个运算符：相等运算符（==）和严格相等运算符（===），前者会自动转换数据类型，后者的NaN不等于自身，以及+0等于-0。Object.is()克服了ES5中严格相等的问题，即+0不等于-0，NaN等于自身
```
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```
##### Object.assign()
用于对象的合并，将多个源对象属性合并到目标对象，且后面的源对象属性覆盖前面的源对象同名属性
```
Object.assign(target, source1, source2);
//目标对象必须是对象，或者可转换成对象，undefined和null无法转成对象，所以如果它们作为参数，就会报错。源对象如果不是对象，或者无法转换成对象，在合并的时候会被忽略（数值、布尔值等）。
```
应用场景：
1. 为对象添加属性和方法
```
Object.assign(SomeClass.prototype, {
  someProperty: xxx,
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});
```
2. 克隆和合并对象

##### Object.getOwnPropertyDescriptors()
回指定对象所有自身属性（非继承属性）的描述对象，相比ES5的Object.getOwnPropertyDescriptor()只能某个对象属性的描述对象，功能有所增强。主要是为了解决Object.assign()无法正确拷贝get属性和set属性的问题。
```
//Object.assign方法总是拷贝一个属性的值，而不会拷贝它背后的赋值方法或取值方法。
//Object.getOwnPropertyDescriptors()方法配合Object.defineProperties()方法，就可以实现正确拷贝
const shallowMerge = (target, source) => Object.defineProperties(
  target,
  Object.getOwnPropertyDescriptors(source)
);
```
##### Object.setPrototypeOf() & Object.getPrototypeOf() & Object.create()
Object.setPrototypeOf()：用来设置对象的原型对象；
```
Object.setPrototypeOf(object, prototype)
```
Object.getPrototypeOf()：用来获取对象的原型对象；
```
Object.getPrototypeOf(obj);
//如果参数不是对象，会被自动转为对象
// 等同于 Object.getPrototypeOf(Number(1))
Object.getPrototypeOf(1)
```
##### Object.keys()，Object.values()，Object.entries() & Object.fromEntries()
Object.keys()，Object.values()，Object.entries()与数组的操作方法一致，Object.fromEntries()是Object.entries()的逆操作

```
const obj = { foo: 'bar', baz: 42 };
Object.entries(obj)
// [ ["foo", "bar"], ["baz", 42] ]

const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
]);

Object.fromEntries(entries)
// { foo: "bar", baz: 42 }
```

### 七，函数扩展
#### 函数参数
##### 参数默认值

```
//常规参数默认值
function sum(x = 0, y = 1) {
  return x + y;
}
sum(5); //6

//参数默认值解构赋值
function foo({x, y = 5}) {
  console.log(x, y);
}
foo() // TypeError: Cannot read property 'x' of undefined
function foo({x, y = 5} = {}) {
  console.log(x, y);
}

foo() //正确
```

```
// 写法一
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
```
写法一函数参数的默认值是空对象，但是设置了对象解构赋值的默认值；写法二函数参数的默认值是一个有具体属性的对象，但是没有设置对象解构赋值的默认值
##### rest 参数（...变量名）

```
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```

#### 箭头函数

```
//只有一个参数时，箭头左边的括号可以省略
//函数体只有一个语句时，表示直接返回
var f = v => v;

//没有参数或者有多个参数时，箭头左边的括号不能省略
var f = () => 'hello';
var f = (1, 2) => 'world';

//函数体多余一条语句时，代码块需用大括号{}包裹
var f = () => {let a = 'world'; return a;};

//如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错
let getTempItem = id => ({ id: id, name: "Temp" });
```
注意：
1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。换言之，箭头函数没有this对象，而是继承父函数的this对象；
2. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误；
3. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest（...变量名） 参数代替；
4. 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

### 八，Set 和 Map 数据结构
#### Set
结构与数组类似，没有重复的值
```
//创建一个Set
const s = new Set();
//Set函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化
const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]
const set = new Set(document.querySelectorAll('div'));

//添加数据
s.add(数据); //返回Set结构

//判断是否含有某个值
s.has(值); //有 =》 true，无 =》 false

//删除数据
s.delete(值); //成功 =》 true，失败 =》 false

//清空Set
s.clear();

//Set继承了数组的keys()，values()，entries()方法都返回一个遍历器
//forEach()使用回调函数遍历每个成员，功能都与数组一致。
for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```

##### 应用场景
```
//数组去重
[...new Set(array)]
//或者
const items = new Set([1, 2, 3, 4, 5]);
const array = Array.from(items);
```
#### WeakSet
WeakSet 结构与 Set 类似，也是不重复的值的集合。区别：
1. WeakSet 的成员只能是对象，而不能是其他类型的值；
2. WeakSet 中的对象都是弱引用，都不计入垃圾回收机制。也就是说，如果其他地方不再引用对象时，不管对象是否在WeakSet，都会被回收。

#### Map
Map的数据结构和JavaScript的对象（Object）类似。

区别： Object只能用字符串作为键（数字会被toString()转换成字符串），Map可以用任何数据类型作为键；
```
//创建Map
const m = new Map();
//任何具有 Iterator 接口、且每个成员都是一个 “双元素” 的数组的数据结构都可以当作Map构造函数的参数
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);
const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);

//添加数据
m.set('edition', 6); //返回Map结构

//查看Map成员数
m.size;

//获取数据
m.get('edition');  //返回键对应的值或者undefined

//判断是否含有某个数据
m.has('edition');  //有 =》 true，无 =》 false

//删除数据
m.delete('edition'); //成功 =》 true，失败 =》 false

//清空Map
m.clear();

//Map继承了数组的keys()，values()，entries()方法都返回一个遍历器，功能都与数组一致
//forEach()遍历 Map 的所有成员
const map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

//Map 结构转为数组结构
const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
// [1, 2, 3]

[...map.values()]
// ['one', 'two', 'three']

[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]

[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]
```
Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键

```
const map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined

const a = ['a'];
const b = a;
map.set(a, 555);
map.get(b) //555

```
#### WeakMap
WeakMap结构与Map结构类似，也是用于生成键值对的集合。区别：
1. WeakMap只接受对象作为键名（null除外），不接受其他类型的值作为键名；
2. WeakMap的键名所指向的对象，不计入垃圾回收机制。

### 九，Symbol类型
为了解决属性名冲突的问题，ES6引入Symbol数据类型，保证每个属性的名字都是独一无二的。Symbol是 JavaScript 语言的第七种数据类型，前六种是：undefined、null、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。
ES6之前，对象属性名只能是字符串类型，ES6中对象属性名可以是字符串和Symbol类型。
```
let s = Symbol();
typeof s
// "symbol"
//Symbol 值不是对象，是一种类似于字符串的数据类型，不能添加属性，也不能与其他类型的值进行运算

//可以给Symbol传递一个参数作为symbol变量的描述
//参数是string类型，如果不是string类型，会调用toString()转换成string类型
let s1 = Symbol('foo');
//注意：Symbol函数参数与symbol类型变量无关，只是一个描述而已，即使相同的参数，返回的值也是不相等的

//Symbol类型的值可以转换成String类型和Boolean类型
let sym = Symbol('My symbol');
String(sym) // 'Symbol(My symbol)'
//或者
sym.toString() 

Boolean(sym) // true
!sym  // false
```
#### Symbol类型值作为属性名
```
let mySymbol = Symbol();

// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';

// 第二种写法
let a = {
  [mySymbol]: 'Hello!'
};

// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });
```
使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中
#### 遍历Symbol类型的属性名
Symbol 作为属性名，该属性不会出现在for...in、for...of循环中，也不会被Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()返回。但是，它也不是私有属性，获取指定对象的所有 Symbol 属性名有两种方法：
1. Object.getOwnPropertySymbols
```
const obj = {};
let a = Symbol('a');
let b = Symbol('b');

obj[a] = 'Hello';
obj[b] = 'World';

const objectSymbols = Object.getOwnPropertySymbols(obj);

objectSymbols
// [Symbol(a), Symbol(b)]
```
2. Reflect.ownKeys获取对象所有类型的键名，包括常规键名和 Symbol 键名
```
let obj = {
  [Symbol('my_key')]: 1,
  enum: 2,
  nonEnum: 3
};

Reflect.ownKeys(obj)
//  ["enum", "nonEnum", Symbol(my_key)]
```
#### Symbol.for() & Symbol.keyFor()
##### Symbol.for()
接收一个字符串作为参数，全局搜索以该参数作为名称的 Symbol 值。如果有，就返回该Symbol值，如果没有，就新建一个以该字符串为名称的 Symbol 值，并将其注册到全局。

Symbol.for()与Symbol()都会生成新的 Symbol值，区别：
1. Symbol()每次都会创建新的Symbol值，Symbol.for()会先全局搜索，搜索不到时才会创建；
2. Symbol.for()创建的值会在全局登记，可以通过Symbol.for()搜索到，Symbol()创建的值不会在全局登记，Symbol.for()搜索不到。

##### Symbol.keyFor()
返回一个已登记的 Symbol 类型值的key
```
let s1 = Symbol.for("foo");
Symbol.keyFor(s1) // "foo"

let s2 = Symbol("foo");
Symbol.keyFor(s2) // undefined
```
#### 对象内置的Symbol值
1. Symbol.hasInstance：指向一个内部方法，当其他对象使用instanceof运算符，判断是否为该对象的实例时，会调用这个方法。
2. Symbol.isConcatSpreadable：表示该对象用于Array.prototype.concat()时，是否可以展开，等于一个布尔值。
3. Symbol.match：指向一个函数，当执行str.match(myObject)时，如果该属性存在，会调用它，返回该方法的返回值。
4. Symbol.replace：指向一个方法，当该对象被String.prototype.replace方法调用时，会返回该方法的返回值。
5. Symbol.search：指向一个方法，当该对象被String.prototype.search方法调用时，会返回该方法的返回值。
6. Symbol.split：指向一个方法，当该对象被String.prototype.split方法调用时，会返回该方法的返回值。
7. Symbol.iterator：指向该对象的默认遍历器方法
```
const myIterable = {};
myIterable[Symbol.iterator] = function* () {
  yield 1;
  yield 2;
  yield 3;
};

[...myIterable] // [1, 2, 3]
```

### 十，Proxy（代理器）
Proxy 用于修改某些操作的默认行为，相当于在目标对象之前设置了一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。
```
var proxy = new Proxy(target, handler);
//target表示所要拦截的目标对象，handler表示定制拦截行为对象
//注意，要使得Proxy起作用，必须针对Proxy实例（proxy）进行操作，而不是针对目标对象（target）进行操作
//如果handler是空对象，那就相当于直接操作目标对象
```
#### 拦截操作
目前支持的拦截操作有13个
1. get(target, propKey, receiver)：拦截对象属性的读取，参数次为目标对象、属性名和 Proxy 实例本身，比如proxy.foo和proxy['foo']。
2. set(target, propKey, value, receiver)：拦截对象属性的设置，参数次为目标对象、属性名、属性值和 Proxy 实例本身。比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
3. has(target, propKey)：拦截propKey in proxy的操作，参数分别是目标对象、需查询的属性名，返回一个布尔值。
4. deleteProperty(target, propKey)：拦截delete proxy[propKey]的操作，返回一个布尔值。
5. ownKeys(target)：拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
6. getOwnPropertyDescriptor(target, propKey)：拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
7. defineProperty(target, propKey, propDesc)：拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
8. preventExtensions(target)：拦截Object.preventExtensions(proxy)，返回一个布尔值。
9. getPrototypeOf(target)：拦截Object.getPrototypeOf(proxy)，返回一个对象。
10. isExtensible(target)：拦截Object.isExtensible(proxy)，返回一个布尔值。
11. setPrototypeOf(target, proto)：拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
12. apply(target, object, args)：拦截 Proxy 实例作为函数调用的操作，参数依次是目标对象、目标对象的上下文对象（this）和目标对象的参数数组。比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
13. construct(target, args)：拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。

#### 应用技巧
Proxy实例作为其他对象的原型对象
```
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

let obj = Object.create(proxy);
obj.time // 35
```
#### Proxy.revocable()
Proxy.revocable方法返回一个可取消的 Proxy 实例，该方法返回一个对象，该对象的proxy属性是Proxy实例，revoke属性是一个函数，可以取消Proxy实例。
```
let target = {};
let handler = {};

let {proxy, revoke} = Proxy.revocable(target, handler);

proxy.foo = 123;
proxy.foo // 123

revoke();
//当执行revoke函数之后，proxy实例被取消，再访问Proxy实例，就会抛出一个错误。
proxy.foo // TypeError: Revoked
```
使用场景：目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。

#### Proxy的this指针
目标对象内部的this关键字会指向 Proxy 代理
```
const target = {
  m: function () {
    console.log(this === proxy);
  }
};
const handler = {};

const proxy = new Proxy(target, handler);

target.m() // false
proxy.m()  // true
```
### 十一，Reflect对象
ES6把一些属于语言内部的操作对象的方法划分到Reflect对象中，并且封装优化内部方法，使其变得更加合理。
1.  将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上。现阶段，某些方法同时在Object和Reflect对象上部署，未来的新方法将只部署在Reflect对象上。也就是说，从Reflect对象上可以拿到语言内部的方法。
2.  修改某些Object方法的返回结果，让其变得更合理。比如，Object.defineProperty(obj, name, desc)在无法定义属性时，会抛出一个错误，而Reflect.defineProperty(obj, name, desc)则会返回false。
3.  让Object操作都变成函数行为。某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为。
4.  Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法。方便Proxy修改默认行为的时候，还可以获取默认行为。
```
var loggedObj = new Proxy(obj, {
  get(target, name) {
    console.log('get', target, name);
    return Reflect.get(target, name);
  }
}
```
#### Reflect对象集成的API（与Proxy的13个API对应）
1. Reflect.apply(target, thisArg, args)
2. Reflect.construct(target, args)
3. Reflect.get(target, name, receiver)
4. Reflect.set(target, name, value, receiver)
5. Reflect.defineProperty(target, name, desc)
6. Reflect.deleteProperty(target, name)
7. Reflect.has(target, name)
8. Reflect.ownKeys(target)
9. Reflect.isExtensible(target)
10. Reflect.preventExtensions(target)
11. Reflect.getOwnPropertyDescriptor(target, name)
12. Reflect.getPrototypeOf(target)
13. Reflect.setPrototypeOf(target, prototype)

### 十二，Iterator对象和for...of循环
为Array，Object，Set，Map等数据结构提供统一的遍历访问接口。ES6 规定，默认的 Iterator接口部署在数据结构的Symbol.iterator属性，或者说，==一个数据结构只要具有Symbol.iterator属性，就可以认为是“可遍历的”==。Symbol.iterator属性值是一个遍历器生成函数，执行该函数返回一个遍历器。for...of循环主要供部署了Iterator 接口的数据结构遍历数据，与ES5的for...in遍历Object类似。
Iterator 的遍历过程：
1. 创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。
2. 第一次调用指针对象的next方法，可以将指针指向数据结构的第一个成员。
3. 第二次调用指针对象的next方法，指针就指向数据结构的第二个成员。
4. 不断调用指针对象的next方法，直到它指向数据结构的结束位置。

---

==每次调用next方法都会返回指针对象当前指向的数据对象。数据对象包含一个value属性和一个done属性，value是当前成员的值，done标记遍历是否结束。遍历未结束时，done的值为false；遍历结束时，value的值是undefined，done的值是true。==
#### 原生具备 Iterator 接口的数据结构
1. Array
2. Map
3. Set
4. String
5. TypedArray
6. 函数的 arguments 对象或者...rest对象
7. NodeList 对象

原生部署 Iterator 接口的数据结构，不用自己写遍历器生成函数，for...of循环会自动遍历它们。除此之外，其他数据结构（主要是对象）的 Iterator 接口，都需要自己在Symbol.iterator属性上面部署，这样才会被for...of循环遍历。

注意：Object对象没有默认部署Iterator接口，需要自己在Symbol.iterator属性上部署，或者采用ES5的for...in，Object.keys()，Object.value()...等方法才能遍历对象。
```
//解决Iterator接口遍历普通对象
//方法一：遍历Object.keys生成的对象键名数组
for (var key of Object.keys(someObject)) {
  console.log(key + ': ' + someObject[key]);
}

//方法二：使用 Generator 函数包装对象
function* entries(obj) {
  for (let key of Object.keys(obj)) {
    yield [key, obj[key]];
  }
}

for (let [key, value] of entries(obj)) {
  console.log(key, '->', value);
}
// a -> 1
// b -> 2
// c -> 3
```

#### 调用 Iterator 接口的场合
1. 解构赋值
2. 拓展运算
3. yield*
4. Array.from()，Map(), Set(), WeakMap(), WeakSet()，Promise.all()等
#### ES5的for...in，forEach & ES6的for...of
1. forEach无法使用break命令，continue命令，return命令控制循环；
2. ==for...in专为遍历Object而设计，且只能遍历到对象的属性键名==。采用for...in遍历数组时，会把数组转换成Object数据结构，键名是数组下标；
3. for...in遍历数组时，顺序不可控，甚至还会遍历原型链上的键；
4. for...of不能直接遍历普通对象，只能遍历部署了Iterator接口数据结构，需要对对象进行加工处理；
5. ==for...of遍历数组时，直接返回数组项，而不是下标（键名）==

# 进阶篇
### 一，Promise对象
ES6引入Promise对象主要用来封装异步操作，异步操作结束后，根据操作结果返回相应的状态。与ES5的回调函数和事件相比，回调函数是函数的横向扩展，而Promise是函数的纵向扩展，代码结构更加清晰。
#### 特点
1. 完全封闭的三种状态（pending，fulfilled，rejected）分别表示进行中，已成功，已失败；
2. 状态不可逆，即一旦状态改变，就不会再发生变化。只可能是从pending=》fulfilled，或者pending=》rejected；

#### 用法
```
const promise = new Promise(function(resolve, reject) {
  // ... some code
    //异步操作逻辑
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```
#### then && catch && finally
##### then
定义在Promise的原型对象上（Promise.prototype），为 Promise 实例添加状态改变时的回调函数。
then方法接收两个回调函数：
1. 第一个回调函数在Promise状态变为已成功时执行，参数是resolve方法返回的值；
2. 第二个回调函数在Promise状态变为已失败时执行，参数是reject方法返回的值（可选）。

##### catch
catch是then方法的别名，相当于不指定fulfilled的回调函数，只指定rejected的回调函数的then方法【.then(null, rejection)或.then(undefined, rejection)】，用于捕捉Promise抛出的异常和rejected状态。then方法指定的回调函数，如果运行中抛出错误，也会被catch方法捕获。

==实际开发中，不要在then方法里面定义Reject状态的回调函数，而是多个then方法链式执行，最后跟一个catch方法==

##### finally
finally方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。
```
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```
finally方法的回调函数不接受任何参数，不关心也不知道promise的状态，执行的操作应该是与状态无关的。

##### then 和 catch 方法返回的都是一个新的Promise实例，因此可以链式调用。

#### Promise静态方法
##### Promise.all
用于将多个 Promise 实例，包装成一个==新的 Promise 实例==。参数是一个Promise 实例的数组或者一个部署了Iterator接口的数据结构（可遍历），且返回的每个成员都是 Promise 实例。如果参数中包含不是Promise实例，则先调用Promise.resolve()方法转换成Promise实例。
```
const p = Promise.all([p1, p2, p3]);
```
状态：
1. 只有p1、p2、p3的状态都是已成功（resolve），p的状态才是成功的；
2. 只要p1、p2、p3之中有一个被rejected，p的状态都是失败的（reject）；

##### Promise.race
与Promise.all方法类似，都是把多个Promise实例包装成一个新的Promise实例返回

与Promise.all有区别的是，Promise.race方法产生的Promise实例与最先发生状态改变的参数Promise实例状态一致。

##### Promise.allSettled
也是把多个Promise实例包装成一个新的Promise实例返回。

Promise.allSettled返回的Promise实例必须等参数中的多个Promise实例状态发生改变才会改变状态，确保所有异步操作都已完成。

##### Promise.any
也是把多个Promise实例包装成一个新的Promise实例返回。与Promise.all的效果相反，只要有一个Promise实例变成已成功，包装实例就是已成功；只有所有的Promise实例变成已失败，包装实例才变成已失败。

##### Promise.resolve
生成一个Promise实例
1. 不带参数时，直接返回一个resolved状态的 Promise 对象；
2. 参数是一个原始值或者原始类型的对象时，返回一个新的 Promise 对象，状态为resolved。回调函数立即执行，参数作为回调函数参数传入；
```
const p = Promise.resolve('Hello');
//相当于
const p = new Promise((resolve, reject) => resolve('Hello'));
p.then(function (s){
  console.log(s)
});
// Hello
```
3. 参数是一个thenable对象时，转为Promise对象并立即执行参数对象的then方；
4. 如果参数是 Promise 实例，原封不动返回该实例；

##### Promise.reject
返回一个新的 Promise 实例，状态为rejected，回调函数会立即执行，参数作为回调函数参数传入。
```
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'));

p.catch(error => {
    console.error(error);   //出错了
})
```
#### Promise 和 setTimeout、setInterval执行顺序

#### 注意
1. 立即 resolved的Promise是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。一般在resolve和reject之前加上return，防止继续往下执行；
2. Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。实际开发中通常是多个then方法链式执行，最后跟一个catch方法。


### 二，Generator（生成器） 函数语法与应用
#### 介绍
1. Generator函数是一个状态机，封装了多个内部状态，内部状态采用yield字段标识（产出的意思）；
2. Generator函数是一个遍历器对象生成函数，执行Generator函数返回一个遍历器Iterator对象；
3. Generator函数与普通函数的定义是一样的，只是在function后面紧跟着一个“*”号；如：
```
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
hw.next();   //hello
hw.next();   //world
//执行helloWorldGenerator返回一个遍历器对象hw
//调用遍历器的next方法依次获取helloWorldGenerator的内部状态
```
#### Generator函数执行流程
1. 调用Generator函数返回一个遍历器对象，遍历器对象指向函数起始位置，类似数组的内存起始地址；
2. 第一次调用遍历器的next方法，从函数其实位置开始执行，直到遇到第一个yield表达式为止，并把yield后面紧跟的表达式作为value的值返回，done的值为false；
3. 第二次调用next方法，从上次停下的位置接着往下执行，直到遇到下一个yield表达式，把yield后面紧跟的表达式作为value的值返回...
4. 若遇到return，则把return紧跟的表达式作为value的值返回，done的值为true；若没有return，则执行到函数结束，value的值为undefined，done的值为true；
5. 一旦done的值为true，再调用遍历器的next方法返回的都是value为undefined，done为true。

##### yield & return
1. 都能返回紧跟着的表达式；
2. yield具备位置记忆功能，可以从函数上次暂停的位置继续执行。return不具备记忆功能；
3. 一个Generator函数可以有多个yield表达式，但只能有一个return表达式；
4. yield表达式只能用在 Generator 函数里面，用在其他地方都会报错。

##### next方法的参数
next的参数被当作上一个yield表达式（而不是单个变量）的返回值

==注：由于next方法的参数表示上一个yield表达式的返回值，所以在第一次使用next方法时，传递参数是无效的==
```
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}
var b = foo(5);   //x=5
b.next() // { value:6, done:false }  //返回yield后面的表达式5+1=6
b.next(12) // { value:8, done:false }  //x+1=12,x=11,y=12*2=24,返回24/3=8
b.next(13) // { value:42, done:true } //y/3=13,z=13,返回x+y+z=5+24+13=42
```
##### yield* 表达式
用来在一个 Generator 函数里面执行另一个 Generator 函数
```
function* foo() {
  yield 'a';
  yield 'b';
}
function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}
```
注意：
1. for...of遍历 Generator 函数时，默认执行Generator 函数的next方法，但是返回值不包括return后面的表达式；
2. Generator 函数返回的遍历器对象是Generator 函数的一个实例，继承了prototype对象上的方法；
3. Generator 函数也不能跟new命令一起用，会报错；
4. Generator 函数的this不是指向遍历器对象，返回的遍历器对象无法直接获取Generator 函数内部this绑定的值。
```
function* g() {
  this.a = 11;
}

let obj = g();
obj.next();
obj.a // undefined

//变通办法
let obj = g.call(g.prototype);
obj.a  //11
```
#### Generator函数应用
##### 异步操作
整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用yield语句注明
```
const fetch = require('node-fetch');
function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}

var g = gen();
var result = g.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

##### 控制流管理
##### 部署 Iterator 接口
利用 Generator 函数，可以在任意对象上部署 Iterator 接口，使其可以被for...of遍历
```
function* iterEntries(obj) {
  let keys = Object.keys(obj);
  for (let i=0; i < keys.length; i++) {
    let key = keys[i];
    yield [key, obj[key]];
  }
}

let myObj = { foo: 3, bar: 7 };

for (let [key, value] of iterEntries(myObj)) {
  console.log(key, value);
}

// foo 3
// bar 7
```
##### 作为数据结构
可以看作是一个数组结构
### 三，async函数
async函数就是 Generator 函数的语法糖，把Generator函数的*换成async标识放在function前，yield换成await，用来解决异步操作。
与Generator函数的区别：
1. async函数返回一个Promise对象，Generator函数返回一个Iterator对象；
2. async函数内置执行器，可以自动往下执行，而不像Generator函数必须通过调用next方法往下执行；
3. async函数的await命令后面是一个Promise对象用来封装异步操作（其他类型会被Promise.resolve()转换成立即执行的Promise对象）。

```
function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}

asyncPrint('hello world', 50);
```
1. async函数内部return语句返回的值，会成为then方法回调函数的参数；
2. async函数返回的 Promise 对象，必须等到内部所有await命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到return语句或者抛出错误（包括promise的rejected）；
3. 任何一个await语句后面的Promise对象变为reject状态，那么整个async函数都会中断执行。通常给可能会变成rejected的promise添加catch或者放在try/catch中；
```
async function f() {
  await Promise.reject('出错了');
  await Promise.resolve('hello world'); // 不会执行
}
//方法一
async function f() {
  try {
    await Promise.reject('出错了');
  } catch(e) {
  }
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// hello world
//方法二
async function f() {
  await Promise.reject('出错了')
    .catch(e => console.log(e));
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// 出错了
// hello world
//promise自己的catch捕捉自己的rejected状态，不影响async函数返回的promise状态
```
4. 多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发
```
//只有getFoo完成以后，才会执行getBar，比较耗时
let foo = await getFoo();
let bar = await getBar();

//同时触发，比较高效
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);
// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```

### 四，Class
ES6引入Class语法，使得前端编程更加面向对象化。
```
//ES5创建一个对象实例
function Point(x, y) {
  this.x = x;
  this.y = y;
}
Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};
var p = new Point(1, 2);

//对应的ES6语法
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
typeof Point // "function"
Point === Point.prototype.constructor // true
```
##### constructor方法
相当于ES5里的构造函数，类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。==constructor默认返回实例对象（this）。constructor中调用super()执行父类的构造函数。==

##### 取值函数（getter）和存值函数（setter）
```
class CustomHTMLElement {
  constructor(element) {
    this.element = element;
  }

  get html() {
    return this.element.innerHTML;
  }

  set html(value) {
    this.element.innerHTML = value;
  }
}
```
存值函数和取值函数是定义在属性的描述对象上面
```
var descriptor = Object.getOwnPropertyDescriptor(
  CustomHTMLElement.prototype, "html"
);
```

#### 实例属性 & 实例方法
#####  实例属性
constructor函数中，定义在this（当前实例对象）上的属性和定义在类的最顶层的属性。一般不建议把实例属性定义在类的最顶层。
##### 实例方法
Class的实例方法都定义在Class的原型对象prototype上，被所有实例对象继承（共享）。
```
class T {
  age = 18;    
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}
//name和age都是实例属性，sayName是实例方法
```
#### 静态属性 & 静态方法
##### 静态属性
静态属性指的是 Class本身的属性，即Class.propName，而不是定义在实例对象（this）上的属性。
1. 在class外通过类名定义；
2. 在class内用static标识。
##### 静态方法
class中用static关键字修饰的实例方法叫静态方法。静态方法定义在类上面，而不是类的原型对象上，不能被实例对象继承，所以静态方法不能通过实例对象调用，而应该通过类（类名.静态方法）调用。
注意：
1. 静态方法中的this指向的是类本身，而不是类的实例对象；
2. 静态方法可以被子类继承。

```
class Foo {
  static tom = 'tom'; //新提案
  static bar() {
    this.baz();
  }
  static baz() {
    console.log('hello');
  }
  baz() {
    console.log('world');
  }
}

Foo.bar() // hello
Foo.prop = 1;
Foo.prop // 1

let foo = new Foo();
foo.prop //undefined

//static bar和static baz是静态方法，baz是实例方法
//prop和tom是静态属性，只能通过类名调用，无法通过实例对象调用
```

#### 私有属性 & 私有方法
##### 私有属性
只能在类的内部访问的属性，不能在类外部访问。属性名前加“#”。
##### 私有方法
只能在类的内部访问的方法，不能在类外部访问。
1. 在命名上加以区别，方法前加“_”；（主要）
2. 利用Symbol值的唯一性，将私有方法的名字命名为一个Symbol值；
3. 在方法前加“#”。（提案）
```
const bar = Symbol('bar');
const snaf = Symbol('snaf');
class Widget {
  //私有属性
  #count = 0;
  // 公有方法
  foo (baz) {
    this._bar(baz);
  }
  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }
  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }
  // 私有方法
  #sum() {
    return this.#count;
  }
  // ...
}
```
#### 类的继承
与Java和C/C++等语言类似，通过extends关键词实现类的继承。
1. 类必须先在constructor方法中调用super方法，父类的构造函数完成对自己的this对象塑造，得到与父类同样的==实例属性和方法==，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象；
2. 在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。==实际开发中，在子类的构造函数第一行都是super()==；
```
class Point { /* ... */ }

class ColorPoint extends Point {
  constructor() {
  }
}

let cp = new ColorPoint(); 
```
3. 如果子类没有显示定义的构造函数，将会有一个默认的构造函数，并且在默认的构造函数中调用super()方法；
```
class ColorPoint extends Point {
}

// 等同于
class ColorPoint extends Point {
  constructor(...args) {
    super(...args);
  }
}
```
##### super关键词
1. 当作函数使用时，代表父类的构造函数，返回的是子类的实例,super()内部的this指向的是子类。ES6要求，子类的构造函数必须执行一次super函数，且只能在子类的构造函数中调用，其他地方调用报错；
```
super()
//相当于
父类.prototype.constructor.call(this)
```
2. super作为对象时，在普通方法中，指向父类的==原型对象（注：不是实例对象，无法访问定义在实例对象上的属性和方法）==；在静态方法中，指向父类
```
class A {
  constructor() {
    this.p = 2;
  }
}

class B extends A {
  get m() {
    return super.p;
  }
}

let b = new B();
b.m // undefined，p定义在父类的实例对象上，无法访问

class A {}
A.prototype.x = 2;

class B extends A {
  constructor() {
    super();
    console.log(super.x) // 2
  }
}
//x定义在父类的原型对象上，可以访问
```
3. 在子类普通方法中通过super调用父类的方法时，方法内部的this指向当前的子类实例
4. 在子类的静态方法中通过super调用父类的方法时，方法内部的this指向当前的子类，而不是子类的实例

##### 类的 prototype 属性和__proto__属性
1. 子类的__proto__属性，表示构造函数的继承，总是指向父类；
2. 子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性
```
class A {
}

class B extends A {
}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```


### 五，前端模块化
#### AMD&CommonJs
ES6的module出现之前，各语言模块加载工具
```
Ruby：require
Python：import
CSS：@import
javascript：CommonJs&AMD
```
##### CommonJs（用于Node环境）
三要素：module（模块标识），exports（模块定义），require（模块引用）

```
// module.js
let name = 'liakng xie';
let sayName = function () {
  console.log(name);
};

module.exports = { name, sayName }
// 或者
exports.sayName = sayName;

//module.exports === exports
```
module.export跟exports的区别
1. module.exports 方法还可以单独返回一个数据类型(String、Number、Object...)，而 exports 只能返回一个 Object 对象
2. 所有的 exports 对象最终都是通过 module.exports 传递执行，因此可以更确切地说，exports 是给 module.exports 添加属性和方法

##### AMD（Asynchronous Module Definition：异步模块定义，用于浏览器环境）
通过define方法定义模块，require方法加载模块，[requireJs](https://requirejs.org/)实现的就是AMD规范

```
//模块定义
define(['module'], function() {
  let name = 'likang xie';

  function sayName() {
    console.log(name);
  }
  
  return { sayName }
});


// 通过 require 引入依赖
require(['module'], function(mod) {
   mod.sayName(); // likang xie
})
```

#### ES6
##### export
export命令规定的是对外的接口（广义上，变量也是一个接口），必须与模块内部的变量建立一一对应关系

```
//变量和常量
export var firstName = 'Michael';
export const firstName = 'Michael';
//等价于
var year = 1958;
export { firstName, lastName, year };


//函数
export function multiply(x, y) {
  return x * y;
};
export class multiply{
    constructor(){
        ...
    }
}
//等价于
function multiply(x, y) {
  return x * y;
}
class multiplyClass{
    constructor(){
        ...
    }
}
export {multiply, multiplyClass}
```
##### export default
export default命令对外提供的是一个匿名接口，模块内不需要为匿名接口命名，即使在模块内命名，在模块外部也是无效的

```
//导出一个匿名接口
export default function () {
  console.log('foo');
}

//即使内部给匿名接口命名，模块外部也无效，与上面写法等价
export default function foo() {
  console.log('foo');
}

// 或者写成

function foo() {
  console.log('foo');
}

export default foo;
```

export default的本质是输出一个叫做default的变量或方法，然后系统允许你为它取任意名字

```
//module
function add(x, y) {
  return x * y;
}
export {add as default};
// 等同于
// export default add;

// app.js
import { default as foo } from 'modules';
```
==所以一个模块中只能有一个默认导出（不允许多个接口导出命名成default），且必须写在模块最后==
##### import
（1）对应export导出的接口
```
//加载指定接口，需要{}包裹
import { area, circumference } from './circle';
//整体加载
import * as circle from './circle';
```
（2）对应export default导出的默认接口

```
//加载默认接口，不需要{}包裹
import area from './circle';
//或者
import area as area11 from './circle';
```
注意：

==1. import必须在模块的头部；==

==2. 不能直接修改接口对象，可以修改接口对象属性，一般不建议修改，保持只读；==

==3. import不能出现在作用域内==
##### import 与 export 复合

```
export { foo, bar } from 'my_module';

// 可以简单理解为
import { foo, bar } from 'my_module';
export { foo, bar };
```
#### ES6模块与CommonJs的区别
1. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用，ES6 模块不会缓存运行结果，而是动态地去被加载的模块取值，并且变量总是绑定其所在的模块；
2. CommonJS 模块是运行时加载，ES6 模块是编译时输出接口；
#### ES6模块与CommonJs模块相互加载
1. node 会自动将module.exports属性当作模块的默认输出，即等同于export default xxx
2. CommonJS 模块加载 ES6 模块，不能使用require命令，而要使用import()函数

