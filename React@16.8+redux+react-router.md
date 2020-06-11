## 搭建项目
### 一，create-react-app脚手架搭建项目
##### 1，安装webpack

```
$ cnpm install webpack -g
##查看webpack版本
$ webpack -v   
```
##### 2，安装create-react-app

```
$ cnpm install create-react-app -g
##查看create-react-app版本
$ create-react-app -v
```
##### 3，创建项目

```
$ mkdir react-workspace
$ cd react-workspace
$ create-react-app <appname>
$ cd appname
$ cnpm install
$ cnpm start

##[查看效果](https://localhost:3000)
```
### 二，修改配置
##### 1，修改create-react-app默认配置

```
##弹射（暴露）create-react-app对webpack的配置封装，不可逆
$ cnpm run eject
```
##### 2，覆盖默认配置
```
##1，安装react-app-rewired
$ yarn add react-app-rewired customize-cra --dev
//react-app-rewired2.x以后，不再支持injectBabelPlugin的方式，需要安装customize-cra

##2，在项目根目录中创建config-overrides.js文件用于修改默认配置
/* config-overrides.js */
const { 
    override, 
    fixBabelImports, 
    addLessLoader,
    addDecoratorsLegacy, 
    addWebpackAlias
} = require('customize-cra');
const path = require('path');

const rewiredMap = () => config => {
    config.devtool = config.mode === 'development' ? 'cheap-module-source-map' : false;
    return config;
};

module.exports = override(
    //antd按需引入和主题配置
    fixBabelImports('import', {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: true,
    }),
    addLessLoader({
        modifyvars: {
          "@icon-url": `${path.resolve(__dirname,'build/assets/font/iconfont')}`, //使用本地字体文件
          '@font-size-base': '13px',
          '@primary-color': '#00879C'
        },
        javascriptEnabled: true
    }),
    addWebpackAlias({
        '@': path.resolve(__dirname, 'src')
    }),
    //装饰器
    addDecoratorsLegacy(),
    //关闭sourcemap，打包后不会再出现js和css的map文件
    rewiredMap()
)

##3，修改package.json文件中的启动项
把react-script 替换成 react-app-rewired
"scripts": {
-   "start": "react-scripts start",
+   "start": "react-app-rewired start",
-   "build": "react-scripts build",
+   "build": "react-app-rewired build",
-   "test": "react-scripts test",
+   "test": "react-app-rewired test",
}
```
## JSX语法
JSX是JavaScript的扩展语法，用来在js文件中写入HTML元素。React默认会进行HTML的转义，避免XSS攻击。React开发中，常用JSX语法插入表达式，样式，数组等。
#### 表达式
JSX中使用“{}”插入表达式（注意区分Vue中{{}}双向绑定）
##### 1，插入属性名
只能使用JSXSpreadAttribute(延伸属性，ES6的扩展运算符)， 换言之，括号内必须带三个点号
```
var props = {};
props.foo = x;
props.bar = y;
var component = <Component {...props} />;
```
##### 2，插入属性值
JSX语法规定，属性值必须是字符串或者是“{}”包裹的JavaScript表达式
```
<div tabIndex={this.props.a} />

//表达式
<Person name={ window.isLoggedIn ? window.name : '' } />;
```
##### 3，插入innerHTML内容
React是单向数据流，注意与Vue的“{{}}”双向绑定区分
```
<div>xxx{111}yyy</div>
```
#### 数组
JSX 允许在模板中插入数组，数组会自动展开所有成员
```
var arr = [
  <h1>Title</h1>,
  <h2>SubTitle</h2>,
];
ReactDOM.render(
  <div>{arr}</div>,
  document.getElementById('example')
);
```
也可以直接在innerHTML内容表达式中遍历数组
```
var ul = (
  <ul className="unstyled">
    {
      this.todoList.todos.map(function (todo) {
        return  (
          <li>
            <input type="checkbox" checked={todo.done}>
            <span className={'done-' + todo.done}>{todo.text}</span>
          </li>
        );
      })
    }
  </ul>
);
```
#### 样式
##### 1，style插入
JSX采用驼峰命名规则，样式属性如 font-size 等都改写为 fontSize。如果直接插入样式表达式，==需用{}包裹（两层大括号）==
```
<div style={{color: '#f00', fontSize: '14px'}}>Hello World.</div>

//或者
ar style = {
  color: '#f00',
  fontSize: '14px'
};

var node = <div style={style}>HelloWorld.</div>;
```

##### 2，class插入
JSX为避开html的class属性与ES6的class同名，特将class样式改写为className。

#### JSX驼峰命名
##### 事件名
HTML对大小写不敏感，事件名如onclick都采用全小写的形式，JSX对这一写法进行了改进，全部采用大小写敏感的驼峰命名写法，如onClick，onChange。
##### 标签名
JSX是在js文件中插入HTML表达式，HTML的固有属性必须使用JS形式，保持驼峰风格，如class要用className代替，for要用htmlFor代替，tabindex要用tabIndex代替，colspan要用colSpan代替。

1. JSX中可以使用点语法配合对象编写组件；
```
const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```
2. 用户自定义组件必须以大写字母开头，否则会报错；
3. JSX不能编译动态组件；
4. Props 默认值为 “True”；
5. false,null,undefined,true等元素不会被react渲染，而数字 0会被渲染
```
<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>
//即使props.messages.length===0，后面的组件也会被渲染
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>
//props.messages.length > 0 返回的是一个布尔值，后面的组件根据前面的布尔值被渲染
```
## React基础
### 一，React & ReactDom
#### react
react包是react的核心代码，核心思想是虚拟DOM。react包含了生成虚拟DOM的函数react.createElement，及Component类，当我们自己封装组件时，就需要继承Component类，才能使用生命周期函数。==即使是函数组件（无状态），在定义之前也必须引入react，因为jsx语法会被编译成react.createElement函数执行。==
```
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
//被react编译执行
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'   //无子元素时为null
)
```

#### react-dom
react-dom则是React剥离出的涉及DOM操作的部分，把react.createElement生存的虚拟DOM渲染到文档中变成实际DOM。主要包含三个API：
##### 1，render
render用于将React渲染的虚拟DOM渲染到浏览器DOM，==一般在顶层组件使用==。该方法把元素挂载到 container 中，并且返回 element 的实例（即 refs 引用），如果是无状态组件，render 会返回 null。
```
ReactDOM.render(<App />, document.getElementById('root'), callback);
//第一个参数表示待渲染的目标组件
//第二个参数表示接收目标组件的容器DOM对象
//第三个参数表示渲染完成后的回调（一般省略）
```
注：
1. 当首次调用时，容器节点里的所有 DOM 元素都会被替换，后续的调用则会使用 React 的 DOM 差分算法（DOM diffing algorithm）进行高效的更新；
2. ReactDOM.render() 不会修改容器节点（只会修改容器的子节点）。可以在不覆盖现有子节点的情况下，将组件插入已有的 DOM 节点中；
3. ReactDOM.render() 目前会返回对根组件 ReactComponent实例的引用。但是，不建议直接使用该引用，如果需要获得对根组件 ReactComponent实例的引用，那么推荐为根元素添加 callback ref。

