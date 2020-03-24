### Express搭建web服务器
1. 安装express和express应用生成器
```
$ mkdir webServer
$ cd webServer
$ cnpm install express --save
$ cnpm install express-generator -g
```
2. 初始化项目
```
$ express -e server
//-e 表示采用ejs模板引擎
```
3. 安装依赖并启动服务
```
$ cnpm install
$ npm start
```
至此，一个简单的web服务器搭建完成．打开浏览器访问http://localhost:3000即可以看到express欢迎页面
#### node热部署
实现node热部署的方法有很多,常见的有supervisor,nodemon,hotnode等,使用方法也都差不多.推荐使用supervisor,比较快
```
$ npm install -g supervisor
$ supervisor server.js
##或者修改node项目中package.json文件中的node启动项
```

### Koa搭建web服务器
1. 初始化项目
```
$ mkdir nodeServer
$ cd nodeServer
$ npm init
```
2. 安装koa和相关插件
```
$ cnpm install koa --save
$ cnpm install koa-body --save
$ cnpm install koa-static --save
$ cnpm install koa-router --save
$ cnpm install koa-cors --save
```
3. 创建服务
```
##server.js文件
const Koa = require("koa");
const koaBody = require("koa-body");
const static = require('koa-static');
const cors = require('koa-cors');

const app = new Koa();

##解析post请求体
app.use(koaBody());

##静态文件处理
app.use(static(path.join(__dirname)))

##设置跨域
app.use(async (ctx, next) => {
  ctx.set("Access-Control-Allow-Origin", "*");
  return next();
});
##或者使用插件
// app.use(cors());

##监听3001端口
app.listen(3001, () => {
  console.log("server run on 127.0.0.1:3001");
});
```
4. 修改项目package.json文件
```
"scripts": {
    "serve":"node app.js"
  }
##可以用node热部署插件实现热部署
```
