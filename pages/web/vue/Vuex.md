#### 什么是vuex？
Vuex是一个专为Vue.js应用程序开发的状态管理器，采用集中式存储管理应用的所有组件的状态。


Vuex是一个专为Vue.js应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。


有了vue-router，可以将一个组件视为一个页面来使用。由于组件只维护自身的状态(data)，组件创建时或者说进入路由时它们被初始化，切换至其他的组件页时当前页自然要被销毁，从而导致data也随之销毁。页面与页面之间总会产生各种需要的共享变量，如果通过$router.param或者$router.meta来传递是远远不够的，很多情况下不得不采用window来保存一些全局的共享变量。这样出现的问题：Vue是不会维护window的这些共享变量的。对于组件来讲，这些变量都存在于组件作用域以外，组件并不会自动维护，这样就违背了js编程规范或者风格规范的一条基本准则：全局变量是毒瘤，是具有极高副作用的。当我们将window内的对象绑定到不同的自定义组件内，一旦要对window内的变量进行修改，会发现所有以对象方式绑定的自定义组件，当对象内的某个属性发生改变时将不会执行自动刷新，所有的计算属性也同时失效！更诡异的是这种情况并不是绝对出现的，当页面元素相对简单的时候一切都显得很正常，一旦页面元素增多，对应的交互操作增多时，这种奇怪的现象就会发送。Vuex就是专门解决页面与页面之间需要的共享变量的创建、维护、变更问题的。


#### 使用Vuex的核心概念？
每一个Vuex应用的核心就是store(仓库)。“store”基本上就是一个容器，它包含着你的应用中大部分的状态(state)。


Vuex和单纯的全局对象有以下**两点不同**：(重点)<br>
1. Vuex的状态存储是响应式的。当Vue组件从store中读取状态的时候，若store中的状态发生变化，那么相应的组件也会相应地得到高效更新。<br>
2. 你不能直接改变store中的状态。改变store中的状态的唯一途径就是显式地提交(commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。


**Vuex的核心概念和核心概念图：**


![vuex](/images/Web/vuex.png)


| 名称 | 解释 | 
| :----- | :----- | 
|state|Vuex store实例的根状态对象，用于定义共享的状态变量。|
|Action|动作，向store发出调用通知，执行本地或者远端的某一个操作(可以理解为store的methods)。|
|Mutations|修改器，它只用于修改state中定义的状态变量。|
|getter|读取器，外部程序通过它获取变量的具体值，或者在取值前做一些计算(可以认为是store的计算属性)。|


#### Vuex在vue-cli中的应用？
1.npm下载vuex资源包：


```html
npm install vuex --save
```


2.在src/main.js中引入：


```javascript
import Vue from 'vue';
import Vuex from 'vuex';
Vue.use(Vuex);
import store from './vuex/store';
```


3.在src下新建vuex文件夹：


```
vuex下:
	* modules //文件夹，存放不同模块需要的共享状态文件
		*	 index.js
		*	 等
	*store.js
    *types.js
```


**说明**


vuex文件夹下store.js：


```javascript
import Vue from 'vue';
import Vuex from 'vuex';
//引入不同模块需要的共享变量：
import index from './modules/index';
//使用vuex
Vue.use(Vuex);
//对外暴露
export default new Vuex.Store({
    modules: {
       index
    }
})
```


vuex文件夹下modules文件夹下index.js：


```javascript
//引入一个常量，保证 action 和 mutations 的统一。
import * as types from '../types';
/**
 * App通用配置
 */
const state = {
	//vuex初始化值
    count : 0
};
const actions = {
    increment({ commit }, n){
        commit(types.TEST_INCREMENT, n)
    },
    decrement({ commit }, state){
        commit(types.TEST_DEREMENT, state)
    }
};
const getters = {
    count: state => state.count
};
const mutations = {
    [types.TEST_INCREMENT](state, n) {
        console.log(n);
        state.count = state.count + 5 + n
    },
    [types.TEST_DEREMENT](state, status) {
        state.count = state.count - 3
    }
};
export default {
    state,
    actions,
    getters,
    mutations
};
```


vuex文件夹下type.js：


```javascript
//暴露常量
export const TEST_INCREMENT='TEST_INCREMENT';
export const TEST_DEREMENT='TEST_DEREMENT';
```


#### 组件中使用Vuex的值和修改值的地方？
**直接获取、修改：**


```javascript
//state
this.$store.state.count;
//getter
this.$store.getters.count;
//调用 action 修改 state 值，不带参数
this.$store.dispatch('increment');
//调用 action 修改 state 值，带参数
this.$store.dispatch('increment',{value :123});
```


**通过辅助函数获取、修改Vuex：**


```
vuex提供了三种辅助函数用于获取、修改vuex：
mapState、mapGetters、mapActions
即将vuex的变量或者方法映射到vue组件this指针上。
```


**获取共享变量使用：**


```html
//使用state获取共享变量
<script type="text/javascript">
import { mapState } from 'vuex';
export default{
    computed : {
        ...mapState([
            'count',
            'buttonShow'
        ])
    }
}
</script>
```


```html
//使用getters获取共享变量，
<script type="text/javascript">
import { mapGetters } from 'vuex';
export default{
    computed : {
        ...mapGetters([
            'count',
            'buttonShow'
        ])
    }
}
</script>
```


```html
//使用actions修改共享变量
<script type="text/javascript">
import { mapActions } from 'vuex';
export default{
    methods : {
    	...mapActions({increment:'increment',decrement:'decrement'}),
    }
}
</script>
```


```html
通过map获取到的变量或者方法，可通过this直接使用或者调用。
```


#### 如何在Vuex中使用异步修改？
在调用Vuex中的方法action的时候，用promise实现异步修改。


```javascript
const actions = {
    asyncInCrement({ commit }, n){
        return new Promise(resolve => {
            setTimeout(() => {
                commit(types.TEST_INCREMENT, n);
                resolve();
            },3000)
        })
    }
};
```