##### 2，unmountComponentAtNode
从 DOM 中卸载组件，会将其事件处理器（event handlers）和 state 一并清除。如果指定容器上没有对应已挂载的组件，这个函数什么也不会做。如果组件被移除将会返回 true，如果没有组件可被移除将会返回 false。
```
ReactDOM.unmountComponentAtNode(container)
```
==执行在componentWillUnmount之前==
##### 3，findDOMNode
用户获取组件对应的真实DOM元素
```
import { findDOMNode } from 'react-dom';
<Example ref={ node=>{ this.node = node} }> // 利用ref获取Example组件的实例

const dom = findDOMNode(this.node);
```
一般不建议使用该方法，而是绑定一个 ref 到 DOM 节点上，通过ref获取组件真实DOM元素

### 二，State & Props
#### State
state是组件的内部状态，完全受控于当前组件，一般在class组件构造函数constructor中被初始化
```
constructor(props) {
    super(props);
    this.state = {date: new Date()};
}
```
##### 更新State
更新state必须且只能采用this.setState({})，传入一个JSON对象，与旧的state进行比较合并。而不能采用this.state.xxx = xxx语法更新state。

this.setState做了两件事，首先更新state，并且通知react重新渲染更新后的节点数据。this.state语法只是单纯更新了state，并不会引发元素的重新渲染。

this.setState更新操作是异步的，且多个连续的this.setState操作最终会被合并成一个更新操作执行，不能依赖上一次的更新变量执行下一次的更新逻辑。

##### State同步更新
###### 1，传入回调函数
```
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```
###### 2，更新操作采用async函数
```
async clickChange(){
    await this.setState({
        xxx: xxx
    });
    await this.setState({
        xxx: xxx
    });
}
```
#### Props
props是组件外部接口，是父组件和子组件通信的通道。父组件通过props传递数据给子组件，子组件通过调用props中的回调函数通知父组件状态修改。
1. props只读，子组件不能修改props；
2. 一般在子组件构造函数中调用super函数时传入props，把props挂载到this；

##### defaultProps
用于指定组件传入的props的默认值
```
import React, {Component} from 'react';
class Greeting extends Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 指定 props 的默认值：
Greeting.defaultProps = {
  name: 'Stranger'
};

export default Greeting;
```
##### propTypes
用于对传入的props进行类型检查，如果值类型不正确，控制台将会显示警告
```
import React, {Component} from 'react';
import PropTypes from 'prop-types';
class Greeting extends Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 指定 props 的类型
//React v15.5之前
Greeting.propTypes = {
  name: String
};
//React v15.5之后
Greeting.propTypes = {
  name: PropTypes.string,
  children: PropTypes.element.isRequired   //限制当前组件的子元素只能有一个
};
export default Greeting;
```
自 React v15.5 起，React.PropTypes 已移入另一个包中。请使用 prop-types 库 代替
##### render prop
render prop 是一个用于告知组件需要渲染什么内容的函数 prop。与普通的父组件与子组件通信的prop函数不同的是，render prop 传递的是一个渲染组件的函数。
```
class Mouse extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <div style={{ height: '100%' }}>
        {this.props.render(this.state)}
        //<Cat /> 不采用这种硬编程的方法写死组件
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```
组件内部动态决定渲染什么内容，而不是把渲染内容写死在组件渲染函数中

“render prop”模式中的“render”可以是任何其他名字，==注：如果命名成children，需要在propType中指定children为function==

Render Props 与 React.PureComponent 一起使用时，不能给 “render prop”传匿名函数，而应该在父组件中定义一个实例方法，把实例方法传递给“render prop”。因为PureComponent是基于props的浅比较，每次发生更新比较时，都是一个新的匿名函数，浅比较结果都返回false，发生子组件重新渲染，从而失去了PureComponent组件原有的性能优化功能。
```
class MouseTracker extends React.Component {
  // 定义为实例方法，`this.renderTheCat`始终
  // 当我们在渲染中使用它时，它指的是相同的函数
  renderTheCat(mouse) {
    return <Cat mouse={mouse} />;
  }
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={this.renderTheCat} />
      </div>
    );
  }
}
```

##### props.children
this.props.children的值是当前组件的子元素，相当于vue中的slot（插槽）。值有三种情况：
1. 组件没有子元素时，值是undefined；
2. 组件只有一个子元素时，值是Object对象；
3. 组件有多个子元素时，值是Array对象。
```
var NotesList = React.createClass({
  render: function() {
    return (
      <ol>
      {
        React.Children.map(this.props.children, function (child) {
          return <li>{child}</li>;
        })
      }
      </ol>
    );
  }
});

ReactDOM.render(
  <NotesList>
    <span>hello</span>
    <span>world</span>
  </NotesList>,
  document.body
);
```
##### React.children
React.children提供了一套处理this.props.children的工具，一般和React.cloneElement()结合使用来操作this.props.children。
1. React.Children.map()，类似Array.prototype.map()；
2. React.Children.forEach()，与React.Children.map()用法类似；
3. React.Children.count()，用于统计当前组件子元素的个数。this.props.children.length得不到正确结果；
4. React.Children.only()，用于判断当前组件是否只有一个子元素；
5. React.Children.toArray()，把this.props.children转换成Array，对children排序时需要使用。

### 三，组件
#### Class组件（有状态组件，类组件，容器组件） 
使用ES6的class继承React.Component定义组件，自己维护内部状态，以及接收父组件传递的props数据。
```
import React, {Component} from 'react';
export default class Child extends Component{
    render() {
        return (
            <div>react</div>
        )
    }
}
```
#### Function组件（无状态组件，函数组件，渲染组件）
一个返回JSX语句的JavaScript函数，接受props参数
```
import React from 'react';
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
==注：无论Class组件还是函数组件，必须先引入React，否则无法编译JSX语法==
#### React集成组件
##### React.Fragment
jsx语法规定，组件只能返回单个元素。React提供Fragment组件，用于返回元素列表。
```
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```
Fragment组件的缩写是<></>，缩写语法不支持属性和key
##### React.StrictMode
与 Fragment 一样，StrictMode 不会渲染任何可见的 UI，它为其后代元素触发额外的检查和警告。凡是React.StrictMode包裹的组件都遵循严格模式，检测一些过时的API和组件生命周期：
1. 检测过时字符串 ref API；
2. 检测废弃的 findDOMNode 方法调用；
3. 检测过时的 context API；
4. 检测已废弃的生命周期；
```
render() {
    return (
        <div>
          <Header />
          <React.StrictMode>
            <div>
              <ComponentOne />
              <ComponentTwo />
            </div>
          </React.StrictMode>
          <Footer />
        </div>
  );
}
```
只有被React.StrictMode包裹的组件及其子组件才遵循严格模式，未被包裹的不遵循严格模式
### 四，事件
React事件处理与传统事件处理的区别：
1. 事件处理函数采用驼峰命名，而不是传统的全小写；
2. 事件处理传入的是一个函数，而不是一个函数字符串；
```
//传统的事件处理，全小写，且传入一个函数字符串
<button onclick="activateLasers()">
  Activate Lasers
</button>
//React事件处理，驼峰命名，且传入一个函数名或者一个回调
<button onClick={activateLasers}>
  Activate Lasers
