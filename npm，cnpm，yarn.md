# Sass部分
### 一，介绍
Sass是一款强化 CSS 的辅助工具，在 CSS 语法的基础上增加了变量 (variables)、嵌套 (nested rules)、混合 (mixins)、导入 (inline imports) 等高级功能。Sass 有两种语法格式：
1. SCSS (Sassy CSS)，仅在 CSS3 语法的基础上进行拓展，与CSS语法一致，文件后缀为.scss
2. Sass 语法，缩进格式语法，即用 “缩进” 代替 “花括号” 表示属性属于某个选择器，用 “换行” 代替 “分号” 分隔属性，sass语法里不存在花括号和分号，文件后缀为.sass

sass文件和scss文件之间可以相互转化

```
# Convert Sass to SCSS
$ sass-convert style.sass style.scss

# Convert SCSS to Sass
$ sass-convert style.scss style.sass
```



### 二，变量与引用
##### 1，变量声明与引用
sass使用$符号来标识变量，例：

```
##声明
$highlight-color: #F90;
##引用
.selected {
  border: 1px solid $highlight-color;
}

##变量声明中的中划线和下划线相互兼容，即用中划线声明的变量可以使用下划线的方式引用，反之亦然
```
sass变量有作用域，块作用域外定义的变量，相当于全局变量，可以在sass文件任何地方使用，{...}块作用域内定义的变量，只在当前作用域内有效

##### 2，变量默认值

```
##如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值，例
$fancybox-width: 400px !default;
```
### 三，嵌套css规则
##### 1，css嵌套
sass解决了重复写多重选择器的问题，采用class类层级嵌套，例：

```
.content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}

##相当于
.content article h1 { color: #333 }
.content article p { margin-bottom: 1.4em }
.content aside { background-color: #EEE }
```
##### 2，父选择器引用标识‘&’
'&'在作用域内指示父选择器，例：

```
article a {
  color: blue;
  &:hover { color: red }
}
## '&'相当于article a
```
##### 3，群组嵌套及同层选择器嵌套

```
##群组嵌套
tr {
    dt { color: #333 }
    dd { color: #555 }
}
```

```
##同层选择器嵌套
article {
  ~ article { border-top: 1px dashed #ccc }
  > section { background: #eee } 
  + div { margin-top: 0 }
}

##相当于
article ~ article { border-top: 1px dashed #ccc }
article > section { background: #eee }
article + div { margin-top: 0 }
```
##### 4，嵌套属性

```
border-style 
border-width 
border-color
##可以写成
border: {
  style: solid;
  width: 1px;
  color: #ccc;
}
```
## 四，sass文件导入
在css文件中通过@import引入其他css文件，只有执行到@import时，浏览器才会去下载引入的css文件，从而导致页面加载起来特别慢。

而sass采用的是css预加载机制，所有的sass文件最终都会被编译到一个css文件中，所以在sass文件中通过@import引入的其他sass文件在编译时即被加载，而无需发起额外的下载请求，更高效。
1. 局部sass文件导入

```
##文件名以下划线开头的sass文件为局部文件，编译时不会生成单独的css文件，例
@import "themes/_night-sky.scss";
```

2. 嵌套导入

```
.blue-theme {
    @import "blue-theme"
}
```
## 五，混合器
由于变量只能实现单个样式的定义，当出现大量样式复用的场景时，定义变量不是一个很好的选择，混合器就可以实现多个样式复用。混合器使用@mixin标识符定义，给一大段样式赋予一个名字，然后就可以@include引用这个名字重用这段样式代码。

```
##定义混合器
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
##引用混合器
.notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}
##最终编译成CSS代码
.notice {
  background-color: green;
  border: 2px solid #00aa00;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}

```
##### 1，混合器应用场景
有特殊意义的css样式，例如圆角，文字溢出等，采用混合器定义一个特殊的名字
##### 2，混合器传参
混合器传参类似JavaScript中的function传参

```
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
##引用时传参
a {
  @include link-colors(blue, red, green);
}
##或者
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
  );
}
```
##### 3，混合器默认参数

```
@mixin link-colors(
    $normal,
    $hover: $normal,
    $visited: $normal
  )
{
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}

@include link-colors(red)
##$normal，$hover，$visited都会被赋值成red
```
## 六，选择器继承

```
.error {
  border: 1px solid red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}

##解析后相当于在应用seriousError class的html元素上添加error类

##继承与混合器的区别：混合器可能会导致css样式元素重复，而继承不是简单滴css样式元素合并，不会导致css样式元素重复，css样式元素选择根据css样式优先级
```
==切记：不要在css规则中使用后代选择器（比如.foo .bar）去继承css规则==
如：
```
.foo .bar { @extend .baz; }
.bip .baz { a: b; }
```

# Less部分
### 一，安装
Less也是一门CSS预处理语言，可运行在Node环境和浏览器环境
```
##全局安装Less
npm install -g less
##把Less文件编译成css文件
$ lessc styles.less styles.css
```
### 二，变量
与Sass定义变量的方式不同，Sass用‘$’,Less用‘@’
1. 普通css样式变量
```
##Less
@width: 10px;
@height: @width + 10px;
#header {
  width: @width;
  height: @height;
}
##编译为
#header {
  width: 10px;
  height: 20px;
}

##
```
2. 选择器变量

```
@my-selector: banner;
.@{my-selector} {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}
##编译为
.banner {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}
##相当于@{}取变量值
```
3. 路径变量（import和url）

```
##路径
@images: "../img";
body {
  color: #444;
  background: url("@{images}/white-sand.png");
}
##编译为
body {
  color: #444;
  background: url("../img/white-sand.png");
}

##import
@themes: "../../src/themes";
@import "@{themes}/tidal-wave.less";
##编译为
@import "../../src/themes/tidal-wave.less";
```
4. 懒加载（变量提前）

```
.lazy-eval {
  width: @var;
}

@var: @a;
@a: 9%;
##编译为
.lazy-eval {
  width: 9%;
}
```
### 3，嵌套
Less的嵌套与Sass嵌套语法类似，不再赘述
### 4，混合器
与Sass混合器不同的是，Less的混合器没有@mixin和@include，而是采用类似函数执行的方式
```
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
#menu a {
  color: #111;
  .bordered();    //id则对应#bordered()
}
##编译为
#menu a {
  color: #111;
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
```

## 总结
1. 变量：

```
sass：$定义；#{$变量} 插值

less：@定义；#{变量} 插值
```

2. 混入：

```
sass：@mixin定义 混入字段、@include调用 混入字段

less：选择器后 直接加（），（）中传参 即可
```

相对来说，Sass更成熟，开发中首选Sass