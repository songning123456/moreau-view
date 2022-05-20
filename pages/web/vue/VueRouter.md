#### vue-router如何响应路由参数的变化？
当使用路由参数时，例如从“/content?id=1”到“content?id=2”，此时原来的组件实例会被复用。这也意味着组件的生命周期钩子不会再被调用，此时Vue应该如何响应路由参数的变化？


复用组件时，想对路由参数的变化作出响应的话，可以watch(监测变化)$route对象：


```javascript
const User = {
    template: '...',
    watch: {
        '$route' (to, from) {
            // 对路由变化作出响应...
        }
    }
}
```


或者使用beforeRouteUpdate守卫：


```javascript
const User = {
    template: '...',
    beforeRouteUpdate (to, from, next) {
        // react to route changes...
        // don't forget to call next()
    }
}
```


#### 完整的vue-router导航解析流程？
1.导航被触发；<br>
2.在失活的组件里调用离开守卫；<br>
3.调用全局的beforeEach守卫；<br>
4.在重用的组件里调用beforeRouteUpdate守卫(2.2+)；<br>
5.在路由配置里调用beforeEnter；<br>
6.解析异步路由组件；<br>
7.在被激活的组件里调用beforeRouteEnter；<br>
8.调用全局的beforeResole守卫(2.5+)；<br>
9.导航被确认；<br>
10.调用全局的afterEach钩子；<br>
11.触发DOM更新；<br>
12.用创建好的实例调用beforeRouteEnter守卫中传给next的回调函数。


#### vue-router有哪几种导航钩子(导航守卫)？
| 名称 | 解释 | 
| :----- | :----- | 
|全局守卫|router.beforeEach|
|全局解析守卫|router.beforeResolve|
|全局后置钩子|router.afterEach|
|路由独享的守卫|beforeEnter|
|组件内的守卫|beforeRouteEnter、beforeRouteUpdate(2.2新增)、beforeRouteLeave|


导航表示路由正在发生改变，vue-router提供的导航守卫主要用来：通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的、单个路由独享的、或者组件级的。


注意：参数或查询的改变并不会触发进入/离开的导航守卫。你可以通过观察$route对象来应对这些变化，或使用beforeRouteUpdate的组件内守卫。


**全局守卫**


使用router.beforeEach注册一个全局前置守卫：


```javascript
const router = new VueRouter({ ... });
router.beforeEach((to, from, next) => {
// ...
})
```


当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫resolve完之前一直处于等待中。每个守卫方法接收三个参数：


| 参数 | 解释 | 
| :----- | :----- |
|<div style='width: 80px'>to</div>|Route即将要进入的目标路由对象。|
|<div style='width: 80px'>from</div>|Route当前导航正要离开的路由。|
|<div style='width: 80px'>next</div>| Function一定要调用该方法来resolve这个钩子，执行效果依赖next方法的调用参数。<br><span class='forest-green'>next()</span>: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是confirmed(确认的)；<br><span class='forest-green'>next(false)</span>: 中断当前的导航。如果浏览器的 URL 改变了(可能是用户手动或者浏览器后退按钮)，那么URL地址会重置到from路由对应的地址；<br><span class='forest-green'>next('/')或者next({ path: '/' })</span>: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向next传递任意位置对象，且允许设置诸如replace: true、name: 'home'之类的选项以及任何用在router-link的to prop或router.push中的选项；<br><span class='forest-green'>next(error)</span>: (2.4.0+)如果传入next的参数是一个Error实例，则导航会被终止且该错误会被传递给router.onError()注册过的回调。| 


确保要调用<span class='forest-green'>next方法</span>，否则钩子就不会被resolved。


**全局解析守卫**


2.5.0新增。在2.5.0+你可以用router.beforeResolve注册一个全局守卫。这和router.beforeEach类似，区别是在导航被确认之前，同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被调用。


**全局后置钩子**


你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受next函数也不会改变导航本身：


```javascript
router.afterEach((to, from) => {
    // ...
})
```


**路由独享的守卫**


你可以在路由配置上直接定义beforeEnter守卫：


```javascript
const router = new VueRouter({
    routes: [
        {
            path: '/foo',
            component: Foo,
            beforeEnter: (to, from, next) => {
                // ...
            }
        }
    ]
})
```


这些守卫与全局前置守卫的方法参数是一样的。


**组件内的守卫**


最后，你可以在路由组件内直接定义以下路由导航守卫：beforeRouteEnter、beforeRouteUpdate(2.2新增)、beforeRouteLeave。


```javascript
const Foo = {
    template: `...`,
    beforeRouteEnter (to, from, next) {
        // 在渲染该组件的对应路由被 confirm 前调用
        // 不！能！获取组件实例 `this`
        // 因为当守卫执行前，组件实例还没被创建
    },
    //不过，你可以通过传一个回调给 next来访问组件实例。
    //在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。
    beforeRouteEnter (to, from, next) {
        next(vm => {
            // 通过 `vm` 访问组件实例
        })
    },
    beforeRouteUpdate (to, from, next) {
        // 在当前路由改变，但是该组件被复用时调用
        // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
        // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
        // 可以访问组件实例 `this`
    },
    beforeRouteLeave (to, from, next) {
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 `this`
    }
}
```