</button>
```
3. 传统的事件可以通过返回false阻止默认行为，React中则必须显式的使用 preventDefault；

#### 事件绑定
React支持4中事件绑定方式
##### 1，在组件构造函数中绑定组件实例this
```
this.handleClick = this.handleClick.bind(this);
```
优点：只会生成一个方法实例，并且绑定一次之后如果多次用到这个方法也不需要再绑定；

缺点：在组件事件处理较多时，会在构造函数中产生多余的代码量，且容易忘记绑定导致事件处理不生效。

==**只有这种事件绑定无法带参数，其余的事件绑定都可以带参数**==
##### 2，JSX中调用时绑定this
```
<button onClick={this.handleClick.bind(this, param)}>
    Click me
</button>
```
优点：写法简单，不需要在构造函数中额外绑定；

缺点：每一次调用的时候都会生成一个新的方法实例，对性能有影响。并且当事件回调被作为props从父组件传入时，组件可能会进行额外的重新渲染。
##### 3，JSX中调用时使用箭头函数绑定this
```
<button onClick={(param)=>this.handleClick(param)}>
    Click me
</button>
```
箭头函数的this指向执行环境的this，采用此办法和.bind(this)优缺点一样
##### 4，使用class属性初始化时指向箭头函数绑定this（实验性语法，需要babel转译）
```
  handleClick = (param) => {
    console.log('this is:', this);
  }
  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
```
优点：集合了前面3中方法的优点，规避了缺点，是最好的事件绑定方式


#### 事件分发

### 五，生命周期
react组件生命周期分为三个阶段：
1. Mounting（加载阶段）；
2. Updating（更新阶段）；
3. Unmounting（卸载阶段）；

#### Mounting（加载阶段）
##### constructor()
加载的时候调用一次，一般用于执行super(props)绑定this，初始化state等
##### static getDerivedStateFromProps(props, state)
组件每次被rerender的时候，包括在组件构建之后(虚拟dom之后，实际dom挂载之前)，每次获取新的props或state之后
##### render()
react最重要的步骤，创建虚拟dom，进行diff算法，更新DOM树都在此进行，==class组件必不可少的生命周期函数==
##### componentDidMount()
组件渲染之后（挂载到DOM树）调用，只调用一次。一般在此请求服务端接口数据。

#### Updating（更新阶段）
##### static getDerivedStateFromProps(props, state)
每次接收新的props之后都会返回一个对象作为新的state，返回null则说明不需要更新state；配合componentDidUpdate，可以覆盖componentWillReceiveProps的所有用法。==setState()引起的组件更新不会调用此钩子函数==
##### shouldComponentUpdate(nextProps, nextState)
组件接收到新的props或者state时调用，return true就会更新dom（使用diff算法更新），return false能阻止更新（不调用render）。常在此处做组件性能优化。
##### render()
重新渲染已更新的节点
##### getSnapshotBeforeUpdate(prevProps, prevState)
触发时间: update发生的时候，在render之后，在组件dom渲染之前；返回一个值，作为componentDidUpdate的第三个参数；配合componentDidUpdate, 可以覆盖componentWillUpdate的所有用法
##### componentDidUpdate()
组件加载时不调用，组件更新完成后调用

#### Unmounting（卸载阶段）
##### componentWillUnmount()
组件被卸载之前调用，常用于做一些释放内存的操作

React16新的生命周期弃用了componentWillMount、componentWillReceivePorps，componentWillUpdate
[生命周期图](https://segmentfault.com/img/bVbhRvx?w=720&h=394)

### 六，Refs & Refs转发
#### Refs
在 React 数据流中，props 是父组件与子组件交互的唯一方式，避免直接操作DOM。但是在某些特殊情况下需要直接操作DOM元素或者组件实例：
1. 管理焦点（focus），文本选择或媒体播放（H5 video & Audio）；
2. 触发强制动画；
3. 集成第三方 DOM 库等。

##### 创建Refs
###### 1，React.createRef()
React封装了创建Refs的API，替代了老版本直接用String作为Refs。
```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.domRef = React.createRef();
    this.componentRef = React.createRef();
  }
  render() {
    return (
        <div>
            <div ref={this.domRef} />
            <ChildComponent ref={this.componentRef} />
        </div>
    );
  }
}
//this.domRef.current === 真实div DOM元素
//this.componentRef.current === ChildComponent组件实例，可以通过这个访问实例方法
```
通过Refs的current属性访问实际DOM元素或组件实例：
1. 当 ref 属性用于 HTML 元素时，构造函数中使用 React.createRef() 创建的 ref 接收底层 DOM 元素作为其 current 属性；
2. 当 ref 属性用于自定义 class 组件时，ref 对象接收组件的挂载实例作为其 current 属性；
3. 你不能在函数组件上使用 ref 属性，因为他们没有实例。但是可以在函数组件中使用createRef()创建的Refs绑定到其渲染的HTML或者子组件上。

###### 2，回调 Refs
传递一个回调函数给HTML元素或组件，作为Refs。回调函数接收接受 React 组件实例或 HTML DOM ，在组件属性中存储对 DOM 节点的引用。
```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.domRefCallback = element => {
      this.domRef = element;
    };
    this.componentRefCallback = component => {
      this.componentRef = component;
    };
  }
  render() {
    return (
        <div>
            <div ref={this.domRefCallback} />
            <ChildComponent ref={this.componentRefCallback} />
        </div>
    );
  }
}
//this.domRef === 真实div DOM元素
//this.componentRef === ChildComponent组件实例，可以通过这个访问实例方法
```
==注意：不是current指向元素或组件实例==

如果把回调函数作为组件Refs时，且组件的子组件接收props.ref作为HTML元素ref，则回调函数的参数是子组件HTML元素对应的真实DOM，而不是组件实例。
```
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
// this.inputElement指向的是子组件CustomTextInput的input，而不是CustomTextInput实例
```
#### Refs转发
Ref 转发是一项将 ref 自动地通过组件传递到其一子组件的技巧。子组件接收父组件的ref，设置到自己渲染的DOM元素上，从而可以在父组件获取子组件中的DOM元素。（上例就是一种Refs转发）
```
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

### 七，组件之间通信
#### 1，Props
父组件通过props传递数据给子组件，子组件通过调用props中的回调函数通知父组件状态修改
#### 2，Context
react是自上而下的单向数据流，在深层嵌套组件中通过props传递值，会使得很多中间组件发生不必要的props传递。Context提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递 props。react提供了一组操作Context的API：
1. createContext(defaultValue) —— 创建Context，defaultValue为Context默认值，Provider没有显示指定value的时候生效；
2. Context.Provider —— Provider React 组件，接收一个value属性，传递给消费组件。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。value值发生变化时，它内部的所有消费组件都会重新渲染。；
3. Class.contextType —— 被赋值为一个由 React.createContext() 创建的 Context 对象。这能让你**在Class组件中**使用 this.context 来消费最近 Context 上的那个值，可以在任何函数中访问；
4. Context.Consumer ——  允许value以回调函数参数形式指定，并返回一个使用该Context的组件。如：
```
<UserContext.Consumer>
    {user => (
        <ProfilePage user={user} />
    )}
</UserContext.Consumer>
```

