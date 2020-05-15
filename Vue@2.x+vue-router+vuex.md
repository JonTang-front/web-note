### 一，vue-cli2脚手架搭建vue项目
##### 1，安装webpack

```
cnpm install webpack -g
##查看webpack版本
webpack -v   
```
##### 2，安装vue-cli脚手架

```
cnpm install vue-cli -g    
##查看vue版本，V大写
vue -V    
```
##### 3，创建vue项目

```
vue init webpack <appname>
##webpack表示用webpack管理项目打包配置
##appname代表项目名称

##输入上面命令后，会询问几个选项：
Project name：项目名称，默认是初始化时输入的名称，直接回车
Project description：项目描述，默认是<a vue.js project>，直接回车
Author：作者，默认会读取git配置文件里面的用户名和邮箱，直接回车
Vue build：是否安装编译器，1，Runtime + Compiler:运行并安装编译器（推荐）；2，Runtime-only：只运行大约6KB比较轻量的压缩文件。直接回车选择安装编译器
install vue-router：是否安装vue的路由插件，直接回车
Use ESLint to lint your code?是否使用ESLint检测你的代码？（ESLint 是一个语法规则和代码风格的检查工具，可以用来保证写出语法正确、风格统一的代码。）直接回车
Setup unit tests? 是否安装单元测试，一般不安装
Setup e2e tests with Nightwatch：是否安装端到端测试工具，一般不安装
Should we run npm install for you after the project has been created? (recommended)：项目创建后是否要为你运行“npm install”安装项目依赖，直接回车确认
```
##### 4，启动vue-cli-service，运行项目

```
cd <appname>      
##进入项目目录
cnpm run dev	
```
##### 5，项目目录结构
![image](D:/note/youdao/1302947749@qq.com/6AC19324C7904F59BF799EFAE376F3EB/clipboard.png)


### 二、vue组件封装与引用
#### 单文件组件（局部注册）
局部注册组件不能在其子组件中使用，一般开发过程中都使用局部注册

```
<template>
    <html>html内容，单跟节点原则</html>
</template>
export default {
    data() {
        return {
            ...
        }
    },
    beforeCreate() {
        
    },
    created() {
        
    },
    components: {
        //注册子组件
    }
    ...
}
```
单文件组件嵌套使用：
1. 定义单文件组件.vue文件；
2. import引入单文件组件；
3. 父组件的components属性中注册组件

#### 自定义全局组件（全局注册）
全局组件注册后可以在任何地方使用
##### 1，创建组件vue文件

```
##如loading.vue
<template>
    <div>Loading...</div>
</template>
<script>
    export default {
        
    }
</script>
<style lang="scss" scoped>
</style>
```
##### 2，创建组件入口文件

```
##组件入口文件一般命名index.js

##在入口文件中引入组件
import Loading from './loading.vue'
export default {
    install: function(Vue){
        //Vue代表vue全局对象
        Vue.component('Cloading', Loading);
    }
}
```
##### 3，引用自定义全局组件

```
##一般在main.js入口文件中

##引入组件，loading表示组件目录，如果组件入口文件命名为index.js，引入时可以省略，默认引入该目录下的index.js文件，详情参考webpack配置；如果组件入口文件命名不是index.js，则需要写全入口文件路径
import Cloading from 'loading'
##vue使用
Vue.use(Cloading);
##vue调用use API的时候，实际上就是调用组件入口文件导出的对象install方法
```

#### Vue内置组件
###### 1，component
用于动态渲染组件，根据 is  的值，来决定哪个组件被渲染。常用于动态渲染标签页组件
```
<component :is="componentId"></component>
```
###### 2，transition
用于Vue组件动画
```
<transition name="fade" mode="out-in" appear>
  <component :is="view"></component>
</transition>
```
###### 3，transition-group
transition组件组
###### 4，keep-alive
包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们
```
<keep-alive>
  <component :is="view"></component>
</keep-alive>
```
###### 5，slot
元素作为组件模板之中的内容分发插槽，<slot> 元素自身将被替换
```
<div class="demo-alert-box">
     <slot></slot>
</div>
```

