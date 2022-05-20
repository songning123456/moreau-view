#### vue-cli工作中如何自定义一个过滤器？
文件结构：


```
.
├── src
│   ├── filter
│   │    ├── filter.js
│   │    
│   └── main.js
└── ...
```


过滤器们放在filter文件夹下。


**filter/filter.js：**这个文件主要是写了过滤器实现的方法，然后export进行导出。


```javascript
function filterOne(n){
    return n + 10;
}
function filterTwo(n){
    return n + 5;
}
export{
    filterOne,
    filterTwo
}
```


**main.js：**


```javascript
// 找 filter/filter.js
import * as filters from './filter/filter.js';
//遍历所有导出的过滤器并添加到全局过滤器
Object.keys(filters).forEach((key) => {
    Vue.filter(key, filters[key]);
})
```


在 .vue 组件下使用：


```javascript
{{test | filterOne}}
```


#### vue-cli工作中常用的过滤器？


在.vue组件中的使用示例：


```javascript
{{ string | trim(1) }} //去除所有空格，过滤器第一个参数为value
```


常用的过滤器，filter/filter.js：


```javascript
//去除空格  type 1-所有空格  2-前后空格  3-前空格 4-后空格
function trim(value, trim) {
    switch (trim) {
        case 1:
            return value.replace(/\s+/g, "");
        case 2:
            return value.replace(/(^\s*)|(\s*$)/g, "");
        case 3:
            return value.replace(/(^\s*)/g, "");
        case 4:
            return value.replace(/(\s*$)/g, "");
        default:
            return value;
    }
}
//字符串循环复制,count->次数
function repeatStr(str, count) {
    let text = '';
    for (let i = 0; i < count; i++) {
        text += str;
    }
    return text;
}
//字符串替换
function replaceAll(str, AFindText, ARepText) {
    raRegExp = new RegExp(AFindText, "g");
    return str.replace(raRegExp, ARepText);
}
```


#### Vue等单页面应用及其优缺点？
**单页Web应用(single page web application，SPA)：**


就是只有一张Web页面的应用。单页应用程序(SPA)是加载单个HTML页面并在用户与应用程序交互时动态更新该页面的Web应用程序。浏览器一开始会加载必需的HTML、CSS和JavaScript，所有的操作都在这张页面上完成，都由JavaScript来控制。因此，对单页应用来说模块化的开发和设计显得相当重要。


**单页Web应用的优点：**


1. 提供了更加吸引人的用户体验：具有桌面应用的即时性、网站的可移植性和可访问性；
2. 单页应用的内容的改变不需要重新加载整个页面，web应用更具响应性和更令人着迷；
3. 单页应用没有页面之间的切换，就不会出现“白屏现象”,也不会出现假死并有“闪烁”现象；
4. 单页应用相对服务器压力小，服务器只用出数据就可以，不用管展示逻辑和页面合成，吞吐能力会提高几倍；
5. 良好的前后端分离。后端不再负责模板渲染、输出页面工作，后端API通用化，即同一套后端程序代码，不用修改就可以用于Web界面、手机、平板等多种客户端。


**单页Web应用的缺点**


1. 首次加载耗时比较多；
2. SEO问题，不利于百度，360等搜索引擎收录；
3. 容易造成Css命名冲突；
4. 前进、后退、地址栏、书签等，都需要程序进行管理，页面的复杂度很高，需要一定的技能水平和开发成本高。


#### 什么是Vue的计算属性？
**计算属性：**模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。例如：message.split('').reverse().join('')。


在这个地方，模板不再是简单的声明式逻辑。你必须看一段时间才能意识到，这里是想要显示变量message的翻转字符串。当你想要在模板中多次引用此处的翻转字符串时，就会更加难以处理。所以，对于任何复杂逻辑，都应当使用**计算属性**。


````html
<div id="example">
    <p>Original message: "{{ message }}"</p>
    <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
````


```javascript
var vm = new Vue({
    el: '#example',
    data: {
        message: 'Hello'
    },
    computed: {
    // 计算属性的 getter
        reversedMessage: function () {
          // `this` 指向 vm 实例
            return this.message.split('').reverse().join('')
        }
    }
})
```