```
//Context.js
import {createContext} from 'react';
export const TestContext = createContext();

//Provider组件（提供Context数据的组件）
import { TestContext } from "./context";
export default class MyContext extends Component{
    render() {
        return (
            <TestContext.Provider  value="context test">
                <Child></Child>
            </TestContext.Provider>
        )
    }
}

//消费（使用）Context数据的组件
import { TestContext } from "./context";
export default class Grandson extends Component{
    static contextType = TestContext;
    render() {
        return (
            <div>
                <div>this is grandson，context is <p>{this.context}</p></div>
            </div>
        )
    }
}
//class组件的静态属性contextType指定从哪个Context中获取数据
//this.context获取Context数据
```

#### 3，redux
参考下面redux介绍
#### 4，EventBus
```
//emitter.js
class EventEmitter {
  constructor () {
    // 存储事件
    this._events = this._events || new Map()
  }
  // 监听事件
  addListener (type, fn) {
    const handle = this._events.get(type);
    if (!handle) {
        this._events.set(type, [fn]);
    }else{
        this._events.set(type, [...handle, fn]);
    }
  }
  // 触发事件
  emit (type, ...args) {
    let handle = this._events.get(type);
    if(handle){
        handle.map(fn => {
            fn.apply(this, args);
        });
    }else{
        handler.call(this);
    }
    return true;
  }
}
const emitter = new EventEmitter();
export default emitter


import emitter from 'emitter';
// 监听事件
emitter.addListener('ages', age => {
  console.log(age);
});
// 触发事件
emitter.emit('ages', 18);  // 18
```
npm包
```
npm install events -S
```

## React进阶
### 一，高阶组件(HOC，Higher Order Components)
高阶组件是参数为组件，返回值为新组件的==函数==
```
//原组件（参数组件）
class Test extends Component{
    render() {
        return (
            <div>
                <p>{this.props.title} - {this.props.name}</p>
                {this.props.children}
            </div>
        );
    }
}

//返回函数组件的高阶组件
const withTest = Comp => {
    const name = '高阶组件';
    return props => <Comp {...props} name={name}></Comp>
}
//注意：不能直接返回<Comp />，而是返回一个新的函数组件


//返回class组件的高阶组件
const withHtest = Comp => {
    return class extends Component{
        render() {
            return (
                <Comp {...this.props}>
                    <span>this is Hoc</span>
                </Comp>
            );
        }
    }
}
//返回一个匿名class组件，this.props表示参数组件的props
```

1. 高阶组件是纯函数，不产生任何副作用；
2. 返回的新组件和参数传入的组件是两个完全独立的组件；
3. 不要在render方法中使用HOC，会发生组件不必要的卸载和重新渲染，影响性能问题；
4. 组件的ref不会被高阶组件获取，需要采用Refs转发；

### 二，React动画
### 三，组件懒加载
React提供lazy和Suspense组件实现组件懒加载（code-splitting，代码分割），类似按需加载，渲染的时候才加载代码
```
import React, {Component, lazy, Suspense} from 'react';

const Test = lazy(() => import('./test'));

export default class MyLazy extends Component{
    render() {
        return (
            <Suspense fallback={<div>Loading...</div>}>
                <Test date={Date.now()}/>
            </Suspense>
        )
    }
}
```
##### React.lazy懒加载原理
React.lazy必须通过调用动态的import()加载一个函数，此时会返回一个Promise，并解析(resolve)为一个带有包含React组件的默认导出的模块。在Promise状态未变成resolved之前（组件未加载完成），都会渲染fallback后面的组件内容。

lazy只能懒加载默认导出的组件，如果想引入命名导出的组件，需要创建一个中间模块
```
//ManyComponents.js是命名导出模块
//中间模块，把命名模块赋值给default
export { MyComponent as default } from "./ManyComponents.js";
//app
const MyComponent = lazy(() => import("./MyComponent.js"));
```
##### React.Suspense异步渲染原理
React.Suspense也是一种虚拟组件，类似于Fragment，仅用作类型标识。Suspense组件可以放在（组件树中）Lazy组件上方的任意位置，并且下方可以有多个Lazy组件。只要还有还没回来的Lazy组件，就渲染fallback指定的内容。React从框架层对用户体验提出强烈要求，==**没被Suspense包起来的Lazy组件会报错**==。

### 四，Hook
Hook是一些可以让你在函数组件里使用react state和生命周期的函数，开发者不需要写class组件也能使用react特性。规定：
1. 只能在函数组件或者自定义的 Hook 中使用Hook；
2. 只能在函数最外层使用Hook，不能在循环、条件判断或者子函数中调用。