#### 开发积累

1. 常用的基础组件，例如input，button等应在应用入口文件main.js中全局注册，可以避免在组件文件中包含基础组件的长列表；
2. 引用第三方UI组件库时，往往按需加载，可以避免加载全部组件导致首页等待时间过长

### 三，vue过滤器
==vue2.0把系统自带的过滤器全部去掉了，可根据需要自己写全局或局部过滤器==
#### 自定义过滤器
##### 全局过滤器
全局过滤器注册后可以在任何地方使用

```
//1.src目录下新建filters目录，filters目录里新建index.js文件
//filters/index.js
export const formatMoney = (value) => {
  if (value) {
    value = Number(value);
    return '￥ ' + value.toFixed(2);
  }
};

//main.js
import {* as filters} from '@/filters';
Object.keys(filters).forEach(k => Vue.filter(k, filters[k]));

//全局指令也是一样
```
##### 组件内过滤器
只能在当前组件内使用
```
export default {
    template: '...'
    filters: {
        filter-name: function(){
            ...
        }
    }
}
```
##### 过滤器使用
管道符（|）

```
//单个过滤器
<div :id="rawId | filter1">{{t | filter2}}</div>
//多个过滤器
<div :id="rawId | filter1">{{t | filter2 | filter3}}</div>
//过滤器传参
<div :id="rawId | filter1">{{t | filter2 | filter3('arg1', arg2)}}</div>
```

#### 双向过滤器(不常用)

```
Vue.filter(name, {
    read: function(input){
        //model=>view
    }，
    write: function(val){
        //view=>model
    }
});
```
### 四，指令
#### Vue内置指令
1. v-text：更新元素的 textContent，与 {{textContent}} 功能一致；
2. v-html：更新元素的 innerHTML，如果innerHTML数据没加载，html元素也不会被渲染。不会出现页面渲染{{undefined}}的效果。切记：不要用该指令动态渲染html模板，会被当作普通字符串渲染，而不会作为 Vue 模板进行编译；
3. v-show：根据表达式之真假值，切换元素的 display CSS 属性，不管表达式的值是true还是false，元素都被加载到DOM树，只是被隐藏了，==一般用于频繁切换的组件==；
4. v-if：根据表达式之真假值，决定是否加载该元素到DOM树，如果表达式的值被修改，组件被销毁并重建，==一般用于不会频繁切换的组件==；
5. v-else：必须与v-if或者v-else-if配合使用；
6. v-for：基于源数据多次渲染元素或模板块；
7. v-on：缩写@，绑定事件监听器，表达式可以是一个方法的名字或一个内联语句；
8. v-bind：缩写:，动态地绑定一个或多个特性，或一个组件 prop 到表达式；
9. v-model：表单数据双向绑定；
10. v-slot：缩写#，插槽传入 prop 的时候使用，参数是插槽名，默认值是default；

#### 自定义指令
##### 1，属性指令
自定义指令为我们提供了几个钩子函数（均为可选项）：
```
bind: 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作。
inserted: 被绑定元素插入父节点时调用（仅保证父节点存在，但不一定被插入到DOM）。
update: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新
componentUpdated: 被绑定元素所在模板完成一次更新周期时调用。
unbind: 只调用一次， 指令与元素解绑时调用。
```
```
//1，directives/index.js
//钩子函数中常用的参数：
//el：所绑定的元素DOM节点；
//binding: 是一个对象，包含很多值，常用的就这么两个：name:指令名；value: 绑定的值，如：v-tableDrag='123', value就是123
//vnode: Vue编译生成的虚拟DOM节点
export default {
  install (Vue) {
    Vue.directive('directive1', {
        bind() {
            
        },
        inserted() {
            
        },
        update() {
            
        }
        ...
    }
    Vue.directive('directive2', {
        bind() {
            
        },
        inserted() {
            
        },
        update() {
            
        }
        ...
    }
    //bind 和 update 时触发相同行为，而不关心其它的钩子
    Vue.directive('directive3', function() {
            ...
        }
    }
}
//2，main.js
import directives from '@/directives/index'
Vue.use(directives)

##使用指令时必须   v- 开头，定义指令时直接写指令名称就好，不需要v-开头
```
##### 2，元素指令(不常用)，功能可以用组件代替

