## [redux-saga](https://redux-saga-in-chinese.js.org) 
### 介绍
redux-saga是一个redux中间件，用于处理redux的Effect副作用（异步获取数据，访问浏览器缓存等）。利用ES6的Generator函数，实现异步操作的同步编程，使业务逻辑结构更加清晰

### 基本概念
#### Effects
1. call: 用于调用异步函数,返回一条描述异步函数调用信息,配合yield调用异步函数并返回函数结果
```
import { call } from 'redux-saga/effects'
function* fetchProducts() {
  const products = yield call(Api.fetch, '/products')
  // ...
}
```
2. apply: 与call函数作用一样,调用方式同js的apply函数
```
yield apply(obj, obj.method, [arg1, arg2, ...])
```
3. cps: 用来处理 Node 风格的函数,如fn(...args, callback),回调形式,cps 表示的是延续传递风格.调用方式与call一致
4. takeEvery: 允许多个函数同时调用,即使上一个异步操作还为完成
```
import { takeEvery } from 'redux-saga'

function* watchFetchData() {
  yield takeEvery('FETCH_REQUESTED', fetchData)
}
//只要触发FETCH_REQUESTED action, fetchData就会被调用,即使上一次调用还未返回
```
5. takeLatest: 任何时刻只允许一个任务在执行, 且永远执行最后一个任务, 如果上一个任务未执行完成, 被自动取消
```
import { takeLatest } from 'redux-saga'

function* watchFetchData() {
  yield takeLatest('FETCH_REQUESTED', fetchData)
}
```
6. take: takeEvery和takeLatest都是先定义action和任务的对应关系,在每个action到来时执行对应的任务.而take采用的action监听方式,在action未到来之前一直阻塞,直到触发监听的action才继续往下执行,常用于通过控制action流来梳理异步业务逻辑关系.例如用户注册,登陆和退出
```
function* loginFlow() {
  while (true) {
    yield take('LOGIN')
    // ... perform the login logic
    yield take('LOGOUT')
    // ... perform the logout logic
  }
}
```
7. fork: 原理与call和apply一样,但是call和apply是会阻塞的Effect,fork不会阻塞当前执行内容,可以继续往下执行,且fork返回一个task对象,可以被取消
```
import { fork, call, take, put } from 'redux-saga/effects'
import Api from '...'

function* authorize(user, password) {
  try {
    const token = yield call(Api.authorize, user, password)
    yield put({type: 'LOGIN_SUCCESS', token})
  } catch(error) {
    yield put({type: 'LOGIN_ERROR', error})
  }
}

function* loginFlow() {
  while(true) {
    const {user, password} = yield take('LOGIN_REQUEST')
    yield fork(authorize, user, password);
    //如果此处用call执行任务,在authorize执行未返回之前,程序都会被阻塞在这里,因而无法监听到后面的LOGOUT action,会导致UI和应用状态不一致的问题
    yield take(['LOGOUT', 'LOGIN_ERROR'])
    yield call(Api.clearItem('token'))
  }
}
```
8. cancel: 用于手动终止指定的task
```
if(action.type === 'LOGOUT')
      yield cancel(task)
```
9. cancelled: 用于判断是否手动终止了task,并在终止后编写相应业务逻辑.常在异步操作的Promise的finally区块中使用
```
finally {
    if (yield cancelled()) {
      // ... put special cancellation handling code here
    }
```
10. put: 同dispatch函数功能一样,用于发布一个同步action
```
import { call, put } from 'redux-saga/effects'
function* fetchProducts() {
  const products = yield call(Api.fetch, '/products')
  // 创建并 yield 一个 dispatch Effect
  yield put({ type: 'PRODUCTS_RECEIVED', products })
}
```
11. race: 与Promise的race一样,只要有一个Promise状态发生改变,立即终止其他任务.常用于对一些接口访问进行timeout限制
```
import { race, take, call } from 'redux-saga/effects'

function* backgroundTask() {
  while (true) { ... }
}

function* watchStartBackgroundTask() {
  while (true) {
    yield take('START_BACKGROUND_TASK')
    yield race({
      task: call(backgroundTask),
      cancel: take('CANCEL_TASK')
    })
  }
}
```


==注: call和apply一般用于处理返回Promise结果的函数==
### 使用
#### 安装
```
$ npm install --save redux-saga
##或者
$ yarn add redux-saga
```
#### 创建sagas
src/sagas目录下创建saga.js
```
import { call, put, takeEvery } from 'redux-saga/effects'

function* fetchData(action) {
   try {
      const data = yield call(Api.fetchUser, action.payload.url);
      yield put({type: "FETCH_SUCCEEDED", data});
   } catch (error) {
      yield put({type: "FETCH_FAILED", error});
   }
   //此处的put相当于dispatch，都是发布一个同步action
}

export default function* () {
  yield takeEvery('FETCH_REQUESTED', fetchData)
  //每次触发同步FETCH_REQUESTED action时，执行fetchData函数，且允许多个fetchData函数同步执行
}
//如果有多个 Saga 监视不同的 action，可以用辅助函数（takeEvery，takeLatest）创建多个观察者，后台启动多个 saga 的 Effect
```
src/sagas目录下创建index.js
```
import watchFetchData from './saga';

export default function* rootSaga() {
  yield all([
    watchFetchData(),
    ...
  ])
}
```
#### 注册sagas
在应用入口文件src/index.js文件中注册sagas
```
import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'
import sagas from './sagas'

const rootSaga = createSagaMiddleware(sagas);

const store = createStore(
  reducer,
  applyMiddleware(rootSaga)
)
```
### 实战技巧

## [redux-thunk](https://github.com/reduxjs/redux-thunk)
### 介绍
### 基本介绍
### 使用
### 实战技巧