可以通过eslint-plugin-react-hooks插件来规范项目中使用的Hook
#### 常用Hook
##### 1，useState
用于函数组件添加state变量。普通函数退出后变量就会”消失”，而 state 中的变量会被 React 保留
```
import React, { useState } from 'react';
function Example() {
  // 声明一个叫 "count" 的 state 变量
  const [count, setCount] = useState(0);
//左边采用数组解析构，第一个变量是自定义state，第二个是对应的操作函数
//useState参数是state变量的初始值，可以是基本数据类型，数组，对象等
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

## 相当于class组件的this.state
this.state = { count: 0 };
```
用定义state变量时返回的操作函数修改state的值
```
setCount(1);

##相当于class组件的setState
this.setState({ count: this.state.count + 1 })};
```
==与setState不同的是，更新 state   变量总是替换它而不是合并它，所以在update复杂结构state值的时候要格外注意==
##### 2，useEffect && useLayoutEffect
useEffect Hook 为函数组件提供执行副作用操作的能力，例如数据获取，设置订阅以及手动更改 React 组件中的 DOM 等。useEffect Hook  相当于componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合
```
function Example() {
  const [times, setTimes] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${times} times`;
  }, [times]);
}
```
- 执行机制：在函数组件内部调用useEffect Hook，第一个参数是回调函数（称之为effect）。在函数组件发生渲染时，都会执行该回调函数（包括首次渲染和更新渲染）
- effect清除：effect中可能会有一些占用内存的操作，例如数据订阅等，需要在组件卸载的时候进行清除，释放内存。effect约定在执行下一次effect执行前或者组件卸载前执行effect中返回的函数进行清除操作，如下
```
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [times, setTimes] = useState(0);
  useEffect(() => {
    console.log('effect running', times);
    return () => {
      console.log('effect clear');
    }
  }, [times]);
  const onClickHandle = () => {
      setTimes(++times)
  }
  return (<Button type="default" onClick={onClickHandle}></Button>);
}
//每次点击button（组建重新渲染）都会依次打印：
//effect running
//effect clear
```
- 性能优化：有时候并不希望每次发生组件重新渲染时，都执行effect，可以给useEffect传入第二个参数。第二个参数是一个依赖项数组，表示effect执行依赖哪些数据修改，默认值是 [] 。如果依赖的数据重新渲染前后没有发生改变，effect将不会被执行。如上例中的times

> ###### useLayoutEffect：作用与 useEffect 相同，与 useEffect 不同的是，它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。使用不当，会出现视觉更新阻塞。

##### 3，useRef && useImperativeHandle
生成一个ref对象，ref.current 属性被初始化为传入的参数，该值在组件的整个生命周期内保持不变，每次渲染时返回同一个 ref 对象
```
const inputEl = useRef(Date.now());
```
与createRef()不同的是：

- createRef生成的ref.current未被使用在DOM元素或者组件上时，始终是null，使用之后指向实际DOM或者组件实例；
- useRef生成的ref.current被初始化为传入的参数，且在组件整个生命周期内不变，绑定到DOM后，.current指向实际DOM


useImperativeHandle对应与ref转发，与 forwardRef 一起使用
```
useImperativeHandle(ref, createHandle, [deps])
```

##### 4，useMemo
利用“创建函数”和依赖项数组，生成一个memoized值，且仅当某个依赖项渲染前后的值发生改变时，“创建函数”才会执行重新计算memoized值。如：
```
const memoizedValue = useMemo(() => return Math.sum(a, b), [a, b]);
```
- 创建函数在首次渲染时无条件执行；
- 依赖项数组默认是 []，没有依赖项时，每次发生组件重新渲染都会执行；
- 不要在“创建函数”中执行与渲染无关的操作，记住与useEffect的区别

##### 5，useContext
接收一个 context 对象（通过React.createContext API创建）并返回该 context 的当前值。useContext的参数必须是 context 对象本身，其值由距离最近的Context.Provider决定，并且使用的useContext的组件总会在 context 值变化时重新渲染。如：
```
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = React.createContext();

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <>
        ...<ThemedButton />
      </>    
    </ThemeContext.Provider>
  );
}
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
```
如果context值修改引起的重新渲染组件的开销较大，可以通过使用 memoization 来优化
##### 6，useReducer
useReducer是useState的替代方案，解决state结构复杂，state使用前后相互依赖等问题。useReducer使用方法形如：
```
const [state, dispatch] = useReducer(reducer, initialArg, init);
```
参数：
1. reducer：类似redux的reducer，(state, action) => newState；
2. initialArg：state的初始值；
3. init：惰性初始化state的函数，用于自定义state初始化，接受外部props，state重置等

返回值（数组解构）：
1. 当前state；
2. dispatch：类似redux中的dispatch，用于触发action，修改state

[完整案例查看](https://juejin.im/post/5ca467256fb9a05e545e46c6)


##### 7，useCallback
把内联回调函数及依赖项数组作为参数传入 useCallback，返回该回调函数的 memoized 版本，回调函数仅在某个依赖项改变时才会更新。相当于useMemo(() => fn, deps)返回传入的回调函数，而不是计算值
```
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```
可以在某些特定的场景下调用memoizedCallback，而不需要触发组件重新渲染
##### 8，useDebugValue

#### 自定义Hook
1. 自定义Hook是一个普通的JavaScript函数，不受react约束，可以自行决定传入什么参数和返回什么结果；
2. 函数名必须以“use”开头，否则React 将无法自动检查你的 Hook 是否违反了 Hook 的规则
3. 只能在函数顶层无条件调用React Hook，不能在条件判断语句或者循环语句中调用；
4. 每次使用自定义Hook，其中的所有 state 和副作用都是完全隔离的，不会产生交叉影响。

简单来说，自定义Hook只是简单的公共逻辑代码提取，并且在其中可以调用React Hook，不会对使用者产生状态污染

### 五，性能优化
#### React.PureComponent
基于Component组件，在shouldComponentUpdate中进行prop和state的浅比较(shallowEqual)实现性能优化。
```
import React, { PureComponent } from 'react';
class Demo extends PureComponent{
    render() {
        return (
            <div>PureComponent</div>
        )
    }
}
```
注：
1. PureComponent一般用于展示组件，单纯完成渲染工作和渲染性能优化；
2. 浅比较只有当引用地址发生变化时，才发生比较操作。所以当state或props不要使用数组和对象等引用类型；
3. state和props频繁发生变化时，不建议使用PureComponent（浅比较也是耗时的）。

#### React.memo（v16.6+）
React.mome是一个高阶组件，用于生成一个性能优化的组件，参数是一个函数组件
```
import React from 'react';
const Demo = React.memo((props) => {
   return <div>PureComponent</div> 
});
```
经过memo加工生成的组件拥有与PureComponent一样的能力，==解决函数组件无法使用PureComponent的问题==
### 六，diff 算法 & Virtual DOM
将Virtual DOM（虚拟Dom）树转换成Actual DOM（真实Dom）树的最少操作的过程，叫作调和。diff算法是调和的具体实现，将O(n^3)复杂度 转化为 O(n)复杂度。
diff算法原则：
- 分层同级比较，不跨层比较；
- 相同的组件生成的DOM结构类似；
- 分组内的同级节点通过唯一的id进行区分（key）

##### 不同类型节点比较
逐层比较，不同类型节点直接替换，组件经历unmount，mount
##### 同类型，不同属性节点比较
同类型节点一般会出现以下几种形式更新：
1. 插入新的同级节点
2. 删除同级节点
3. 同级节点交换位置
4. 更新节点属性

节点移动规则：index>lastIndex时，max(index,lastIndex)进行移动。index表示元素在旧树中的同级标记位置，lastIndex表示新树当前遍历元素。若在旧树种找不到节点，则直接插入；遍历完成后，查找新树中存在，且在旧树中不存在的节点进行删除。==所以同级节点添加唯一的key进行区分至关重要，直接决定react组件的性能==

diff算法需要改进的地方：列表尾部节点移动到列表首部时，需要移动元素(list.length-1)次。实际开发中，尽量避免列表尾部元素直接移动到首部的更新操作，在列表数据很庞大时，直接影响组件性能。

### 七，React API

## react-router（v4.0+）
### 一，react-router & react-router-dom
#### react-router
提供了router的核心api，如Router、Route、Switch等，但没有提供有关dom操作进行路由跳转的api。react-router是路由的base（core）包，浏览器端开发安装react-router-dom即可。
#### react-router-dom
提供了BrowserRouter、HashRouter、Route、Link、NavLink等api，可以通过dom操作触发事件控制路由。React-router-dom集成了React-router，实际开发中直接用React-router-dom即可。
##### 主要组件
###### 路由
1. <BrowserRouter>：History API push原理实现
2. <HashRouter>：超链锚点原理实现，url中带#

###### 路由规则
1. <Route>：定义路由匹配规则
2. <Switch>：匹配单个路由，一般用来包裹<Route>，==只匹配最先匹配到的单个路由==
3. <IndexRoute>：默认路由（v4.0+已经去掉，<Route exact />代替）

###### 跳转链接
1. <Link>：相当于<a>链接
2. <IndexLink>：默认链接，用于解决<Link>匹配根目录始终选中状态的问题（v4.0+已经去掉） 
3. <NavLink>：特殊的<Link>，可以添加选中样式activeClassName
4. <Redirect>：重定向跳转

### 二，路由配置

##### 路由匹配规则
1. :paramName：匹配一段位于 /、? 或 # 之后的 URL，匹配的部分作为路由参数；
2. ()：在它内部的内容被认为是可选的；
3. *：匹配任意字符（非贪婪的）直到命中下一个字符或者整个 URL 的末尾；
```
<Route path="/hello/:name">         // 匹配 /hello/michael 和 /hello/ryan
//this.props.params.name 获取参数值
<Route path="/hello(/:name)">       // 匹配 /hello, /hello/michael 和 /hello/ryan
<Route path="/files/*.*">           // 匹配 /files/hello.jpg 和 /files/path/to/hello.jpg
```
4. react-router@4.0+版本支持多个路由同时匹配；
```
<Route path="/" component={Home}></Route>
<Route path="/admin" component={Admin}></Route>
<Route path="/login" component={Login}></Route>
```
浏览器输入localhost:3000时，相当于“/”。“/”、“/admin”、"/login"三个路由都会被匹配，所以三个组件会被同时加载。此时，应该用<Switch>包裹路由，实现单个路由匹配。一旦有一个路由被匹配，将不会再往下匹配，永远只会加载第一个被匹配到的路由组件。
==实际开发技巧：带参数的复杂路由配置放在不带参数的简单路由配置之前==
5. exact标记路由精确匹配（注：不要在嵌套路由的父层级做精确匹配，否则子路由不会被匹配）；
```
<Route exact path="/admin" render={() =>
    <Admin>
        <Switch>
            <Route path="/admin/home" component={Home}></Route>
            <Route component={NoMatch}></Route>
        </Switch>
    </Admin>}>