```
Vue.elementDirective(name, {
    bind: function(){
    
    }
});
##指令名称可以随意命名，相当于html标签使用，且使用时标签名称与命名名称一致，无需v-开头
一般用组件代替元素指令
```
##### 3，自定义键盘

```
Vue.config.keyCodes.ctrl = 17;
```

### 五，混入
本质是一个对象，用来分发 Vue 组件中的可复用功能，所以对象属性和方法只能是vue的属性和方法。
```
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}
// 定义一个使用混入对象的组件
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "hello from mixin!"
```
混入原则：
1. 数据对象data在内部会进行递归合并，并在发生冲突时以组件数据优先；
2. 同名钩子函数将合并为一个数组，都会被调用，且==混入对象的钩子将在组件自身钩子之前调用==；
3. methods、components、directives、filters等值为对象的选项将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对；

### 六，生命周期钩子

```
beforeCreate(){}    
##在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用;
create(){}    
##在实例创建完成后被立即调用，挂载阶段还没开始，除el属性不可见，其余属性均可见；
beforeMount(){}    
##在挂载开始之前被调用：相关的 render 函数首次被调用;
mounted(){}     
##el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用；
beforeUpdate(){}    
##虚拟DOM打补丁之前，即更新实际DOM之前被调用；
updated(){}     
##数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子;
beforeDestory(){}   
##实例销毁之前调用。在这一步，实例仍然完全可用，一般在此释放资源
destoryed(){}   
##Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁;

额外：
activated(){}    
##keep-alive 组件激活时调用；
deactivated(){}     
##keep-alive 组件停用时调用
```

### 七，组件之间通信
#### 父子组件间通信
##### 1，props和$.emit() 

```
##父组件通过props给子组件传递参数，子组件通过$.emit()通知父组件，并在父组件上绑定事件监听

##子组件
{
    props: ['param'],
    methods: {
        callback() {
            this.$emit('someEvent', param);
        }
    }
}
##父组件
<child param="data" @someEvent="someMethod"></child>
```

##### 2，$attrs和$listeners

主要用于解决祖父组件和孙子组件之间的通信问题

（1） $attrs：包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)，也就是说在子组件中没有通过props声明的父组件传递过来的参数都包含在$attrs中，并且可以通过 v-bind="$attrs" 传入内部组件（自己的子组件）

（2）$listeners：对属性相对应的，包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件
```
##孙子组件Grandson
{
    this.$attr.paramxxx
    this.$emit('someEvent', param)
}
##子组件Child
<Grandson v-bind="$attrs" v-on="$listeners"></Grandson>
##父组件Father
<Child :paramChild="paramChild" :paramGrandson="paramGrandson" @someEvent="someMethod"></Child>
```
##### 3，provide和inject

```
##父组件
{
    provide: {
        key: value
    }
}
##子组件
{
    inject: ['key']
}
##优势：不管组件嵌套层级有多深，只要在父组件中注入数据，在所有子组件中都可以通过inject获取数据
```
##### 4，$parent和$children

```
##父组件，$children表示当前组件的所有直接子组件，不保证顺序，也不是响应式
{
    this.$children[0].key = value;
}
##子组件，$parent表示当前组件的直接父组件
{
    this.$parent.key
}
```


