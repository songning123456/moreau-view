#### Promise对象是什么？
Promise对象是ES6(ECMAScript 2015)对于异步编程提供的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。


传统回调：


```javascript
// 当参数a大于10且参数func2是一个方法时 执行func2
function func1(a, func2) {
    if (a > 10 && typeof func2 == 'function') {
        func2()
    }
}

func1(11, function() {
    console.log('this is a callback')
})
```


Promise对象改写：


````javascript
function func1(a){
	return new Promise((resolve,reject) => {
    	if(a > 10){
            resolve(a)
        }else{
        	reject(b)
        }
    })
}

func1('11').then(res => {
	console.log('success');
}).catch(err => {
    console.log('error');
})
````


**Promise对象的两个特点：**


| 特点 | 解释 | 
| :----- | :----- | 
|<div style='width: 200px'>对象的状态不受外界影响</div>|Promise对象有三种状态：pending(进行中)、fulfilled(已成功)和rejected(已失败)。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是Promise这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。|
|<div style='width: 200px'>一旦状态改变，就不会再变，任何时候都可以得到这个结果</div>|Promise对象的状态改变，只有两种可能：从pending变为fulfilled和从pending变为rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为resolved(已定型)。如果改变已经发生了，你再对Promise对象添加回调函数，也会立即得到这个结果。这与事件(Event)完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。|


|类型| 名称 | 解释 | 
| :----- | :----- | :----- | 
|<div class='forest-green' style='width: 50px'>参数</div>|resolve|将Promise对象的状态从“未完成”变为“成功”(即从pending变为fulfilled)，在异步操作成功时调用，并将异步操作的结果，作为参数传递出去。|
|<div class='forest-green' style='width: 50px'>参数</div>|reject|将Promise对象的状态从“未完成”变为“失败”(即从pending变为rejected)，在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。|
|<div class='forest-green' style='width: 50px'>方法</div>|.then|用于指定调用成功时的回调函数。.then方法返回的是一个新的Promise实例(注意，不是原来那个Promise实例)，因此可以采用链式写法，即then方法后面再调用另一个then方法。|
|<div class='forest-green' style='width: 50px'>方法</div>|.catch|用于指定发生错误时的回调函数。|


#### axios、fetch与ajax有什么区别？
主要区别是axios、fetch请求后都支持Promise对象API，ajax只能用回调函数。


**ajax：**


Ajax被认为是(Asynchronous JavaScript and XML)的缩写。现在，允许浏览器与服务器通信而无须刷新当前页面的技术都被叫做Ajax。依赖的传输对象：XMLHttpRequest。


**axios：**


示例：


```javascript
axios({
    method: 'post',
    url: '/user/12345',
    data: {
        firstName: 'Fred',
        lastName: 'Flintstone'
    }
})
.then(function (response) {
    console.log(response);
})
.catch(function (error) {
    console.log(error);
});
```


Vue2.0之后推荐大家用axios替换JQuery ajax，想必让Axios进入了很多人的目光中。


Axios本质上也是对原生XHR的封装，只不过它是Promise的实现版本，符合最新的ES规范，它有以下几条特性：<br>
1. 从node.js创建http请求；<br>
2. 支持Promise API；<br>
3. 客户端支持防止CSRF；<br>
4. 提供了一些并发请求的接口(重要，方便了很多的操作)。


Axios既提供了并发的封装，也没有fetch的各种问题，而且体积也较小，推荐大家使用。


**fetch：**


示例：


```javascript
fetch(url).then(res => {
    console.log(res)
}).catch(err => {
	console.log(err)
})
```


Fetch API提供了一个JavaScript接口，用于访问和操纵HTTP管道的部分，例如请求和响应。它还提供了一个全局fetch()方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。


<span class='forest-green'>Fetch优点主要有：</span><br>
1. 语法简洁，更加语义化；<br>
2. 基于标准Promise实现，支持async/await；<br>
3. 同构方便，使用isomorphic-fetch。


<span class='forest-green'>不过原生支持率并不高，引入下面这些polyfill后可以完美支持IE8+：</span><br>
1. 由于IE8是ES3，需要引入ES5的polyfill：es5-shim, es5-sham；<br>
2. 引入Promise的polyfill：es6-promise；<br>
3. 引入fetch探测库：fetch-detector；<br>
4. 引入fetch的polyfill：fetch-ie8；<br>
5. 可选：如果你还使用了jsonp，引入fetch-jsonp；<br>
6. 可选：开启Babel的runtime模式，现在就使用async/await。


#### 什么是JS的同源策略和跨域问题？
请求的跨域问题真的不是浏览器不同引起的！！！


<span class='forest-green'>同源策略：</span>所谓同源策略，指的是浏览器对不同源的脚本或者文本的访问方式进行的限制。比如源a的js不能读取或设置引入的源b的元素属性。同源指两个页面具有相同的协议、主机(也常说域名)、端口三个要素缺一不可。


