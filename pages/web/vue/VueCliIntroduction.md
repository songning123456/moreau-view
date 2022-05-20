#### 构建的vue-cli工程都到了哪些技术，它们的作用分别是什么？
| 名称 | 作用 | 
| :----- | :----- | 
|<div style='width: 140px'>vue.js</div>|vue-cli工程的核心，主要特点是<span class='forest-green'>双向数据绑定</span>和<span class='forest-green'>组件系统</span>。|
|<div style='width: 140px'>vue-router</div>|vue官方推荐使用的路由框架。|
|<div style='width: 140px'>vuex</div>|专为vue.js应用项目开发的状态管理器，主要用于维护Vue组件间共用的一些变量和方法。|
|<div style='width: 140px'>axios/fetch/ajax</div> |用于发起GET或POST等http请求，基于Promise设计。|
|<div style='width: 140px'>vux等</div>|一个专为Vue设计的移动端UI组件库|
|<div style='width: 140px'>(创建)emit.js文件</div>|用于Vue事件机制的管理。|
|<div style='width: 140px'>webpack</div>|模块加载和vue-cli工程打包器。|


#### vue-cli工程常用的npm命令有哪些？
| 命令 | 解释 | 
| :----- | :----- | 
|<div style='width: 180px'>npm install</div>|下载node_modules资源包的命令。|
|<div style='width: 180px'>npm run dev</div>|启动vue-cli开发环境的npm命令。|
|<div style='width: 180px'>npm run build</div>|vue-cli生成生产环境部署资源的npm命令。|
|<div style='width: 180px'>npm run build --report</div>|用于查看vue-cli生产环境部署资源文件大小的npm命令。在浏览器上自动弹出一个展示vue-cli工程打包后app.js、manifest.js、vendor.js文件里面所包含代码的页面。可以具此优化vue-cli生产环境部署的静态资源，提升页面的加载速度。|


#### 请说出vue-cli工程中每个文件夹和文件的用处？
![vue-cli目录](/images/Web/vue-cli.jpg)


| 文件夹 | 解释 | 
| :----- | :----- | 
|<div style='width: 130px'>build文件夹</div>|用于存放webpack相关配置和脚本。开发中仅偶尔使用到此文件夹下webpack.base.conf.js用于配置less、sass等css预编译库，或者配置一下UI库。|
|<div style='width: 130px'>config文件夹</div>|主要存放配置文件，用于区分开发环境、线上环境的不同。 常用到此文件夹下config.js配置开发环境的端口号、是否开启热加载或者设置生产环境的静态资源相对路径、是否开启gzip压缩、npm run build命令打包生成静态资源的名称和路径等。|
|<div style='width: 130px'>dist文件夹</div>|默认npm run build命令打包生成的静态资源文件，用于生产部署。|
|<div style='width: 130px'>node_modules</div>|存放npm命令下载的开发环境和生产环境的依赖包。|
|<div style='width: 130px'>src</div>|存放项目源码及需要引用的资源文件。|
|<div style='width: 130px'>src下assets</div>|存放项目中需要用到的资源文件，css、js、images等。|
|<div style='width: 130px'>src下components</div>|存放vue开发中一些公共组件：header.vue、footer.vue等。|
|<div style='width: 130px'>src下emit</div>|自己配置的vue集中式事件管理机制。|
|<div style='width: 130px'>src下router</div>|vue-router vue路由的配置文件。|
|<div style='width: 130px'>src下service</div>|自己配置的vue请求后台接口方法。|
|<div style='width: 130px'>src下page</div>|存在vue页面组件的文件夹。|
|<div style='width: 130px'>src下util</div>|存放vue开发过程中一些公共的.js方法。|
|<div style='width: 130px'>src下vuex</div>|存放vuex为vue专门开发的状态管理器。|
|<div style='width: 130px'>src下app.vue</div>|使用标签`<route-view></router-view>`渲染整个工程的.vue组件。|
|<div style='width: 130px'>src下main.js</div>|vue-cli工程的入口文件。|
|<div style='width: 130px'>index.html</div>|设置项目的一些meta头信息和提供`<div id="app"></div>`用于挂载vue节点。|
|<div style='width: 130px'>package.json</div>|用于node_modules资源部和启动、打包项目的npm命令管理。|


#### config文件夹下index.js的对于工程开发环境和生产环境的配置？
![config-index](/images/Web/config-index.jpeg)


| 名称 | 环境 | 功能 | 
| :----- | :----- | :----- | 
|build|<div style='width: 80px'>生产环境</div>|<span class='forest-green'>index：</span>配置打包后入口.html文件的名称以及文件夹名称；<br><span class='forest-green'>assetsRoot：</span>配置打包后生成的文件名称和路径；<br><span class='forest-green'>assetsPublicPath：</span>配置打包后.html引用静态资源的路径，一般要设置成 "./"；<br><span class='forest-green'>productionGzip：</span>是否开发gzip压缩，以提升加载速度。|
|dev|<div style='width: 80px'>开发环境</div>|<span class='forest-green'>port：</span>设置端口号；<br><span class='forest-green'>autoOpenBrowser：</span>启动工程时，自动打开浏览器；<br><span class='forest-green'>proxyTable：</span>vue设置的代理，用以解决跨域问题。|


#### 请你详细介绍一些package.json里面的配置？
| 名称 | 解释 | 
| :----- | :----- | 
|scripts|npm run xxx命令调用node执行的.js文件。|
|dependencies|生产环境依赖包的名称和版本号，即这些依赖包都会打包进生产环境的JS文件里面。|
|devDependencies|开发环境依赖包的名称和版本号，即这些依赖包只用于代码开发的时候，不会打包进生产环境js文件里面。|