#### 兄弟（包括非父子的其他关系组件）组件间通信
##### 1，props和$.emit()
```
##子传父，父传另一个子，不作介绍
```
##### 2，中央事件总线

```
##中央事件总线
let EventBus = new Vue();
##发送消息方
EventBus.$emit('someEvent', param);

##接收消息方
EventBus.$on('someEvent',(val)=>{
    this.brothermessage=val;
});
##回调函数参数代表数据
```
#### 万能滴解决组件间的通信：vuex

### 八，Vue动画
vue实现动画的方式分为下面几种：
1. 在 CSS 过渡和动画中自动应用 class，即用css3的过渡和动画自己定义动画效果；
2. 利用第三方 CSS 动画库，如Animate.css；
3. 在过渡钩子函数中使用 JavaScript 直接操作 DOM
4. 使用第三方 JavaScript 动画库，如 Velocity.js

#### transition组件实现vue动画原理
1. transition组件包裹目标元素或组件；
2. 自动嗅探目标元素是否应用了CSS过渡或动画，如果是，在恰当的时机添加/删除 CSS 类名；
3. 如果过渡组件提供了JavaScript钩子函数，这些钩子函数将在恰当的时机被调用；
4. 如果没有找到 JavaScript 钩子并且也没有检测到 CSS 过渡/动画，DOM 操作 (插入/删除) 在下一帧中立即执行。(注意：此指浏览器逐帧动画机制，和 Vue 的 nextTick 概念不同)。

#### 自定义css过渡&动画
1. v-enter：定义进入过渡的开始状态，元素插入之前到元素被插入后的下一帧生效；
2. v-enter-to：定义进入过渡的结束状态，元素插入后的下一帧到动画完成生效；
3. v-enter-active：v-enter + v-enter-to的完整过程；
4. v-leave: 定义离开过渡的开始状态，在离开过渡被触发到下一帧生效；
5. v-leave-to：定义离开过渡的结束状态，离开过渡下一帧到过渡/动画完成生效；
6. v-leave-active：v-leave + v-leave-to的完整过程

'v-'是这些类名的默认前缀，transition组件命名后（name），'v'会被组件名替换
##### 开发积累
1. 如果自己写过渡实现动画效果，一般只需要定义v-enter-active，v-leave-active运动状态，v-enter，v-leave-to初始状态和最终状态即可；
```
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
```
2. 如果自己写动画实现，一般只需要定义动画效果，然后实现v-enter-active，v-leave-active运动状态即可
```
.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-in .5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```
#### 引用第三方动画库（Animate.css）
enter-class
enter-active-class
enter-to-class (2.1.8+)
leave-class
leave-active-class
leave-to-class (2.1.8+)分别对应上面的6中状态类
##### 开发积累
一般添加enter-active-class和leave-active-class即可


### 九，关于new Vue
###### 1，单页面应用（SPA）只能有一个new Vue实例

```
new Vue({
    el: '#app',
    store,
    router,
    render: h => h(App)
});
```
###### 2、根vue实例特有el属性，组件实例不具有el属性
###### 3、根vue实例的data属性可以是对象，组件实例的data属性必须是function
因为组件可能被用来创建多个实例，如果不是函数，则这些实例共享同一份数据
以 _ 或 $ 开头的属性 不会 被 Vue 实例代理，因为它们可能和 Vue 内置的属性、API 方法冲突。你可以使用例如 vm.$data._property 的方式访问这些属性
###### 4、v-model只能用于表单元素的数据绑定，v-bind可以用于所有元素的属性（数据）绑定；
###### 5，优先级：render()>template>el

### 十，vue-router
##### 1，在vue中使用vue-router
###### （1），路由匹配规则

```
const routes = [{
    name: 路由名称,
    path: 匹配地址,
    component: 熏染组件,
    redirect: 重定向,
    children: [{
        name: 路由名称,
        path: 匹配地址,
        component: 熏染组件,
        redirect: 重定向
    }, 
    ...
    ]
}, 
....
];
```
###### （2），定义路由