</Route>
// "/admin/home"匹配不到Home组件
```
如果父路由设置为精准匹配，在匹配子路由时，匹配不到父路由，根本接触不到子路由

### 三，路由嵌套
##### v4.0之前的版本（v2.x/v3.x）
v4.0之前的版本是将路由看成一个整体的单元，与别的组件是分离的，一般会单独放到一个 router 文件中，对其进行集中式管理；并且，布局和页面的嵌套由路由的嵌套所决定
```
import { BrowserRouter as Router, Route, NavLink, IndexRoute, browserHistory } from 'react-router';
const PrimaryLayout = props => (
  <div className="primary-layout">
    <header>
      <NavLink to="/">home</NavLink>
      <NavLink to="/users">users</NavLink>
    </header>
    <main>
      {props.children}
      //props.children渲染Route组件中component指定的组件，默认是HomePage组件
    </main>
  </div>
);

const HomePage =() => <div>Home Page</div>
const UsersPage = () => <div>Users Page</div>

const App = () => (
  <Router history={browserHistory}>
    <Route path="/" component={PrimaryLayout}>
      <IndexRoute component={HomePage} />
      <Route path="/users" component={UsersPage} />
    </Route>
  </Router>
);
render(<App />, document.getElementById('root'))
```

##### v4.0+版本
v4.0+版本对路由进行了拆分，将其放到了各自的模块中，不再有单独的 router 模块，充分体现了组件化的思想。v4.0+中，<BrowserRouter>，<Route>，<Link>等元素都是组件。
```
import { BrowserRouter as Router, Route, NavLink } from 'react-router-dom'

const PrimaryLayout = () => (
  <div className="primary-layout">
    <header>
      <NavLink to="/">home</NavLink>
      <NavLink to="/users">users</NavLink>
    </header>
    <main>
      <Route path="/" exact><HomePage/></Route>
      <Route path="/users"><UsersPage/></Route>
    </main>
  </div>
)

const HomePage =() => <div>Home Page</div>
const UsersPage = () => <div>Users Page</div>

const App = () => (
  <BrowserRouter>
    <PrimaryLayout />
  </BrowserRouter>
)

render(<App />, document.getElementById('root'))
```
##### 实战技巧
v4.0+版本，路由被当做普通组件一样使用，可以开发混合路由组件（在组件中混合<Route>等路由标签）实现页面架构，如上面的demo就是混合路由组件方式。但实际开发中还是建议路由统一配置，不过这里统一配置不同于v4.0-之前的统一配置。
```
## 1，App.js作为页面的统一入口，包裹所有页面，App组件只需要简单渲染子组件即可
import React, {Component} from 'react';
import './App.css';
export default class extends Component{
  render() {
    return (
      <div>
        {this.props.children}
      </div>
    );
  }
}

## 2，src目录下新建router.js文件，作为路由配置文件
import React, { Component } from 'react';
import { BrowserRouter as Router, Route, Switch, Redirect } from 'react-router-dom';
import App from './App';
import Login from './pages/login';
import Admin from './pages/admin';
import Detail from './pages/detail';
import NoMatch from './pages/noMatch';
import Home from './components/Home';
export default class IRouter extends Component{
    render() {
        return (
            <Router>
                <App>
                    <Switch>
                        <Redirect exact from="/" to="/admin/home"/>
                        <Route path="/login" component={Login}></Route>
                        <Route path="/admin" render={() =>
                            <Admin>
                                <Switch>
                                    <Route path="/admin/home" component={Home}></Route>
                                    <Route component={NoMatch}></Route>
                                </Switch>
                            </Admin>}>
                        </Route>
                        <Route exact path="/order/detail" component={Detail}></Route>
                    </Switch>
                </App>
            </Router>
        );
    }
} 
//App包裹所有路由配置，作为子组件在App中渲染
//路由嵌套时，父路由组件采用render props加载匿名函数返回组件方式实现，父组件包裹子路由组件，只需要在父组件相应地方加载子组件即可
//默认路由采用Switch Redirect组合方式指定，不再用IndexRoute
//react-router@4.0+不需要在<Router>中传入history，而是通过useHistory hooks获取history，可以在任何地方获取history，增加了路由的灵活性

## 3，嵌套路由父组件（如Admin），在内容加载区渲染子组件（this.props.children）
import React, { Component } from 'react';
import NavLeft from '@/components/NavLeft';
import Header from '@/components/Header';
import Footer from '@/components/Footer';
import './index.less';
export default class Admin extends Component{
    render() {
        return (
            <div className="admin-wrapper">
                <aside>
                    <NavLeft/>
                </aside>
                <article className="main-wrapper">
                    <Header/>
                    <div className="content">
                        {this.props.children}
                    </div>
                    <Footer/>
                </article>
            </div>
        );
    }
}
```

### 四，动态路由 & 传参
#### 动态路由
使用场景
1. 用户根据用户ID加载用户信息页面；
2. 根据产品ID加载产品详情页面
```
//路由配置
<Route path="/user/:id" component={UserInfo} />

<Link to={'/user/2' }>About</Link>
//或者
this.props.history.push('/user/2')
```

#### 路由跳转
##### 1，标签跳转
react-router提供 <Link> 和 <NavLink> 实现标签跳转，这里只举例说明 <Link>
```
## 1，to：String
<Link to="/courses">courses</Link>

## 2，to：Object
<Link to={{
pathname: '/courses',
search: 'sort=name',
hash: '#the-hash',
query: { data: 1 },
state: { fromDashboard: true },
replace: false
}}>courses</Link>
//pathname：跳转路径
//search：url参数，以“?sort=name”拼接在url中
//query：路由参数，刷新页面后丢失
//state：路由参数，刷新页面后不会丢失
//replace：替换历史堆栈中的当前条目，而不是添加新条目

## 3，activeClassName：String & activeStyle: object
<NavLink
to="/faq" activeClassName="selected"
>FAQs</NavLink>
//NavLink特有
```
##### 2，编程式跳转
###### withRouter（react-router官方推荐）
```
import React from "react";
import {withRouter} from "react-router-dom";
 
class MyComponent extends React.Component {
  ...
  myFunction() {
    this.props.history.push("/some/Path");
  }
  ...
}
export default withRouter(MyComponent);
//缺点：只能在当前组件内获取history对象，也就是说只能在当前组件内进行路由跳转操作
//实际开发中往往需要在其他地方实现跳转，比如redux数据修改的时候
```
###### 使用Context
react-router@4.x 在 Router 组件中通过Context暴露了一个router对象，可以在子组件中通过Context获取router对象，从而获取history
```
import React from "react";
import PropTypes from "prop-types";
 