注意：beforeRouteEnter是支持给next传递回调的唯一守卫。对于beforeRouteUpdate和beforeRouteLeave来说，this已经可用了，所以不支持传递回调，因为没有必要了:


```javascript
beforeRouteUpdate (to, from, next) {
    // just use `this`
    this.name = to.params.name;
    next();
}
```


离开守卫beforeRouteLeave：通常用来禁止用户在还未保存修改前突然离开。该导航可以通过next(false)来取消：


```javascript
beforeRouteLeave (to, from , next) {
    const answer = window.confirm('Do you really want to leave? you have unsaved changes!');
    if (answer) {
        next()
    } else {
        next(false)
    }
}
```


#### vue-router的几种实例方法以及参数传递？
| 实例方法 | 解释 | 
| :----- | :----- | 
|<div style='width: 400px'>this.$router.push(location, onComplete?, onAbort?)</div>|这个方法会向history栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的URL。并且点击`<router-link :to="...">`等同于调用 router.push(...)。|
|<div style='width: 400px'>this.$router.replace(location, onComplete?, onAbort?)</div>|这个方法不会向history添加新记录，而是跟它的方法名一样——替换掉当前的history记录，所以，当用户点击浏览器后退按钮时，并不会回到之前的URL。|
|<div style='width: 400px'>this.$router.go(n)</div>|这个方法的参数是一个整数，意思是在history记录中向前或者后退多少步，类似window.history.go(n)。|


**注意：**<br>
1. 在2.2.0+，可选的在router.push或router.replace中提供onComplete和onAbort回调作为第二个和第三个参数。这些回调将会在导航成功完成(在所有的异步钩子被解析之后)或终止(导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由)的时候进行相应的调用；<br>
2. 如果目的地和当前路由相同，只有参数发生了改变(比如从一个用户资料到另一个/users/1 -> /users/2)，你需要使用beforeRouteUpdate来响应这个变化(比如抓取用户信息)。


**参数传递方式：**


vue-router提供了params、query、meta三种页面间传递参数的方式。


示例：


```javascript
// 字符串，不带参数
this.$router.push('home');

// 对象，不带参数
this.$router.push({ path: 'home' });

// params（推荐）：命名的路由，params 必须和 name 搭配使用
this.$router.push({ name:'user', params: { userId: 123 }});

// 这里的 params 不生效
this.$router.push({ path:'/user', params: { userId: 123 }});

// query：带查询参数，变成 /register?plan=private
this.$router.push({ path: 'register', query: { plan: 'private' }});

//meta方式：路由元信息
export default new Router({
    routes: [
        {
            path: '/user',
            name: 'user',
            component: user,
            meta:{
                title:'个人中心'
            }
        }
    ]
})
```


在组件中使用：


```javascript
//通过$route对象获取，注意是route，么有r
this.$route.params;

this.$route.query;

this.$route.meta;
```


#### $route和$router的区别？
| 名称 | 解释 | 
| :----- | :----- | 
|$route|是“路由信息对象”，包括path、params、hash、query、fullPath、matched、name等路由信息参数。|
|$router|是“路由实例”对象包括了路由的跳转方法、钩子函数等。|


#### vue-router的动态路由匹配以及使用？
动态路径匹配：即把某种模式匹配到的所有路由，全都映射到同个组件。使用动态路由参数来实现。


例如，我们有一个User组件，对于所有ID各不相同的用户，都要使用这个组件来渲染。那么，我们可以在vue-router的路由路径中使用“动态路径参数”(dynamic segment)来达到这个效果。


```javascript
const User = {
    template: '<div>User</div>'
};

const router = new VueRouter({
    routes: [
    // 动态路径参数 以冒号开头
        { path: '/user/:id', component: User }
    ]
})
```


这样，像/user/foo和/user/bar都将映射到相同的路由。


一个“路径参数”使用冒号“:”标记。当匹配到一个路由时，参数值会被设置到this.$route.params，可以在每个组件内使用。


在User的模板，输出当前用户的ID：


```javascript
const User = {
    template: '<div>User {{ $route.params.id }}</div>'
}
```


#### vue-router如何定义嵌套路由？
嵌套路由：是路由的多层嵌套。


1.需要在一个被渲染的组件中嵌套router-view组件。


例如，在User组件的模板添加一个router-view：


```javascript
const User = {
    template: `
        <div class="user">
            <h2>User</h2>
            <router-view></router-view>
        </div>
    `
}
```


2.在嵌套的出口中渲染组件，在VueRouter的参数中使用children配置：


```javascript
const router = new VueRouter({
    routes: [
        { 
            path: '/user/:id', 
            component: User,
            children: [
                {
                    // 当 /user/:id/profile 匹配成功，
                    // UserProfile 会被渲染在 User 的 <router-view> 中
                    path: 'profile',
                    component: UserProfile
                },
                {
                    // 当 /user/:id/posts 匹配成功
                    // UserPosts 会被渲染在 User 的 <router-view> 中
                    path: 'posts',
                    component: UserPosts
                }
            ]
        }
    ]
})
```


