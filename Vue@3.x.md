### 一，安装

```
cnpm install -g @vue/cli
# OR
yarn global add @vue/cli
```
如果电脑上已安装了旧版本（version<3.x）vue，需先卸载旧版本，再安装新版本（version>=3.x）

```
cnpm uninstall vue-cli -g
# OR
yarn global remove vue-cli

#查看vue版本
vue --version
```
### 二，创建项目
##### 1，初始化项目

```
vue create <appName>
##例：vue create testApp
```
你会被提示选取一个 preset。你可以选默认的包含了基本的 Babel + ESLint 设置的 preset，也可以选“手动选择特性”来选取需要的特性

```
##手动选择preset
Router
Vuex    //根据项目需求
CSS Pre-processors   //CSS预编译工具，可选择scss或者less
Linter / Formatter   //代码检测和规范工具，选择ESLint
```
##### 2，手动创建项目配置文件
vue-cli3类似create-react-app脚手架，对webpack配置进行了高度封装，需要创建自定义的项目配置文件。

```
##在项目根目录创建vue.config.js文件，常用配置如下
module.exports = {
  baseUrl: '/',
  outputDir: 'dist', // 打包的目录
  lintOnSave: true, // 在保存时校验格式
  productionSourceMap: false, // 生产环境是否生成 SourceMap
  devServer: {
    open: true, // 启动服务后是否打开浏览器
    host: 'localhost',
    port: 8080, // 服务端口
    https: false,
    hotOnly: false,
    proxy: {
        
    }, // 设置代理
    before: app => {}
  },
}
```
##### 3，环境变量
在项目根目录创建以下文件
```
.env
##保存公用参数，可以在所有模式中获取到

.env.developemnt
##只在 development 模式下生效

.env.production
##只在 production 模式下生效

.env.test
##只在 test 模式下生效

##然后在 package.json 的 scripts 命令中添加对应的 mode
"scripts": {
    "serve": "vue-cli-service serve --mode development",
    "build": "vue-cli-service build --mode production",
    "lint": "vue-cli-service lint --mode test"
}
```
