## npm
官网[https://www.npmjs.com/]
### 常用命令
```
## 1，初始化npm包
$ npm init

## 2，安装node依赖
$ npm install
//简写：npm i

## 3，安装插件
$ npm install 包名@版本号 [-g/--save/--dev/--save-dev]
//-g表示全局安装，默认安装在当前目录
//--save表示安装在生产环境（product）
//--dev表示安装在开发环境（development）

## 4，更新插件
$ npm update [包名] [-g]
//包名省略时，更新package.json中的所有依赖包

## 5，删除插件
$ npm uninstall 包名

## 6，更新npm版本
$ npm install npm -g
```
[更多查看npm命令文档](https://docs.npmjs.com/cli-documentation/)
### 发布包
#### 1，注册npm账号
npm官网注册账号，输入username，password，email，后面登录需要这些信息
#### 2，登录npm账号
```
$ npm login
//依次输入注册时的username，password，email

$ npm whoami
//检测是否已登录
```
#### 3，初始化项目信息
```
## 进入项目目录
$ npm init
## 依次按提示填入包名、版本、描述、github地址、关键字、license等
```
#### 4，发布
```
$ npm publish
## 控制台出现 “+包名@版本号” 表示发布成功
```
常见报错：
1. errno -4048，syscall unlink：npm未登录，需要npm login；
2. no_perms Private mode enable, only admin can publish this module：当前不是原始镜像，可能用的是其他镜像，如淘宝镜像。要切换回原始的npm镜像，命令：npm config set registry https://registry.npmjs.org

#### 5，更新包
```
$ npm version patch
## 该命令在原来的版本上自动加1,实际上是将package.json文件中的version值修改了

$ npm publish
## 重新发布
## 控制台依然可以看到 “+包名@版本号” 信息，并且可以在包的version中看到两个版本
```
#### 6，删除包
```
$ npm unpublish 包名@版本号
## 控制台出现 “-包名@版本号” 表示删除成功

$ npm unpublish 包名 --force
## 删除包的所有版本
```
## cnpm
淘宝镜像，类似CDN。淘宝镜像采用的策略是每隔10分钟同步npm上的包。用法与npm一致，下载速度比npm快，但不能保证版本号稳定性
```
## 安装淘宝镜像
$ npm install -g cnpm --registry=https://registry.npm.taobao.org

## 切回原始npm镜像
$ npm config set registry https://registry.npmjs.org
```
## yarn
官网[https://yarn.bootcss.com](https://yarn.bootcss.com/)
### 常用命令
```
## 1，初始化项目
$ yarn init

## 2，安装node依赖
$ yarn install
//简写：yarn

## 3，安装插件
$ yarn add 包名@版本号 [--dev/--peer/--optional]
//注意：yarn全局安装global必须紧跟yarn：yarn global add 包名@版本号

## 4，更新插件
$ yarn upgrade 包名@版本号

## 5，删除插件
$ yarn remove 包名
```
[更多查看npm命令文档](https://yarn.bootcss.com/docs/cli/)