注意：children配置就是像routes配置一样的路由配置数组。所以呢，你可以嵌套多层路由。


注意：如果基于上面的配置，当你访问/user/foo时，User的出口是不会渲染任何东西，这是因为没有匹配到合适的子路由。如果你想要渲染点什么，可以提供一个空的子路由：


```javascript
const router = new VueRouter({
    routes: [
        {
            path: '/user/:id', 
            component: User,
            children: [
                // 当 /user/:id 匹配成功，
                // UserHome 会被渲染在 User 的 <router-view> 中
                { path: '', component: UserHome },
                // ...其他子路由
            ]
        }
    ]
});
```


#### router-link组件及其属性？
router-link组件：用于支持用户在具有路由功能的应用中(点击)导航。


可以通过to属性指定目标地址，默认渲染成带有正确链接的`<a>`标签，可以通过配置tag属性生成别的标签。另外，当目标路由成功激活时，链接元素自动设置一个表示激活的CSS类名。


| 属性 | 类型 | 说明 | 示例 | 
| :----- | :----- | :----- | :----- | 
|<div style='width: 100px'>to</div>|string\Location|表示目标路由的链接。当被点击后，内部会立刻把to的值传到router.push()，所以这个值可以是一个字符串或者是描述目标位置的对象。|`<router-link to="home">Home</router-link>`|
|<div style='width: 100px'>replace</div>|boolean(默认false)|设置replace属性的话，当点击时，会调用router.replace()而不是router.push()，于是导航后不会留下history记录。|`<router-link :to="{ path: '/abc'}" replace></router-link>`|
|<div style='width: 100px'>append</div>|boolean(默认false)|设置append属性后，则在当前(相对)路径前添加基路径。例如，我们从/a导航到一个相对路径b，如果没有配置append，则路径为/b，如果配了，则为/a/b。|`<router-link :to="{ path: 'relative/path'}" append></router-link>`|
|<div style='width: 100px'>tag</div>|string(默认'a')|有时候想要渲染成某种标签，例如`<li>`于是我们使用tag prop类指定何种标签，同样它还是会监听点击，触发导航。|`<router-link to="/foo" tag="li">foo</router-link>`|
|<div style='width: 100px'>active-class</div>|string(默认"router-link-active")|设置链接激活时使用的CSS类名。默认值可以通过路由的构造选项linkActiveClass来全局配置。|————|
|<div style='width: 100px'>exact</div>|boolean(默认false)|"是否激活"默认类名的依据是inclusive match(全包含匹配)。举个例子，如果当前的路径是/a开头的，那么也会被设置CSS类名。|这个链接只会在地址为/的时候被激活：`<router-link to="/" exact>`|
|<div style='width: 100px'>event</div>|string\Array(默认'click')|声明可以用来触发导航的事件，可以是一个字符串或是一个包含字符串的数组。|————|
|<div style='width: 100px'>exact-active-class</div>|string默认'router-link-exact-active'|配置当链接被精确匹配的时候应该激活的class。注意默认值也是可以通过路由构造函数选项linkExactActiveClass进行全局配置的。|————|


#### vue-router实现动态加载路由组件(懒加载)？
当打包构建应用时，Javascript包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了。结合Vue的异步组件和Webpack的代码分割功能，轻松实现路由组件的懒加载。


1.定义一个能够被Webpack自动代码分割的异步组件。


```javascript
//在src/router/index.js里面引入异步引入组件
const index = () => import('../page/list/index.vue');
```


2.在路由配置中什么都不需要改变，只需要像往常一样使用index。


```javascript
const router = new VueRouter({
    routes: [
        { path: '/index', component: index,name:"index" }
    ]
})
```


3.在build/webpack.base.conf.js下的output属性，新增chunkFilename。


```
output: {
    path: config.build.assetsRoot,
    filename: '[name].js',
    //新增chunFilename属性
    chunkFilename: '[name].js',
    publicPath: process.env.NODE_ENV === 'production' ? config.build.assetsPublicPath : config.dev.assetsPublicPath
},
```


#### vue-router路由的两种模式？
vue-router路由提供了两种路由模式：**hash模式**和**history模式**。


**hash模式：**


vue-router默认hash模式。使用URL的hash来模拟一个完整的URL，于是当URL改变时，页面不会重新加载。


**history模式：**


如果不想要很丑的hash，我们可以用路由的history模式。这种模式充分利用history.pushState API来完成URL跳转而无须重新加载页面。


```javascript
//设置mode属性，设置路由模式
const router = new VueRouter({
    mode: 'history',
    routes: [...]
})
```


不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问`http://oursite.com/user/id`就会返回404，这就不好看了。所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果URL匹配不到任何静态资源，则应该返回同一个index.html页面，这个页面就是你app依赖的页面。