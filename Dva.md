### [dva](https://dvajs.com/guide/getting-started.html)
#### 一，创建新应用
##### 1，安装dva-cli
```
$ npm install dva-cli -g
$ dva -v
dva-cli version 0.9.1
```
##### 2，新建应用
```
$ dva new dva-demo

$ cd dva-demo
$ npm start

Compiled successfully!

The app is running at:

  http://localhost:8000/

Note that the development build is not optimized.
To create a production build, use npm run build.
```
##### 3，应用目录结构
重点记录dva架构新的目录
- mock：模拟服务端接口数据
- src/models：数据处理模块
- src/routes：页面模块，相当于pages，每个文件表示一个页面
- src/services：API请求交互模块
- src/router.js：路由配置文件
- src/index.js：dva入口文件
- .webpackrc：拓展webpack配置，如antd的按需加载
```
//安装antd
$ npm install antd babel-plugin-import --save
//.webpackrc文件
{
    "extraBabelPlugins": [
        ["import", { "libraryName": "antd", "libraryDirectory": "es", "style": "css" }]
    ]
}
```
#### [dva工作流程](https://dvajs.com/guide/getting-started.html)
dva之前，react开发架构：
- 路由：react-router
- 状态管理：[redux](https://www.redux.org.cn/)
- 异步状态（副作用）：[redux-saga](https://redux-saga-in-chinese.js.org/)

dva = React-Router + Redux + Redux-saga，并对这三个工具包进行了封装，提供极少的几个API进行操作
##### 入口（src/index.js）
初始化dva，并加载插件，models状态管理，router路由，然后挂载到#root节点
```
import dva from 'dva';
import createHistory from 'history/createBrowserHistory';
import './index.css';

// 1. Initialize
// const app = dva();
const app = dva({
    history: createHistory(),
    initialState: {
        products: [
           { name: 'dva', id: 1 },
           { name: 'antd', id: 2 },
           { name: 'umi', id: 3}
         ]
    },
 });

// 2. Plugins
// app.use({});

// 3. Model
app.model(require('./models/products').default);

// 4. Router
app.router(require('./router').default);

// 5. Start
app.start('#root');
```
##### 状态管理（src/models目录）

```
export default {
    namespace: 'products',
    state: [
      { name: 'dva', id: 1 },
      { name: 'antd', id: 2 }
    ],
    reducers: {
      delete(state, { payload: id }) {
        return state.filter(item => item.id !== id);
      }
    },
    effects: {

    },
    subscriptions{
        
    }
};
```
- namespace：model命名空间，也是其在全局state上的属性，只能是字符串；
- state：model初始值，但是其优先级低于dva初始化的initialState，类型是array；
- reducers：model的reducer，与redux的reducer一样。类型是object；
- effects：处理异步action，相当于redux-saga。每一个effects都是Generator函数；
- subscriptions：监听数据变化，类似于redux的subscribe订阅或者vue中的watch，但不局限于store中的数据，可以是当前的时间、服务器的 websocket 连接、keyboard 输入、geolocation 变化、history 路由变化等等。

##### 状态与组件连接
```
import React from 'react';
import { connect } from 'dva';
import ProductList from '../components/ProductList';
const Products = ({dispatch, products}) => {
    const handleDelete = function(id) {
        dispatch({
            type: 'products/delete',
            payload: id
        });
    };
    return (
        <div>
            <h2>List of Products</h2>
            <ProductList onDelete={handleDelete} products={products} />
        </div>
    )
};

export default connect(({ products }) => ({
    products,
  }))(Products);
```
dva的connect与redux的connect一致，都是连接组件与store（model），向组件注入state状态和dispatch方法，返回一个容器组件。
##### 路由配置（src/router.js）
```
import React from 'react';
import { Router, Route, Switch } from 'dva/router';
import IndexPage from './routes/IndexPage';
import Products from './routes/Products';

function RouterConfig({ history }) {
  return (
    <Router history={history}>
      <Switch>
        <Route path="/" exact component={IndexPage} />
        <Route path="/products" exact component={Products} />
      </Switch>
    </Router>
  );
}

export default RouterConfig;

```
dva的路由配置与一般的react应用路由配置并无区别，dva的初衷也不是解决路由配置，而是应用的状态管理。所以dva常与umi配合使用。

#### [API介绍](https://dvajs.com/api)
##### 主要的6个API
###### 1，dva()
```
const app = dva(opts);
```
常用参数：
1. history：指定应用路由方式，browserHistory或者hashHistory，默认是hashHistory
```
import createHistory from 'history/createBrowserHistory';
const app = dva({
  history: createHistory(),
});
```
2. initialState：初始化全局state，优先级最高
```
initialState: {
    products: [
        { name: 'dva', id: 1 },
        { name: 'antd', id: 2 },
        { name: 'umi', id: 3}
    ]
}
```
3. onAction：dispatch派发action时回调，一般用于日志打印
```
import createLogger from 'redux-logger';
const app = dva({
  onAction: createLogger(opts),
});
```
4. onStateChange：state状态改变时回调，一般用于同步 state 到 localStorage

###### 2，app.model(model)
注册model到dva
###### 3，app.router()
注册路由表到dva
```
import { Router, Route } from 'dva/router';
app.router(({ history }) => {
  return (
    <Router history={history}>
      <Route path="/" component={App} />
    </Router>
  );
});
```
###### 4，app.start()
挂载应用并启动，参数是selector元素选择器，如“#root”
###### 5，app.unmodel(namespace)
注销model，参数是model的namespace。且必须在model的subscription中返回 unlisten 函数，否则报警告
###### 6，app.replaceModel(model)
替换model，旧model的 reducers，effects，subscriptions都会被清除，state会被保留。如果不存在相同namespace的model，那么执行app.model操作。在app.start之后执行，与app.unmodel一样，必须在model的subscription中返回 unlisten 函数
##### dva输出文件
###### 1，dva/router
默认输出 react-router 接口
###### 2，dva/fetch
dva还集成了fetch，不过也可以使用其他请求库，如axios
###### 3，dva/saga
输出 redux-saga 的接口，主要用于用例的编写
###### 4，dva/dynamic
解决组件动态加载问题的 util 方法
```
import dynamic from 'dva/dynamic';

const UserPageComponent = dynamic({
  app,
  models: () => [
    import('./models/users'),
  ],
  component: () => import('./routes/UserPage'),
});
```
