## 一，概念
本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency-graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle

## 二，安装
#### 1，本地安装
一般建议本地安装，这样可以根据项目需要单独更新
```
npm install --save-dev webpack@<version>
##@符号指定安装版本

##如果使用 webpack 4+ 版本，还需要安装 CLI
npm install --save-dev webpack-cli
```
#### 2，全局安装

```
npm install --global webpack@<version>
## OR
yarn global add webpack@<version>
```
#### 3，运行
webpack 通过运行一个或多个 npm scripts，会在本地 node_modules 目录中查找安装的 webpack

```
##package.json文件
"scripts": {
    "start": "webpack --config webpack.config.js"
}
```


## 三，基本配置
#### 1，entry(入口)
指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的，一并打包处理输出到称之为 bundles 的文件中。
通过entry属性可以指定一个或者多个入口模块。

```
##单入口
const config = {
  entry: './src/app.js'
};

##OR 对象写法
const config = {
  entry: {
      app: './src/app.js'
  }
};
module.exports = config;
```

```
##多入口（常用于多页面应用）
const config = {
  entry: [
    './src/pageOne/index.js',
    './src/pageTwo/index.js',
    './src/pageThree/index.js'
  ]
};

##OR 对象写法
const config = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
module.exports = config;
```

#### 2，output(出口)
output 属性告诉 webpack 在哪里输出它所创建的 bundles，以及如何命名这些文件，默认值为 ./dist。

```
const path = require('path');
##path是一个 Node.js 核心模块，用于操作文件路径

const config = {
    entry: {
        app: './src/app.js'
    }
    output: {
        filename: '[name].js',
        path: path.resolve(__dirname, '../dist')
    }
};
##filename 用于输出文件的文件名，[name].js支持多入口输出，占位符(substitutions)来确保每个文件具有唯一的名称
##目标输出目录
##__dirname表示配置文件所在当前目录，path.resolve(__dirname, '../dist')表示定位到与配置文件父目录平行的dist目录
module.exports = config;
```
==**注：配置可以存在多个入口起点，但只能指定一个输出配置**==

#### 3，loader
loader 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。webpack loader 将所有类型的文件，转换为应用程序的依赖图（和最终的 bundle）可以直接引用的模块。
使用loader前先安装对应的loader：

```
##vue loader
npm install --dev vue-loader
##css loader
npm install --save-dev css-loader
##TypeScript loader
npm install --save-dev ts-loader
##babel loader 编译ES6
npm install --save-dev babel-loader
##url loader 处理图片，视频/音频，字体等文件
npm install --save-dev url-loader

##更多loader参考webpack官网loader
```

```
const path = require('path');

const config = {
  module: {
    rules: [
      { 
        test: /\.txt$/,
        loader: 'raw-loader' 
      }
    ]
  }
};

##test表示文件匹配规则
##loader表示转换时，应该使用的loader
module.exports = config;
```

#### 4，plugins(插件)
插件可以被看作是loader的扩展，解决loader解决不了的问题。

使用：1，首先在配置文件顶部require()引入
2，new一个插件实例并添加到配置对象的plugins数组中

```
const HtmlWebpackPlugin = require('html-webpack-plugin'); //通过 npm 安装
const webpack = require('webpack'); //访问内置的插件

const config = {
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

#### 5，mode(模式)
设置mode参数来配置webpack的模式，development(开发环境) 或 production(生产环境)

```
module.exports = {
  mode: 'production'
};

##或者从 CLI 参数中传递
$ webpack --mode=production

##mode设置成production时，会将 process.env.NODE_ENV 的值设为 production
##mode设置成development时，会将 process.env.NODE_ENV 的值设为 development
```
## 四，其他配置
#### 1，resolve(模块解析)
resolver 是一个库(library)，用于帮助找到模块的绝对路径。当打包模块时，webpack 使用 enhanced-resolve 来解析文件路径。

```
const config = {
  resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
      'api': resolve('src/api'),
      'config': resolve('src/config'),
      'common': resolve('src/components/common'),
    }
  }
};

module.exports = config;
```

#### 2，target(构建目标)
webpack支持多种构建编译，例如node环境和web环境分别打包

```
var path = require('path');
var serverConfig = {
  target: 'node',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.node.js'
  }
  //…
};

var clientConfig = {
  target: 'web', // <=== 默认是 'web'，可省略
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.js'
  }
  //…
};

module.exports = [ serverConfig, clientConfig ];

##将在你的 dist 文件夹下创建 lib.js 和 lib.node.js 文件
```
## 五，使用webpack进行代码分割
## 六，webpack插件开发