class MyComponent extends React.Component {
  static contextTypes = {
    router: PropTypes.object.isRequired
  }
  constructor(props, context) {
     super(props, context);
  }
  ...
  myFunction() {
    this.context.router.history.push("/some/Path");
  }
  ...
}
//风险：Context在React未来的版本中可能不在被支持
```
###### 自定义hack（==实际开发推荐==）
```
// src/history.js
//BrowserRouter
import { createBrowserHistory } from 'history';
//HashRouter
import { createHashHistory } from 'history';
export default createHashHistory()

// src/index.js
import { Router, Link, Route } from 'react-router-dom';
import history from './history';
ReactDOM.render(
  <Provider store={store}>
    <Router history={history}>
      ...
    </Router>
  </Provider>,
  document.getElementById('root'),
);

//需要编程式跳转的地方
import history from './history';
export function addProduct(props) {
  return dispatch =>
    axios.post(`xxx`, props, config)
      .then(response => {
        history.push('/cart'); //这里
      });
}
```
以上三种编程式路由跳转，都可以把传入一个object实现路由传参
```
history.push({
    pathname: '/courses',
    search: '?sort=name',
    hash: '#the-hash',
    state: { fromDashboard: true }
})
```
#### 路由参数
##### params方式
简单快捷，成为页面路由的一部分，作为路由匹配，刷新页面不会丢失。但只能传字符串，传递对象需要JSON.stringify转换
###### 传参
```
//路由配置
<Route path="/about/:id" component={About} />

<Link to={'/about/2' }>About</Link>
//或者
this.props.history.push('/about/2')
```
###### 获取
```
componentDidMount(){
    console.log(this.props.match.params.id);
}
```
##### query方式
不作为路由匹配，location.query中获取，刷新页面时参数丢失
###### 传参
```
//路由配置
<Route path="/contact" component={Contact} />

<Link to={{pathname:'/contact',query:{id:'456'}}}>contact</Link>
//或者
this.props.history.push({pathname :'/contact',query :{id:'456'}})
```
###### 获取
```
componentDidMount(){
   console.log(this.props.location.query.id);
}
```
##### state方式
不作为路由匹配，location.state中获取，刷新页面时参数不会丢失。==路由传参首选==
###### 传参
```
//路由配置
<Route path="/contact" component={Contact} />

<Link to={{pathname:'/contact',state:{id:'456'}}}>contact</Link>
//或者
this.props.history.push({pathname :'/contact',state :{id:'456'}})
```
###### 获取
```
componentDidMount(){
   console.log(this.props.location.state.id);
}
```
==以上三种路由获取参数的方式，都必须通过withRouter包裹组件才能在props中获取location和match==

- v4.0+版本，对于无状态组件（Function Component），还可以通过useParams hook获取路由参数；
- v4.0-版本，Router传入history context，组件中通过this.props.history获取路由参数

### 五，路由钩子
#### react-router@3.x
react-routerV3.0-提供onEnter 和 onLeave作为路由钩子函数，分别在路由进入和离开的时候执行
```
const enterTab = (nextState, replace) => {
  replace({ pathname: '/' });
}
const leaveTab = () => {
  console.log('是否退出?');
}

<Router history={browserHistory}>
  <Route path='/' component={App}>
    <Route path='map' component={Map} onEnter={enterTab.bind(this)} onLeave={leaveTab.bind(this)}></Route>
  </Route>
</Router>
```
- onEnter一般用于做一些路由权限控制，例如用户登录验证，还可以用于代替<Redirect>
- onLeave一般用户做一些退出判断
- onEnter 和 onLeave只能在路由定义的时候使用

==react-router@4.x+已经不在支持这两个路由钩子==

#### react-router@4.x+
由于react-router@4.x+把路由都组件化，不再支持路由钩子，因此需要开发者实现自定义钩子路由
```
//components/AuthRouter自定义路由组件
import React, { Component } from 'react'
import { Route, Redirect } from 'react-router-dom';

export default class AuthRouter extends Component{
    render() {
        const { component: Component, ...rest } = this.props;
        const isLogin = localStorage.getItem('user');
        return (
            <Route {...rest} render={ props => {
                    return isLogin? <Component {...props}/> : <Redirect to="/login"/>
                }
            }/>
        );
    }
}
```
在路由配置中，需要权限验证的路由替换成自定义路由组件即可
```
//src/router.js路由配置文件
import React, { Component } from 'react';
import { BrowserRouter as Router, Route, Switch, Redirect } from 'react-router-dom';
import AuthRoute from '@/components/AuthRouter';
import App from '@/App';
import Admin from '@/pages/admin';
import Login from '@/pages/login';
import Home from '@/pages/home';
import CMap from '@/pages/map';
import User from '@/pages/user';

export default class IRouter extends Component{
    render() {
        return (
            <Router>
                <App>
                    <Switch>
                        <Redirect exact from="/" to="/admin/home"/>
                        <Route path="/login" component={Login}></Route>
                        <Route path="/admin" render={() =>
                            <Admin>
                                <Switch>
                                    <AuthRoute path="/admin/home" component={Home}></AuthRoute>
                                    <AuthRoute path="/admin/map" component={CMap}></AuthRoute>
                                    <AuthRoute path="/admin/user" component={User}></AuthRoute>
                                </Switch>
                            </Admin>}>
                        </Route>
                        <Route exact path="/order/detail" component={Detail}></Route>
                    </Switch>
                </App>
            </Router>
        );
    }
} 
```
#### 路由监听
react-router的history提供listen方法实现路由监听
```
import React, { Component } from 'react';
import { withRouter } from 'react-router-dom';
class CHeader extends Component{
    state = {}
    componentDidMount() {
        // 1.接收到props中的history对象
        const history = this.props.history;
        // 2. 使用history的listen方法添加自定义监听事件 参数为一个回调函数 即：路由改变之后执行的方法
        this.unListen = history.listen(this.routerChange);
    }
    routerChange = () => {
        ...dosomething
    }
    render() {
        return (
            <div>home page.</div>
        )
    }
}

export default withRouter(CHeader)
```

### 六，路由懒加载
##### 1，React.lazy & React.Suspense
由React提供的lazy API实现组件的异步加载，但异步加载的组件需要用React提供的Suspense组件包裹，在异步组件尚未加载时一个初始状态，且不支持服务端渲染
```
const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </div>
  );
}
```
##### 2，[loadable-components](https://github.com/gregberge/loadable-components) & [react-loadable](https://github.com/jamiebuilds/react-loadable)
解决方法一不支持服务端渲染，还可以支持客户端渲染，两种插件的使用方法类似，本质都是一个高阶组件，返回一个promise
###### loadable-components
```
## 1，yarn add @loadable/component
## 2，使用
import loadable from '@loadable/component'
const OtherComponent = loadable(() => import('./OtherComponent'))
function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  )
}
```
###### react-loadable
```
## 1，yarn add react-loadable
## 2，src/util/loadable.js
import React from 'react';
import Loadable from 'react-loadable';