```
export default new VueRouter({
    routes    //相当于routes：routes
});
```
###### （3），挂载到根组件

```
new Vue({
    el: '#app',
    store,
    router, // 注入到根实例中
    render: h => h(App)
});
```
##### 2，路由匹配
（1）动态路由匹配：路径参数采用 ":" 跟在路径后面 

```
/user/:username
##单个参数username
/user/:username/post/:post_id
##多个参数username，post_id
```
（2）通配符匹配和404匹配：由于路由的优先级是路由的定义顺序，即谁先定义的，谁的优先级就最高，所以通常通配符匹配放在路由规则的最后一项，表示以上所有路由均匹配不到时才匹配，==this.$route.params.pathMatch==的值是通配符代表的字段

##### 3，响应路由变化
（1）组件的watch监听路由变化
（2）组件beforeRouteUpdate守卫导航卫
##### 4，路由跳转方式
（1） 标签跳转

```
<router-link :to="..."></router-link>
##to表示跳转的路径，可以是字符串，也可以是对象
```
（2）编程式跳转

```
this.$router.push({
    path:跳转路由地址,
    query: {
        name：value
    }
});
##OR
this.$router.push({
    name:跳转路由名称,
    params: {
        name：value
    }
});

##如果采用params传参，跳转路由地址需由路由名称name指出，标签跳转也遵循这个规则
```
（3）接收参数
```
var query = this.$route.query;
##OR
var param = this.$route.params;
```

##### 5，导航守卫——主要用来通过跳转或取消的方式守卫导航
（1）全局守卫

```
const router = new VueRouter({ ... })；
router.beforeEach((to, from, next) => {
  // ...
});
##全局前置守卫，一般用于用户登陆验证

router.afterEach((to, from) => {
  // ...
});
##全局后置守卫，一般用户资源回收
```
（2）路由守卫

```
routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
];
```
（3）组件内部导航守卫

```
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建，不过，你可以通过传一个回调给 next来访问组件实例
    next(vm => {
        // 通过 `vm` 访问组件实例
     });
    //注意 beforeRouteEnter 是支持给 next 传递回调的唯一守卫。对于 beforeRouteUpdate 和 beforeRouteLeave 来说，this 已经可用了，所以不支持传递回调，因为没有必要了。
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时								候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}

##记住参数或查询的改变并不会触发进入/离开的导航守卫。你可以通过观察 $route 对象来应对这些变化，或使用 beforeRouteUpdate 的组件内守卫
```
==注：使用任何导航守卫必须手动调用next()回调才会发生路由跳转，next()默认参数是to指定的路由，可以传入参数修改跳转路由==

##### 6，路由懒加载
路由懒加载的本质是在需要的时候才从服务器加载一个模块，而不是在启动应用的时候就加载所有模块。懒加载有利于缩短应用的启动时间，增强用户体验
（1）vue异步组件实现路由懒加载（异步组件常用）
　　
```
component：resolve => (['需要加载的路由的地址'，resolve])
```

（2）ES6提出的import(推荐使用这种方式)
　　
```
const HelloWorld = () => import('需要加载的模块地址')
```

实现本质都是返回一个Promise，需要被渲染的时候才执行这个Promise函数返回组件实例

### 十一，关于vuex
1. state，存储组件状态，组件中获取状态值：this.$store.state.key
若store被分割成多个模块，获取状态值：this.$store.state.[模块名].key
辅助函数获取状态，一般用于组件的data属性中：...mapState(['key'])
2. getters，相当于store的计算属性，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算，
一般在组件的计算属性中：...mapGetters(['key'])
3. mutation，更改 Vuex 的 store 中的状态的唯一方法是提交 mutation，且mutation是同步操作，任何异步操作在mutation中都无法完成。
4. actions，修改store中的state值，通过store.dispatch action，action提交mutation修改state的值，与mutation不同的是，action支持异步操作，一般异步请求都在action中完成，通常用...mapActions与组件的methods绑定，实现数据vuex存储；