#### vue-cli提供的几种脚手架模板？
在使用之前，可以先用vue-list命令查询可用的模板。


![vue-list](/images/Web/vue-list.jpeg)


| 脚手架模板 | 解释 | 
| :----- | :----- | 
|<div style='width:140px'>webpack</div>|基于webpack和vue-loader的目录结构，而且支持热部署、代码检查、测试及css抽取。|
|<div style='width:140px'>webpack-simple</div>|基于webpack和vue-loader的目录结构。|
|<div style='width:140px'>browerify</div>|基于Browerfiy和vueify(作用于vue-loader类似)的结构，支持热部署、代码检查及单元测试。|
|<div style='width:140px'>browerify-simple</div>|基于Browerfiy和vueify的结构。|
|<div style='width:140px'>simple</div>|单个引入Vue.js的index.html页面。|


这里我们主要会使用webpack作为常用脚手架，可以运行<span class='forest-green'>vue init webpack my-project</span>来生成项目。


#### 如何在组件中使用全局常量？
1.在src下新建const文件夹下新建const.js：


```
.
├── src
│   ├── const
│   │    ├── const.js
│   │    
│   └── main.js
└── ...
```


2.如何在const.js文件下，设置常量：


```javascript
export default {
    install(Vue, options){
        Vue.prototype.global = {
            title:'全局',
            isBack: true,
            isAdd:  false,
        };
    }
 }
```


3.在main.js下全局引入：


```javascript
//引入全局常量
import constant from './const/const.js';
Vue.use(constant);
```


4.即可在.vue组件中使用：


```javascript
//通过js方式使用：
this.global.title;
//或在 html 结构中使用
{{global.title}}
```


#### 在JS中使用全局常量？
1.在src下新建const文件夹下新建type.js：


```
.
├── src
│   ├── const
│   │    ├── type.js
│   │    
│   └── main.js
└── ...
```


2.在type.js文件下，设置常量：


```javascript
export const TEST_INCREMENT='TEST_INCREMENT';
export const TEST_DEREMENT='TEST_DEREMENT';
```


3.在其他.js文件下引入并使用：


```javascript
//以对象的形式引入：
import * as types from '../types';
//使用：
types.TEST_INCREMENT;
```


#### 如何定义一个常量，允许项目打包后，修改[hash].js里面的值？
1.在static下新建config.js：


```
.
├── 项目路径
│   ├── static
│   │___├── config.js
```


2.在config.js里面设置全局变量：


```javascript
window.g = {
    PUBLIC_IP  : "http://10.10.10.10:8080"
}
```


3.在index.html里面引入：


```html
<script type="text/javascript" src="./static/config.js"></script>
```


4.在其他.js文件中即可使用：


```javascript
window.g.PUBLIC_IP;
```


5.打包后修改：


通过**npm run build**命令打包后，此config.js文件会被打包到dist/static文件夹下，此时如果需要修改PUBLIC_IP，打开config.js即可修改，无需重新打包！


#### Vue如何禁止弹窗后面的滚动条滚动？


```html
methods : {
   //禁止滚动
    stop() {
        var mo = function(e) {e.preventDefault();};
        document.body.style.overflow='hidden';
        document.addEventListener("touchmove",mo,false); //禁止页面滑动
    },
    /***取消滑动限制***/
    move() {
        var mo = function(e) {e.preventDefault();};
        document.body.style.overflow=''; //出现滚动条
        document.removeEventListener("touchmove",mo,false);
    }
}
```


#### 请说出计算属性的缓存和方法调用的有什么区别？
1.计算属性必须返回结果；<br>
2.计算属性是基于它的依赖缓存的。一个计算属性所依赖的数据发生变化时，它才会重新取值；<br>
3.使用计算属性还是methods取决于是否需要缓存，当遍历大数组和做大量计算时，应当使用计算属性，除非你不希望得到缓存；<br>
4.计算属性是根据依赖自动执行的，methods需要事件调用。