//通用的过场组件
const loadingComponent =()=>{
    return (
        <div>loading</div>
    ) 
}
//过场组件默认采用通用的，若传入了loading，则采用传入的过场组件
export default (loader,loading = loadingComponent)=>{
    return Loadable({
        loader,
        loading
    });
}
## 3，router.js等使用的地方
import React, { Fragment } from 'react'
import { BrowserRouter, Route } from 'react-router-dom'
import loadable from '../util/loadable'

const Home = loadable(()=>import('@pages/home'))

const Routes = () => (
    <BrowserRouter>
        <Route path="/home" component={Home}/>
    </BrowserRouter>
);

export default Routes
```

##### 3，基于 webpack 和 [lazyload-loader](https://github.com/rongchanghai/lazyload-loader)
```
## 1，yarn add lazyload-loader
## 2，webpack配置
module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: [
          'babel-loader',
          'lazyload-loader',
        ]
      },
    ···
  }
## 3，router.js等地方使用
// 使用lazy! 作为前缀
import Shop from 'lazy!./view/Shop';
<Route path="/shop" component={Shop} />

```

## Redux（单向数据流）
### 一，flux & redux
### 二，redux三大原则
##### 单一数据源
整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中
##### State只读
redux中的State就是一个用于存储应用状态数据的普通对象。改变 state 的唯一方法就是触发 action，action 是一个用于描述已发生事件的普通对象
##### reducer必须是纯函数
reducer必须是纯函数，不能有任何产生副作用的操作，例如new Date等。任何时候执行reducer函数，得到的结果都是一样的
### 三，redux基本要素 & API
#### 基本要素
##### 1，Store
全局唯一，包含以下几个部分：
1. state：存储应用的状态数，本质是一个JavaScript对象；
2. dispatch：派发action；
3. reducer：接受dispatch派发的action，返回新的state；

##### 2，Action
标记修改store中state数据的动作，action的本质是一个普通的对象，包含一个type字段表示action的类型（必须），以及其他action携带数据（可选）
##### 3，Reducer
接收action，根据action type产生新的state，必须是纯函数
#### API
##### redux
1. **createStore**：创建Store，第一个参数是reducer，第二个参数是state的初始值（可选），该参数一般用于服务端渲染时与前端数据同步；
2. **combineReducers**：组合reducer，参数是一个reducer对象，返回一个组合后的reducer，本质是一个高阶组件。combineReducers把Store的state状态模块化，各个reducer的state 参数都不同，分别对应它管理的那部分 state 数据；
3. **dispatch**：派发action，参数是一个action。reducer接受action，返回新的state；
4. **subscribe**：订阅Store的state状态变化，触发组件更新；
5. **getState**：获取Store的整个state数据树；
##### react-redux
1. **connect**：连接组件和redux，参数是mapStateToProps和mapDispatchToProps。执行返回函数连接组件，参数是组件实例；
2. **Provider**：包裹应用的根组件，提供store，使子孙组件的connect中能获取到store

### 四，异步action & middleware
#### 1，异步action
所谓异步action，即action creator不再像同步action creator那样返回一个object标识action，而是返回一个函数，函数参数是dispatch，在函数内部执行异步操作，并根据操作结果，dispatch同步action更新state。
#### 2，middleware（中间件）
提供位于 action 被发起之后，到达 reducer 之前的操作。redux生态中最常用的两个解决异步操作的middleware即：[redux-saga](https://redux-saga-in-chinese.js.org/)，[redux-thunk](https://github.com/reduxjs/redux-thunk)
- 关于redux-saga，[参考相关笔记](https://github.com/JonTang-front/web-note/blob/master/redux-saga.md)
- 关于redux-thunk，[参考相关笔记](https://github.com/JonTang-front/web-note/blob/master/redux-thunk.md)
### 五，实战
#### 1，redux & react-redux
**redux** 是一个独立的应用状态管理工具，独立于react之外，可以用于任何前端架构

**react-redux** 是用于连接react和redux的工具，为react提供自动订阅store状态变化的功能。如果不使用 react-redux，我们需要手动订阅store的状态变化，来对我们的react组件进行更新

==实际项目开发中，一般结合使用 redux 和 react-redux==
#### 2，安装
```
$ cnpm install redux react-redux redux-devtools-extension --save
//redux-devtools-extension 是 redux 调试工具，可以跟踪store的状态变化
//还需要Chrome相应安装 redux-devTools 插件，chrome应用商店无法访问时，可以从github上找工具源码打包（https://github.com/reduxjs/redux-devtools）
```
#### 3，创建store，reducer，action
src/redux目录下分别创建action.js，reducer.js，store.js
```
//action.js文件
/**
 * action 类型
 */
export const type = {
    SAYHELLO: 'SAYHELLO',
    TODO: 'TODO'
}

/**
 * action
 * @param {*} text 
 */
export function sayHello(text) {
    return {
        type: type.SAYHELLO,
        text
    }
}
export function todo(doSomething) {
    return {
        type: type.TODO,
        doSomething
    }
}
```

```
//reducer.js文件
/**
 * Reducer 数据处理
 */
import { combineReducers } from 'redux';
import { type } from './../action'
const sayHello = (state = {}, action) => {
    switch (action.type) {
        case type.SAYHELLO:
            return {
                ...state,
                text: action.text
            }
        default: return state;
    }
}

const todo = (state = {}, action) => {
    switch (action.type) {
        case type.SAYHELLO:
            return {
                ...state,
                doSomething: action.doSomething
            };
        default: return state;
    }
}
export default combineReducers({
    sayHello,
    todo
})
```
采用combineReducers进行reducer的分发，各个reducer接收的state参数只是Store state中的各自对应的部分，Store state中各状态模块名由combineReducers参数指定
```
//store.js文件
/**
 * 创建Store
 */
import { createStore } from 'redux';
import reducer from './../reducer';
import { composeWithDevTools } from 'redux-devtools-extension';

export default () => createStore(reducer, composeWithDevTools())
```
#### 4，在组件中使用
采用react-redux提供的connect连接react组件和redux状态数据。connect返回的函数相当于一个高阶组件（HOC）
```
import React, {Component} from 'react';
import { connect } from 'react-redux';
import { sayHello } from './../../redux/action';
class Home extends Component{
    componentDidMount() {
        this.props.onTextInit();
    }
    render() {
        return (
            <div className="home">
                Welcome to React ！！！
            </div>
        );
    }
}

const mapStateToProps = state => ({
    text: state.text
});

const mapDispatchToProps = dispatch => ({
    onTextInit: () => {
        dispatch(sayHello('hello, welcome to redux'))
    },
    onTextClick: () => {
        dispatch(sayHello('Store state clicked'))
    },
    onTextChange: () => {
        dispatch(sayHello('Store state changed'))
    }
})
export default connect(mapStateToProps, mapDispatchToProps)(Home);
```
1. mapStateToProps：把Store state中对应的状态数据映射到组件的props中。==参数是全局state，原则是返回最小范围的state数据，避免组件发生不必要的重新渲染==；
2. mapDispatchToProps：把dispatch映射到组件的props中。参数是dispatch
==mapDispatchToProps可以省略，dispatch会被connect以props参数形式传入组件，可以在组件中主动dispatch action==

## Mobx（响应式数据流）
