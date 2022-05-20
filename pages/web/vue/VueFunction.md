#### Vue中如何实现tab切换功能？
遇到此问题，一定要先回答前两种方式，再提使用第三方UI库实现。


在Vue中，实现Tab切换主要有三种方式：<br>
1. 使用component动态组件实现Tab切换(推荐移动端使用)；<br>
2. 使用vue-router路由配合<router-view></router-view>标签实现；<br>
3. 使用第三方插件。


**使用component动态组件实现Tab切换：**


动态组件component：渲染一个“元组件”为动态组件。依is的属性值，来决定哪个组件被渲染。


示例：


```html
<template>
    <div>
        <tab>
            <tab-item selected @on-item-click="onItemClick">Tab标签一</tab-item>
            <tab-item @on-item-click="onItemClick">Tab标签二</tab-item>
            <tab-item @on-item-click="onItemClick">Tab标签三</tab-item>
        </tab>
        <component :is="currentView"></component>
    </div>
</template>
<script type="text/javascript">
	import { Tab, TabItem } from 'vux'
    import initOne from '@/components/example/initOne.vue';
    import initTwo from '@/components/example/initTwo.vue';
    import initThree from '@/components/example/initThree.vue';
	export default{
		data () {
			return {
				currentView:"",
			}
		},
		components : {
			Tab,
			TabItem,
			initOne,
            initTwo,
            initThree
		},
		mounted(){
			 this.currentView = "initOne";
		},
		methods : {
			onItemClick (index) {
		        if(index==0){
		         //Tab切换一
		         this.currentView = "initOne";
		        }
		        if(index==1){
		         //Tab切换二
		         this.currentView = "initTwo";
		        }
		        if(index==2){
		         //Tab切换三
		         this.currentView = "initThree";
		        }
			}
		}
	}
</script>
<style type="text/css">
	
</style>
```


**通过`<router-view></router-view>`标签和vue-router子路由配合实现Tab切换：**


示例：


```html
<!-- account.vue文件： -->
<template> 
    <div id="account"> 
        <p class="tab"> 
            <!-- 使用 router-link 组件来导航. -->
            <!-- 通过传入 `to` 属性指定链接. -->
            <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
            <router-link to="/account/course">我的课程</router-link> 
            <!-- 注意这里的路径，course和order是account的子路由 -->
            <router-link to="/account/order">我的订单</router-link> 
        </p> 
        <!-- 路由出口 -->
        <!-- 路由匹配到的组件将渲染在这里 -->
        <router-view></router-view> 
    </div> 
</template>
```


```javascript
// 在src/router/index.js：
import account from '../page/demo/account.vue';
import course from '../page/demo/course.vue';
import order from '../page/demo/order.vue';

export default new Router({
    //路由配置
    routes: [
        { 
            path: '/account', 
            name: 'account', 
            component: Account, 
            children: [ 
                {name: 'course', path: 'course', component: course}, 
                {name: 'order', path: 'order', component: order} 
            ] 
        } 
    ]
})
```


#### Vue中如何利用keep-alive标签实现某个组件缓存功能？
vue-cli工程中实现某个组件的缓存功能，可用`<keep-alive>`标签，与vue-router的meta形式数据传递配合完成。


示例：


```html
<!-- 在app.vue里面template部分使用<keep-alive>组件： -->
<template>
<div id="app">
    <keep-alive>
        <router-view v-if="$route.meta.keepAlive"></router-view>
    </keep-alive>
    <router-view v-if="!$route.meta.keepAlive"></router-view>
</div>
</template>
```


```javascript
// 在src/router/index.js：
import account from '../page/demo/account.vue';
import course from '../page/demo/course.vue';

export default new Router({
    //路由配置
    routes: [
        { 
            path: '/account', 
            name: 'account', 
            component: Account, 
            meta:{
                keepAlive:false //false为不缓存
            }
        },
        { 
            path: '/course', 
            name: 'course', 
            component: course, 
            meta:{
                keepAlive:true //true为缓存
            }
        } 
    ]
})
```


#### Vue中实现切换页面时为左滑出效果？
左滑效果实现，需要使用`<transition></transition>`组件配合css3动画效果实现。


示例：


```html
<!-- 在app.vue里面设置 -->
<template>
    <div id="app">
        <!-- 使用transition来规定页面切换时候的样式-->
        <transition name="slide-left">
            <router-view></router-view>
        </transition> 
    </div>
</template>
<script>
export default {
    name: 'app',
    mounted(){
    },
    data () { 
        return {
            index:0
        }
    },
    methods :{
    }
}
</script>
<style lang="less">
    /*左滑动效*/
    .slide-left-enter-active {
        animation: slideLeft 0.3s;
    }
    /****自定义动画**/
    @keyframes slideLeft {
        from {
            transform: translate3d(100%, 0, 0);/*横坐标,纵坐标,z坐标*/
            visibility: visible;
        }
        to {
            transform: translate3d(0, 0, 0);
        }
    }
</style>
```


#### 在vue-cli工程中如何实现无痕刷新？
一般常用的两种刷新方法：<br>
1. window.location.reload()，原生JS提供的方法；<br>
2. this.$router.go(0)，Vue路由里面的一种方法。


这两种方法都可以达到页面刷新的目的，简单粗暴，但是用户体验不好，相当于按F5刷新页面，页面的重新载入，会有短暂的白屏。


**无痕刷新**


先在全局组件注册一个方法，用该方法控制router-view的显示与否，然后在子组件调用：<br>
1. v-if控制`<router-view></router-view>`的显示；<br>
2. provide：全局注册方法；<br>
3. methods：设置reload方法。


```html
<!-- 在app.vue里面设置 -->
<template>
    <div id="app">
        <router-view v-if="isRouterAlive"></router-view>
    </div>
</template>
<script>
export  default {
    provide(){
        return{
            reload: this.reload
        }
    },
    data(){
        return {
            isRouterAlive: true
        }
    },
    methods:{
        reload(){
            this.isRouterAlive = false;
            this.$nextTick(function() {
                this.isRouterAlive = true;
            })
        }
    }
}
</script>
```


```html
<!-- 在.vue组件中使用，先用inject注册，然后即可通过this调用 -->
<script>
export  default{
    inject:['reload'],
    mounted(){
        this.reload();
    }
}
</script>
```