#### 什么是vue.js中的自定义指令？
自定义一些指令对底层DOM进行操作。Vue里面有许多内置的指令，比如v-if和v-show，这些丰富的指令能满足我们的绝大部分业务需求，不过在需要一些特殊功能时，我们仍然希望对DOM进行底层的操作，这时就要用到自定义指令。


#### 自定义指令的几个钩子函数？
| 名称 | 解释 | 
| :----- | :----- | 
|bind|只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。|
|inserted|被绑定元素插入父节点时调用(仅保证父节点存在，但不一定已被插入文档中)。|
|update|所在组件的VNode更新时调用，但是可能发生在其子VNode更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新。|
|componentUpdated|指令所在组件的VNode及其子VNode全部更新后调用。|
|unbind|只调用一次，指令与元素解绑时调用。|


#### 钩子函数参数？
除了el之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过元素的dataset来进行。


指令钩子函数会被传入以下参数：


| 名称 | 解释 | 
| :----- | :----- | 
|el|指令所绑定的元素，可以用来直接操作DOM。|
|binding|一个对象，包含以下属性：<br><span class='forest-green'>name：</span>指令名，不包括v-前缀。<br><span class='forest-green'>value：</span>指令的绑定值，例如：v-my-directive="1 + 1"中，绑定值为2。<br><span class='forest-green'>oldValue：</span>指令绑定的前一个值，仅在update和componentUpdated钩子中可用，无论值是否改变都可用。<br><span class='forest-green'>expression：</span>字符串形式的指令表达式。例如v-my-directive="1 + 1"中，表达式为"1 + 1"。<br><span class='forest-green'>arg：</span>传给指令的参数，可选。例如v-my-directive:foo中，参数为"foo"。<br><span class='forest-green'>modifiers：</span>一个包含修饰符的对象。例如：v-my-directive.foo.bar中，修饰符对象为{foo: true, bar: true}。|
|vnode|Vue编译生成的虚拟节点。|
|oldVnode|上一个虚拟节点，仅在update和componentUpdated钩子中可用。|


#### 如何在vue-cli中使用自定义指令？
文件结构：


```
.
├── src
│   ├── directives
│   │    ├── index.js
│   │    ├── modules
│   └── main.js
└── ...
```


在modules下新建foucs.js下：


```javascript
// 聚焦指令
export default {
    bind (el, binding, vnode) {},
    inserted (el, binding, vnode) {
        el.focus()
    },
    update (el, binding, vnode) {},
    componentUpdated (el, binding, vnode) {},
    unbind (el, binding, vnode) {}
}
```


在src/directives/index.js下：


```javascript
import focus from './modules/focus';
export {focus}
```


在src/main.js下，使用directives自定义指令：


```javascript
//引入自定义指令
import * as directives from './directives';
//注册指令
Object.keys(directives).forEach(k => Vue.directive(k, directives[k]));
```


在.vue组件中使用：


```html
<input v-focus type="text" />
```


#### 父组件异步获取动态数据传递给子组件？
例如，在一个index.vue组件里面有一个content.vue子组件，需要在http请求数据返回后，将list数据传递给content.vue子组件。利用v-if可在http请求返回后再显示。这样子组件可以返回的http请求数据。


```html
<template>
	<div class="indexWrap">
        <content :list="list" v-if="isBack"></content>
	</div>
</template>
<script type="text/javascript">
    import content from '../components/content.vue';
	export default{
		data () {
			return {
				index:0,
                list:"",
                isBack:false
			}
		},
		components : {
            content
		},
	}
</script>
```


#### Vue开发命令npm run dev输入后的执行过程？
首先来看执行npm run dev时候最先执行的build/dev-server.js文件，该文件主要完成下面几件事情：<br>
1. 检查node和npm的版本、引入相关插件和配置；<br>
2. webpack对源码进行编译打包并返回compiler对象；<br>
3. 创建express服务器；<br>
4. 配置开发中间件(webpack-dev-middleware)和热重载中间件(webpack-hot-middleware)；<br>
5. 挂载代理服务和中间件；<br>
6. 配置静态资源；<br>
7. 启动服务器监听特定端口(8080)；<br>
8. 自动打开浏览器并打开特定网址(localhost:8080)。