| URL1 | URL2 | 说明 | 是否允许通信 |
| :----- | :----- | :----- | :----- | 
|`http://www.foo.com/js/a.js`|`http://www.foo.com/js/b.js`|协议、域名、端口都相同|<div style='width: 50px'>允许</div>|
|`http://www.foo.com/js/a.js`|`http://www.foo.com:8888/js/b.js`|协议、域名相同，端口不同|<div style='width: 50px'>不允许</div>|
|`https://www.foo.com/js/a.js`|`http://www.foo.com/js/b.js`|主机、域名相同，协议不同|<div style='width: 50px'>不允许</div>|
|`http://www.foo.com/js/a.js`|`http://www.bar.com/js/b.js`|协议、端口相同，域名不同|<div style='width: 50px'>不允许</div>|
|`http://www.foo.com/js/a.js`|`http://foo.com/js/b.js`|协议、端口相同，主域名相同，子域名不同|<div style='width: 50px'>不允许</div>|


同源策略限制的不同源之间的交互主要针对的是JS中的XMLHttpRequest等请求，下面这些情况是完全不受同源策略限制的：<br>
1. 页面中的链接，重定向以及表单提交是不会受到同源策略限制的；<br>
2. 跨域资源嵌入是允许的，当然，浏览器限制了Javascript不能读写加载的内容。


**跨域问题：**


即我们需要向不同源的后台接口发送http请求去请求数据。vue-cli开发环境开发的时候一定会涉及到跨域问题，因为vue-cli开发环境启动本地一个localhost:端口号node服务器，此时去请求数据接口一定是跨域的。


#### 如何解决跨域问题？
不要只说个jsonp，实际开发中第二种方式用的多。


1. vue-cli项目中通过node.js代理服务器来实现跨域请求；<br>
2. 在服务器响应客户端的时候，带上Access-Control-Allow-Origin:*头信息(<span class='forest-green'>推荐使用</span>)；<br>
3. 通过jquery的jsonp形式解决跨域问题。


1.在vue-cli项目中的config文件夹下的index.js配置文件中，配置dev对象的proxyTable对象，可通过node.js的代理服务器来实现跨域请求。


```html
dev: {
    env: require('./dev.env'),
    port: 8080,
    autoOpenBrowser: true,
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {},   
    cssSourceMap: false
}
```


但是这种解决方案，仅适用于vue-cli开发环境解决跨域问题，生产环境如果涉及到跨域问题，并未解决。


2.以java代码为例，设置http请求的响应头，推荐使用：


```html
public class testFilter implements Filter { 
    public void doFilter(ServletRequest request, ServletResponse resp, FilterChain chain) throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse) resp; 
        response.setHeader("Access-Control-Allow-Origin", "*"); //解决跨域访问报错 
        response.setHeader("Access-Control-Allow-Methods", "POST, PUT, GET, OPTIONS, DELETE"); 
        response.setHeader("Access-Control-Max-Age", "3600"); //设置过期时间 
        response.setHeader("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept, client_id, uuid, Authorization"); 
        response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate"); // 支持HTTP 1.1. 
        response.setHeader("Pragma", "no-cache"); // 支持HTTP 1.0. response.setHeader("Expires", "0"); 
        chain.doFilter(req, res); 
    }
}
```


3.jQuery设置ajax请求跨域：


```javascript
$.ajax({
    url:'...../data.js',//可以不是本地域名 
    type:'get',
    dataType:'jsonp',  //jsonp格式访问
    jsonpCallback:'aa'  //获取数据的函数
})
```


#### 如何在vue-cli工程中调试接口时使用本地的JSON模拟数据？
1.在新建目录：src/service/tempdata下新建test.js文件，存放本地JSON数据：


```javascript
export  const meeting = {
	"data": [
        {
            "name": "列表1", 
            "nameId": "123"
        },
        {
            "name": "列表2", 
            "nameId": "234"
        }
    ]
};
```


2.在新建目录：src/service/getData.js下引入使用：


```javascript
//1、引入
import * as meeting from './tempdata/meeting';

//2、设置 JSON 模拟数据函数
const setpromise = data => {
	return new Promise((resolve, reject) => {
		resolve(data)
        //如果修改为 reject(data)，则下面使用时，走 .catch 方法
	})
};

//3、使用
var Meeting = () => setpromise(meeting.meeting); 

//4、导出
export{ Meeting }
```


3.在.vue文件中使用：
```html
<script type="text/javascript">
//1、引入
import { Meeting } from '../service/getData';
//2、即可在 生命周期钩子函数 或者 methods 方法里面使用了
export default{
	mounted(){
    	Meeting().then(res => {
        	//res为模拟数据
        	console.log(res)
        }).catch(err => {
        	console.log(err)
        })
    }
}
</script>
```


#### axios有什么特点？
1. Axios是一个基于promise的HTTP库，支持promise所有的API；
2. 它可以拦截请求和响应；
3. 它可以转换请求数据和响应数据，并对响应回来的内容自动转换成JSON类型的数据；
4. 安全性更高，客户端支持防御XSRF。