```
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    test: window.sessionStorage.getItem('test') || ''
  },
  getters: {
    test: state => state.test
  }
  mutations: {
    insertTest(state, test){
        window.sessionStorage.setItem('test', test);
        state.test = test;
    }
  },
  actions: {
    INSERTTEST({commit}, test){
        commit('insertTest', test);
    }
  },
});

##一般会创建一个mutations-type.js文件定义所有的mutation类型

##分模块
export default Vuex.Store({
  modules: {
    module1,
    module2,
    ...
  }
});

##在组件中触发action修改store中的state值
this.$store.dispatch('INSERTTEST', val);

##在组件中获取store中state的值
this.$store.state.[module-name].[key-name]
## OR
...mapGetters([
      'key1',
      'key2',
    // ...
]); //一般用在组件的计算属性computed中
```

==注：在组件的computed计算属性中引用veux状态时，state状态节点更新时，组件的computed属性跟着更新，千万不要试图直接修改整个store，computed不会随之更新==

### 十二，跨域访问代理
基本原理：在浏览器与服务器之前设置一个中间代理，所有浏览器发送出去的请求，只要能在代理中匹配到，都由代理进行访问地址变换，然后进行转发。从服务端返回的数据也是一样。

通过vue-cli脚手架生成的项目中会有build，config这两个文件夹，在config目录下有dev.env.js，prod.env.js和index.js三个文件，分别表示开发环境配置，生产环境配置以及配置文件入口。

在config/index.js文件的proxyTable进行代理配置。例：

```
'/application': {
    target: 'http://47.103.73.10:8801',
    secure: false,
    changeOrigin: true,
    pathRewrite: {
        '^/application': ''
    }   
}
```
所有以‘/application’开头的请求都会被代理替换成target对应的内容，changeOrigin表示是否跨域，pathRewrite表示将自己添加的‘/application’标识去掉。如果接口本身包含有‘applicaiton’字段，则不需要pathRewrite

### 十三，diff算法简介

vue的diff位于patch.js文件中，复杂度为O(n)，即从根节点到叶子节点遍历对比。diff的过程就是调用名为patch的函数，比较新旧节点，一边比较一边给真实的DOM打补丁。
###### 1，虚拟dom（virtual DOM）
virtual DOM是将真实的DOM的数据抽取出来，以对象的形式模拟树形结构

```
<div>
    <p>123</p>
</div>
##对应的virtual DOM（伪代码）：
var Vnode = {
    tag: 'div',
    children: [
        { tag: 'p', text: '123' }
    ]
};
```
###### 2，diff比较
在采取diff算法比较新旧节点的时候，比较只会在同层级进行, 不会跨层级比较。当数据发生改变时，set方法会让调用Dep.notify通知所有订阅者Watcher，订阅者就会调用patch给真实的DOM打补丁，更新相应的视图。
###### 3，元素key的必要性
不设key，Vnode和oldVnode只会进行头尾两端以及元素个数的相互比较，设key后，除了头尾两端的比较外，还会从用key生成的对象oldKeyToIdx中查找匹配的节点，所以为节点设置key可以更高效的利用dom。


### 十四，vue常用组件库
1. PC端： [elementUI](https://element.eleme.cn/#/zh-CN/component/installation)， [iview](https://iviewui.com/)
2. 移动端：[mintui](http://mint-ui.github.io/#!/zh-cn)，[vux](https://vux.li/)，[vonic](https://wangdahoo.github.io/vonic/docs)，[vant](https://youzan.github.io/vant/#/zh-CN/card)等
3. 移动端适配：[flexible手淘框架](https://github.com/amfe/article/issues/17)+rem，viewport + postcss-px-to-viewport
4. 移动端事件：[vue-touchjs](https://www.npmjs.com/package/vue-touchjs)
