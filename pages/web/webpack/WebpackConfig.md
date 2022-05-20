#### 什么是Entry？
entry是配置模块的入口，可抽象成输入，Webpack执行构建的第一步将从入口开始搜寻及递归解析出所有入口依赖的模块。entry配置是必填的，若不填则将导致Webpack报错退出。


#### 什么是context？
Webpack在寻找相对路径的文件时会以context为根目录，context默认为执行启动Webpack时所在的当前工作目录。 如果想改变context的默认配置，则可以在配置文件里这样设置它：


```javascript
module.exports = {
    context: path.resolve(__dirname, 'app')
}
```


注意，context必须是一个绝对路径的字符串。除此之外，还可以通过在启动Webpack时带上参数webpack --context来设置context。之所以在这里先介绍context，是因为Entry的路径和其依赖的模块的路径可能采用相对于context的路径来描述，context会影响到这些相对路径所指向的真实文件。


#### Entry类型有哪些？
Entry类型可以是以下三种中的一种或者相互组合：


|类型|例子|含义|
| :----- | :----- | :----- | 
|string|'./app/entry'|入口模块的文件路径，可以是相对路径。|
|array|['./app/entry1', './app/entry2']|入口模块的文件路径，可以是相对路径。|
|object|{ a: './app/entry-a', b: ['./app/entry-b1', './app/entry-b2']}|配置多个入口，每个入口生成一个 Chunk。|


如果是array类型，则搭配output.library配置项使用时，只有数组里的最后一个入口文件的模块会被导出。


#### Chunk名称？
Webpack会为每个生成的Chunk取一个名称，Chunk的名称和Entry的配置有关：


```
1. 如果entry是一个string或array，就只会生成一个Chunk，这时Chunk的名称是main；
2. 如果entry是一个object，就可能会出现多个Chunk，这时Chunk的名称是object键值对里键的名称。
```


#### 如何配置动态Entry？
假如项目里有多个页面需要为每个页面的入口配置一个Entry，但这些页面的数量可能会不断增长，则这时Entry的配置会受到到其他因素的影响导致不能写成静态的值。其解决方法是把Entry设置成一个函数去动态返回上面所说的配置，代码如下：


```javascript
// 同步函数
entry: () => {
    return {
        a:'./pages/a',
        b:'./pages/b',
    }
};
// 异步函数
entry: () => {
    return new Promise((resolve)=>{
        resolve({
            a:'./pages/a',
            b:'./pages/b',
        });
    });
};
```


#### 什么是Output？
output配置如何输出最终想要的代码。output是一个object，里面包含一系列配置项。


##### filename配置？
output.filename配置输出文件的名称，为string类型。如果只有一个输出文件，则可以把它写成静态不变的：


```javascript
filename: 'bundle.js'
```


但是在有多个Chunk要输出时，就需要借助模版和变量了。前面说到Webpack会为每个Chunk取一个名称，可以根据Chunk的名称来区分输出的文件名：


```javascript
filename: '[name].js'
```


代码里的[name]代表用内置的name变量去替换[name]，这时你可以把它看作一个字符串模块函数，每个要输出的Chunk都会通过这个函数去拼接出输出的文件名称。内置变量除了name还包括：


|变量名|含义|
| :----- | :----- | 
|id|Chunk的唯一标识，从0开始|
|name|Chunk的名称|
|hash|Chunk的唯一标识的Hash值|
|chunkhash|Chunk内容的Hash值|


其中hash和chunkhash的长度是可指定的，[hash:8]代表取8位Hash值，默认是20位。


注意ExtractTextWebpackPlugin插件是使用contenthash来代表哈希值而不是chunkhash，原因在于ExtractTextWebpackPlugin提取出来的内容是代码内容本身而不是由一组模块组成的Chunk。


##### chunkFilename配置？
output.chunkFilename配置无入口的Chunk在输出时的文件名称。chunkFilename和上面的filename非常类似，但chunkFilename只用于指定在运行过程中生成的Chunk在输出时的文件名称。常见的会在运行时生成Chunk场景有在使用CommonChunkPlugin、使用import('path/to/module')动态加载等时。chunkFilename支持和filename一致的内置变量。


##### path配置？
output.path配置输出文件存放在本地的目录，必须是string类型的绝对路径。通常通过Node.js的path模块去获取绝对路径：


```javascript
path: path.resolve(__dirname, 'dist_[hash]')
```


##### publicPath配置？
在复杂的项目里可能会有一些构建出的资源需要异步加载，加载这些异步资源需要对应的URL地址。output.publicPath配置发布到线上资源的URL前缀，为string类型。默认值是空字符串''，即使用相对路径。这样说可能有点抽象，举个例子，需要把构建出的资源文件上传到CDN服务上，以利于加快页面的打开速度。配置代码如下：


```javascript
filename:'[name]_[chunkhash:8].js';
publicPath: 'https://cdn.example.com/assets/';
```


这时发布到线上的HTML在引入JavaScript文件时就需要：


```html
<script src='https://cdn.example.com/assets/a_12345678.js'></script>
```


使用该配置项时要小心，稍有不慎将导致资源加载404错误。output.path和output.publicPath都支持字符串模版，内置变量只有一个：hash代表一次编译操作的Hash值。


##### crossOriginLoading配置？
Webpack输出的部分代码块可能需要异步加载，而异步加载是通过JSONP方式实现的。JSONP的原理是动态地向HTML中插入一个`<script src="url"></script>`标签去加载异步资源。output.crossOriginLoading则是用于配置这个异步插入的标签的crossorigin值。


script标签的crossorigin属性可以取以下值：


```
1. anonymous(默认)在加载此脚本资源时不会带上用户的Cookies；
2. use-credentials在加载此脚本资源时会带上用户的Cookies。
```


通常用设置crossorigin来获取异步加载的脚本执行时的详细错误信息。


##### libraryTarget和library配置？
当用Webpack去构建一个可以被其他模块导入使用的库时需要用到它们。


```html
1. output.libraryTarget配置以何种方式导出库；
2. output.library配置导出库的名称；
3. 它们通常搭配在一起使用。
```


output.libraryTarget是字符串的枚举类型，支持以下配置。


##### var(默认)配置？
编写的库将通过var被赋值给通过library指定名称的变量。假如配置了output.library='LibraryName'，则输出和使用的代码如下：


````javascript
// Webpack 输出的代码
var LibraryName = lib_code;

// 使用库的方法
LibraryName.doSomething();
````


假如output.library为空，则将直接输出：


```html
lib_code
```


其中lib_code代指导出库的代码内容，是有返回值的一个自执行函数。


##### CommonJS配置？
编写的库将通过CommonJS规范导出。假如配置了output.library='LibraryName'，则输出和使用的代码如下：


```javascript
// Webpack 输出的代码
exports['LibraryName'] = lib_code;

// 使用库的方法
require('library-name-in-npm')['LibraryName'].doSomething();
```


其中library-name-in-npm是指模块发布到Npm代码仓库时的名称。


##### CommonJS2配置？
编写的库将通过CommonJS2规范导出，输出和使用的代码如下：


```javascript
// Webpack 输出的代码
module.exports = lib_code;

// 使用库的方法
require('library-name-in-npm').doSomething();
```


CommonJS2和CommonJS规范很相似，差别在于CommonJS只能用exports导出，而CommonJS2在CommonJS的基础上增加了module.exports的导出方式。在output.libraryTarget为commonjs2时，配置output.library将没有意义。


##### this配置？
编写的库将通过this被赋值给通过library指定的名称，输出和使用的代码如下：


```javascript
// Webpack 输出的代码
this['LibraryName'] = lib_code;

// 使用库的方法
this.LibraryName.doSomething();
```


##### window配置？
编写的库将通过window被赋值给通过library指定的名称，即把库挂载到window上，输出和使用的代码如下：


```javascript
// Webpack 输出的代码
window['LibraryName'] = lib_code;

// 使用库的方法
window.LibraryName.doSomething();
```


##### global配置？
编写的库将通过global被赋值给通过library指定的名称，即把库挂载到global上，输出和使用的代码如下：


```javascript
// Webpack 输出的代码
global['LibraryName'] = lib_code;

// 使用库的方法
global.LibraryName.doSomething();
```


##### libraryExport配置？
output.libraryExport配置要导出的模块中哪些子模块需要被导出。它只有在output.libraryTarget被设置成commonjs或者commonjs2时使用才有意义。假如要导出的模块源代码是：


```javascript
export const a = 1;
export default b = 2;
```


现在你想让构建输出的代码只导出其中的a，可以把output.libraryExport设置成a，那么构建输出的代码和使用方法将变成如下：


```javascript
// Webpack 输出的代码
module.exports = lib_code['a'];

// 使用库的方法
require('library-name-in-npm')===1;
```


#### 什么是Module？
module配置如何处理模块。


##### 配置Loader？
rules配置模块的读取和解析规则，通常用来配置Loader。其类型是一个数组，数组里每一项都描述了如何去处理部分文件。配置一项rules时大致通过以下方式：


1. 条件匹配：通过test、include、exclude三个配置项来命中Loader要应用规则的文件。
2. 应用规则：对选中后的文件通过use配置项来应用Loader，可以只应用一个Loader或者按照从后往前的顺序应用一组Loader，同时还可以分别给Loader传入参数。
3. 重置顺序：一组Loader的执行顺序默认是从右到左执行，通过enforce选项可以让其中一个Loader的执行顺序放到最前或者最后。


下面来通过一个例子来说明具体使用方法：


```javascript
module: {
    rules: [
        {
            // 命中 JavaScript 文件
            test: /\.js$/,
            // 用 babel-loader 转换 JavaScript 文件
            // ?cacheDirectory 表示传给 babel-loader 的参数，用于缓存 babel 编译结果加快重新编译速度
            use: ['babel-loader?cacheDirectory'],
            // 只命中src目录里的js文件，加快 Webpack 搜索速度
            include: path.resolve(__dirname, 'src')
        },
        {
            // 命中 SCSS 文件
            test: /\.scss$/,
            // 使用一组 Loader 去处理 SCSS 文件。
            // 处理顺序为从后到前，即先交给 sass-loader 处理，再把结果交给 css-loader 最后再给 style-loader。
            use: ['style-loader', 'css-loader', 'sass-loader'],
            // 排除 node_modules 目录下的文件
            exclude: path.resolve(__dirname, 'node_modules'),
        },
        {
            // 对非文本文件采用 file-loader 加载
            test: /\.(gif|png|jpe?g|eot|woff|ttf|svg|pdf)$/,
            use: ['file-loader'],
        },
    ]
}
```


在Loader需要传入很多参数时，你还可以通过一个Object来描述，例如在上面的babel-loader配置中有如下代码：


```javascript
use: [
    {
        loader:'babel-loader',
        options:{
        cacheDirectory:true,
        },
        // enforce:'post' 的含义是把该 Loader 的执行顺序放到最后
        // enforce 的值还可以是 pre，代表把 Loader 的执行顺序放到最前面
        enforce:'post'
    },
    // 省略其它 Loader
]
```


上面的例子中test include exclude这三个命中文件的配置项只传入了一个字符串或正则，其实它们还都支持数组类型，使用如下：


```javascript
{
    test:[
        /\.jsx?$/,
        /\.tsx?$/
    ],
    include:[
        path.resolve(__dirname, 'src'),
        path.resolve(__dirname, 'tests'),
    ],
    exclude:[
        path.resolve(__dirname, 'node_modules'),
        path.resolve(__dirname, 'bower_modules'),
    ]
}
```


数组里的每项之间是或的关系，即文件路径符合数组中的任何一个条件就会被命中。


##### 配置noParse？
noParse配置项可以让Webpack忽略对部分没采用模块化的文件的递归解析和处理，这样做的好处是能提高构建性能。原因是一些库例如jQuery、ChartJS它们庞大又没有采用模块化标准，让Webpack去解析这些文件耗时又没有意义。noParse是可选配置项，类型需要是RegExp、[RegExp]、function其中一个。例如想要忽略掉jQuery、ChartJS，可以使用如下代码：


```javascript
// 使用正则表达式
noParse: /jquery|chartjs/;

// 使用函数，从 Webpack 3.0.0 开始支持
noParse: (content)=> {
    // content 代表一个模块的文件路径
    // 返回 true or false
    return /jquery|chartjs/.test(content);
}
```


注意被忽略掉的文件里不应该包含import、require、define等模块化语句，不然会导致构建出的代码中包含无法在浏览器环境下执行的模块化语句。


##### 配置parser？
因为Webpack是以模块化的JavaScript文件为入口，所以内置了对模块化JavaScript的解析功能，支持AMD、CommonJS、SystemJS、ES6。parser属性可以更细粒度的配置哪些模块语法要解析哪些不解析，和noParse配置项的区别在于parser可以精确到语法层面，而noParse只能控制哪些文件不被解析。parser使用如下：


```javascript
module: {
    rules: [
        {
        test: /\.js$/,
        use: ['babel-loader'],
        parser: {
            amd: false, // 禁用 AMD
            commonjs: false, // 禁用 CommonJS
            system: false, // 禁用 SystemJS
            harmony: false, // 禁用 ES6 import/export
            requireInclude: false, // 禁用 require.include
            requireEnsure: false, // 禁用 require.ensure
            requireContext: false, // 禁用 require.context
            browserify: false, // 禁用 browserify
            requireJs: false, // 禁用 requirejs
            }
        },
    ]
}
```


#### 什么是Resolve？
Webpack在启动后会从配置的入口模块出发找出所有依赖的模块，Resolve配置Webpack如何寻找模块所对应的文件。Webpack内置JavaScript模块化语法解析功能，默认会采用模块化标准里约定好的规则去寻找，但你也可以根据自己的需要修改默认的规则。


##### alias配置？
resolve.alias配置项通过别名来把原导入路径映射成一个新的导入路径。例如使用以下配置：


```javascript
// Webpack alias 配置
resolve:{
    alias:{
        components: './src/components/'
    }
}
```


当你通过import Button from 'components/button'导入时，实际上被alias等价替换成了import Button from './src/components/button'。以上alias配置的含义是把导入语句里的components关键字替换成./src/components/。这样做可能会命中太多的导入语句，alias还支持$符号来缩小范围到只命中以关键字结尾的导入语句：


```javascript
resolve:{
    alias:{
        'react$': '/path/to/react.min.js'
    }
}
```


react$只会命中以react结尾的导入语句，即只会把import 'react'关键字替换成import '/path/to/react.min.js'。


##### mainFields配置？
有一些第三方模块会针对不同环境提供几分代码。例如分别提供采用ES5和ES6的2份代码，这2份代码的位置写在package.json文件里，如下：


```javascript
{
    "jsnext:main": "es/index.js",// 采用 ES6 语法的代码入口文件
    "main": "lib/index.js" // 采用 ES5 语法的代码入口文件
}
```


Webpack会根据mainFields的配置去决定优先采用那份代码，mainFields默认如下：


```javascript
mainFields: ['browser', 'main']
```


Webpack会按照数组里的顺序去package.json文件里寻找，只会使用找到的第一个。假如你想优先采用ES6的那份代码，可以这样配置：


```javascript
mainFields: ['jsnext:main', 'browser', 'main']
```


##### extensions配置？
在导入语句没带文件后缀时，Webpack会自动带上后缀后去尝试访问文件是否存在。resolve.extensions用于配置在尝试过程中用到的后缀列表，默认是：


```javascript
extensions: ['.js', '.json']
```


也就是说当遇到require('./data')这样的导入语句时，Webpack 会先去寻找./data.js文件，如果该文件不存在就去寻找./data.json文件，如果还是找不到就报错。假如你想让Webpack优先使用目录下的TypeScript文件，可以这样配置：


```javascript
extensions: ['.ts', '.js', '.json']
```


##### modules配置？
resolve.modules配置Webpack去哪些目录下寻找第三方模块，默认是只会去node_modules目录下寻找。有时你的项目里会有一些模块会大量被其它模块依赖和导入，由于其它模块的位置分布不定，针对不同的文件都要去计算被导入模块文件的相对路径，这个路径有时候会很长，就像这样import '../../../components/button'这时你可以利用modules配置项优化，假如那些被大量导入的模块都在./src/components目录下，把modules配置成：


```javascript
modules:['./src/components','node_modules']
```


后，你可以简单通过import 'button'导入。


##### descriptionFiles配置？
resolve.descriptionFiles配置描述第三方模块的文件名称，也就是package.json文件。默认如下：


```javascript
descriptionFiles: ['package.json']
```


##### enforceExtension配置？
resolve.enforceExtension如果配置为true所有导入语句都必须要带文件后缀，例如开启前import './foo'能正常工作，开启后就必须写成import './foo.js'。


##### enforceModuleExtension配置？
enforceModuleExtension和enforceExtension作用类似，但enforceModuleExtension只对node_modules下的模块生效。enforceModuleExtension通常搭配enforceExtension使用，在enforceExtension:true时，因为安装的第三方模块中大多数导入语句没带文件后缀，所以这时通过配置enforceModuleExtension:false来兼容第三方模块。


#### 什么是Plugin？Plugin配置？
Plugin用于扩展Webpack功能，各种各样的Plugin几乎让Webpack可以做任何构建相关的事情。


Plugin的配置很简单，plugins配置项接受一个数组，数组里每一项都是一个要使用的Plugin的实例，Plugin需要的参数通过构造函数传入。


```javascript
const CommonsChunkPlugin = require('webpack/lib/optimize/CommonsChunkPlugin');

module.exports = {
    plugins: [
        // 所有页面都会用到的公共代码提取到 common 代码块中
        new CommonsChunkPlugin({
            name: 'common',
            chunks: ['a', 'b']
        }),
    ]
};
```


#### DevServer配置？
它提供了一些配置项可以改变DevServer的默认行为。要配置DevServer，除了在配置文件里通过devServer传入参数外，还可以通过命令行参数传入。注意只有在通过DevServer去启动Webpack时配置文件里devServer才会生效，因为这些参数所对应的功能都是DevServer提供的，Webpack本身并不认识devServer配置项。


##### hot配置？
devServer.hot配置是否启用使用DevServer中提到的模块热替换功能。DevServer默认的行为是在发现源代码被更新后会通过自动刷新整个页面来做到实时预览，开启模块热替换功能后将在不刷新整个页面的情况下通过用新模块替换老模块来做到实时预览。


##### inline配置？
DevServer的实时预览功能依赖一个注入到页面里的代理客户端去接受来自DevServer的命令和负责刷新网页的工作。devServer.inline用于配置是否自动注入这个代理客户端到将运行在页面里的Chunk里去，默认是会自动注入。DevServer会根据你是否开启inline来调整它的自动刷新策略：


1. 如果开启inline，DevServer会在构建完变化后的代码时通过代理客户端控制网页刷新。
2. 如果关闭inline，DevServer将无法直接控制要开发的网页。这时它会通过iframe的方式去运行要开发的网页，当构建完变化后的代码时通过刷新iframe来实现实时预览。 但这时你需要去`http://localhost:8080/webpack-dev-server/`实时预览你的网页了。
3. 如果你想使用DevServer去自动刷新网页实现实时预览，最方便的方法是直接开启inline。


##### historyApiFallback配置？
devServer.historyApiFallback用于方便的开发使用了HTML5 History API的单页应用。这类单页应用要求服务器在针对任何命中的路由时都返回一个对应的HTML文件，例如在访问`http://localhost/user和http://localhost/home`时都返回index.html文件，浏览器端的JavaScript代码会从URL里解析出当前页面的状态，显示出对应的界面。配置historyApiFallback最简单的做法是：


```javascript
historyApiFallback: true
```


这会导致任何请求都会返回index.html文件，这只能用于只有一个HTML文件的应用。如果你的应用由多个单页应用组成，这就需要DevServer根据不同的请求来返回不同的HTML文件，配置如下：


```javascript
historyApiFallback: {
    // 使用正则匹配命中路由
    rewrites: [
        // /user 开头的都返回 user.html
        { from: /^\/user/, to: '/user.html' },
        { from: /^\/game/, to: '/game.html' },
        // 其它的都返回 index.html
        { from: /./, to: '/index.html' },
    ]
}
```


##### contentBase配置？
devServer.contentBase配置DevServer HTTP服务器的文件根目录。默认情况下为当前执行目录，通常是项目根目录，所有一般情况下你不必设置它，除非你有额外的文件需要被DevServer服务。例如你想把项目根目录下的public目录设置成DevServer服务器的文件根目录，你可以这样配置：


```javascript
devServer:{
  contentBase: path.join(__dirname, 'public')
}
```


这里需要指出可能会让你疑惑的地方，DevServer服务器通过HTTP服务暴露出的文件分为两类：


1. 暴露本地文件。
2. 暴露Webpack构建出的结果，由于构建出的结果交给了DevServer，所以你在使用了DevServer时在本地找不到构建出的文件。contentBase只能用来配置暴露本地文件的规则，你可以通过contentBase：false来关闭暴露本地文件。


##### headers配置？
devServer.headers配置项可以在HTTP响应中注入一些HTTP响应头，使用如下：


```javascript
devServer:{
    headers: {
        'X-foo':'bar'
    }
}
```


##### host配置？
devServer.host配置项用于配置DevServer服务监听的地址。例如你想要局域网中的其它设备访问你本地的服务，可以在启动DevServer时带上--host 0.0.0.0。host的默认值是127.0.0.1即只有本地可以访问DevServer的HTTP服务。


##### port配置？
devServer.port配置项用于配置DevServer服务监听的端口，默认使用8080端口。如果8080端口已经被其它程序占有就使用8081，如果8081还是被占用就使用8082，以此类推。


##### allowedHosts配置？
devServer.allowedHosts配置一个白名单列表，只有HTTP请求的HOST在列表里才正常返回，使用如下：


```javascript
allowedHosts: [
    // 匹配单个域名
    'host.com',
    'sub.host.com',
    // host2.com 和所有的子域名 *.host2.com 都将匹配
    '.host2.com'
]
```


##### disableHostCheck配置？
devServer.disableHostCheck配置项用于配置是否关闭用于DNS重绑定的HTTP请求的HOST检查。DevServer默认只接受来自本地的请求，关闭后可以接受来自任何HOST的请求。它通常用于搭配--host 0.0.0.0使用，因为你想要其它设备访问你本地的服务，但访问时是直接通过IP地址访问而不是HOST访问，所以需要关闭HOST检查。


##### HTTPS配置？
DevServer默认使用HTTP协议服务，它也能通过HTTPS协议服务。有些情况下你必须使用HTTPS，例如HTTP2和Service Worker就必须运行在HTTPS之上。要切换成HTTPS服务，最简单的方式是：


```javascript
devServer:{
    https: true
}
```


DevServer会自动的为你生成一份HTTPS证书。如果你想用自己的证书可以这样配置：


```javascript
devServer:{
    https: {
        key: fs.readFileSync('path/to/server.key'),
        cert: fs.readFileSync('path/to/server.crt'),
        ca: fs.readFileSync('path/to/ca.pem')
    }
}
```


##### clientLogLevel配置？
devServer.clientLogLevel配置在客户端的日志等级，这会影响到你在浏览器开发者工具控制台里看到的日志内容。clientLogLevel是枚举类型，可取如下之一的值none|error|warning|info。默认为info级别，即输出所有类型的日志，设置成none可以不输出任何日志。


##### compress配置？
devServer.compress配置是否启用gzip压缩。boolean为类型，默认为false。


##### open配置？
devServer.open用于在DevServer启动且第一次构建完时自动用你系统上默认的浏览器去打开要开发的网页。同时还提供devServer.openPage配置项用于打开指定URL的网页。


#### Target配置？
javaScript的应用场景越来越多，从浏览器到Node.js，这些运行在不同环境的JavaScript代码存在一些差异。target配置项可以让Webpack构建出针对不同运行环境的代码。target可以是以下之一：

|target值|描述|
| :----- | :----- | 
|web|针对浏览器**(默认)**，所有代码都集中在一个文件里|
|node|针对Node.js，使用require语句加载Chunk代码|
|async-node|针对Node.js，异步加载 hunk代码|
|webworker|针对WebWorker|
|electron-main|针对Electron主线程|
|electron-renderer|针对Electron渲染线程|


例如当你设置target:'node'时，源代码中导入Node.js原生模块的语句require('fs')将会被保留，fs模块的内容不会打包进Chunk里。


#### devtool配置？
devtool配置Webpack如何生成Source Map，默认值是false即不生成Source Map，想为构建出的代码生成Source Map以方便调试，可以这样配置：


```javascript
module.export = {
    devtool: 'source-map'
}
```


#### Watch和WatchOptionsl配置？
前面介绍过Webpack的监听模式，它支持监听文件更新，在文件发生变化时重新编译。在使用Webpack时监听模式默认是关闭的，想打开需要如下配置：


```javascript
module.export = {
    watch: true
}
```


在使用DevServer时，监听模式默认是开启的。除此之外，Webpack还提供了watchOptions配置项去更灵活的控制监听模式，使用如下：


```javascript
module.export = {
    // 只有在开启监听模式时，watchOptions 才有意义
    // 默认为 false，也就是不开启
    watch: true,
    // 监听模式运行时的参数
    // 在开启监听模式时，才有意义
    watchOptions: {
        // 不监听的文件或文件夹，支持正则匹配
        // 默认为空
        ignored: /node_modules/,
        // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
        // 默认为 300ms  
        aggregateTimeout: 300,
        // 判断文件是否发生变化是通过不停的去询问系统指定文件有没有变化实现的
        // 默认每隔1000毫秒询问一次
        poll: 1000
    }
}
```


#### Externals配置？
Externals用来告诉Webpack要构建的代码中使用了哪些不用被打包的模块，也就是说这些模版是外部环境提供的，Webpack在打包时可以忽略它们。有些JavaScript运行环境可能内置了一些全局变量或者模块，例如在你的HTML HEAD标签里通过以下代码：`<script src="path/to/jquery.js"></script>`引入jQuery ，全局变量jQuery就会被注入到网页的JavaScript运行环境里。如果想在使用模块化的源代码里导入和使用jQuery，可能需要这样：


```javascript
import $ from 'jquery';
$('.my-element');
```


构建后你会发现输出的Chunk里包含的jQuery库的内容，这导致jQuery库出现了2次，浪费加载流量，最好是Chunk里不会包含jQuery库的内容。Externals配置项就是为了解决这个问题。通过externals可以告诉Webpack JavaScript运行环境已经内置了那些全局变量，针对这些全局变量不用打包进代码中而是直接使用全局变量。要解决以上问题，可以这样配置externals：


```javascript
module.export = {
    externals: {
        // 把导入语句里的 jquery 替换成运行环境里的全局变量 jQuery
        jquery: 'jQuery'
    }
}
```


#### ResolveLoader配置？
ResolveLoader用来告诉Webpack如何去寻找Loader，因为在使用Loader时是通过其包名称去引用的，Webpack需要根据配置的Loader包名去找到Loader的实际代码，以调用Loader去处理源文件。ResolveLoader的默认配置如下：


```javascript
module.exports = {
    resolveLoader:{
        // 去哪个目录下寻找 Loader
        modules: ['node_modules'],
        // 入口文件的后缀
        extensions: ['.js', '.json'],
        // 指明入口文件位置的字段
        mainFields: ['loader', 'main']
    }
}
```


该配置项常用于加载本地的Loader。


#### 如何整体配置结构？
```javascript
const path = require('path');

module.exports = {
    // entry 表示 入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
    // 类型可以是 string | object | array   
    entry: './app/entry', // 只有1个入口，入口只有1个文件
    entry: ['./app/entry1', './app/entry2'], // 只有1个入口，入口有2个文件
    entry: { // 有2个入口
        a: './app/entry-a',
        b: ['./app/entry-b1', './app/entry-b2']
    },
    // 如何输出结果：在 Webpack 经过一系列处理后，如何输出最终想要的代码。
    output: {
        // 输出文件存放的目录，必须是 string 类型的绝对路径。
        path: path.resolve(__dirname, 'dist'),
        
        // 输出文件的名称
        filename: 'bundle.js', // 完整的名称
        filename: '[name].js', // 当配置了多个 entry 时，通过名称模版为不同的 entry 生成不同的文件名称
        filename: '[chunkhash].js', // 根据文件内容 hash 值生成文件名称，用于浏览器长时间缓存文件
        
        // 发布到线上的所有资源的 URL 前缀，string 类型
        publicPath: '/assets/', // 放到指定目录下
        publicPath: '', // 放到根目录下
        publicPath: 'https://cdn.example.com/', // 放到 CDN 上去
        
        // 导出库的名称，string 类型
        // 不填它时，默认输出格式是匿名的立即执行函数
        library: 'MyLibrary',
        
        // 导出库的类型，枚举类型，默认是 var
        // 可以是 umd | umd2 | commonjs2 | commonjs | amd | this | var | assign | window | global | jsonp ，
        libraryTarget: 'umd', 
        
        // 是否包含有用的文件路径信息到生成的代码里去，boolean 类型
        pathinfo: true, 
        
        // 附加 Chunk 的文件名称
        chunkFilename: '[id].js',
        chunkFilename: '[chunkhash].js',
        
        // JSONP 异步加载资源时的回调函数名称，需要和服务端搭配使用
        jsonpFunction: 'myWebpackJsonp',
        
        // 生成的 Source Map 文件名称
        sourceMapFilename: '[file].map',
        
        // 浏览器开发者工具里显示的源码模块名称
        devtoolModuleFilenameTemplate: 'webpack:///[resource-path]',
        
        // 异步加载跨域的资源时使用的方式
        crossOriginLoading: 'use-credentials',
        crossOriginLoading: 'anonymous',
        crossOriginLoading: false,
    },

    // 配置模块相关
    module: {
        rules: [ // 配置 Loader
            {  
                test: /\.jsx?$/, // 正则匹配命中要使用 Loader 的文件
                include: [ // 只会命中这里面的文件
                    path.resolve(__dirname, 'app')
                ],
                exclude: [ // 忽略这里面的文件
                    path.resolve(__dirname, 'app/demo-files')
                ],
                use: [ // 使用那些 Loader，有先后次序，从后往前执行
                    'style-loader', // 直接使用 Loader 的名称
                    {
                        loader: 'css-loader',      
                        options: { // 给 html-loader 传一些参数
                        }
                    }
                ]
            },
        ],
        noParse: [ // 不用解析和处理的模块
            /special-library\.js$/  // 用正则匹配
        ],
    },

    // 配置插件
    plugins: [
    ],
    // 配置寻找模块的规则
    resolve: { 
        modules: [ // 寻找模块的根目录，array 类型，默认以 node_modules 为根目录
            'node_modules',
            path.resolve(__dirname, 'app')
        ],
        extensions: ['.js', '.json', '.jsx', '.css'], // 模块的后缀名
        alias: { // 模块别名配置，用于映射模块
            // 把 'module' 映射 'new-module'，同样的 'module/path/file' 也会被映射成 'new-module/path/file'
            'module': 'new-module',
            // 使用结尾符号 $ 后，把 'only-module' 映射成 'new-module'，
            // 但是不像上面的，'module/path/file' 不会被映射成 'new-module/path/file'
            'only-module$': 'new-module', 
        },
        alias: [ // alias 还支持使用数组来更详细的配置
            {
                name: 'module', // 老的模块
                alias: 'new-module', // 新的模块
                // 是否是只映射模块，如果是 true 只有 'module' 会被映射，如果是 false 'module/inner/path' 也会被映射
                onlyModule: true, 
            }
        ],
        symlinks: true, // 是否跟随文件软链接去搜寻模块的路径
        descriptionFiles: ['package.json'], // 模块的描述文件
        mainFields: ['main'], // 模块的描述文件里的描述入口的文件的字段名称
        enforceExtension: false, // 是否强制导入语句必须要写明文件后缀
    },
    // 输出文件性能检查配置
    performance: { 
        hints: 'warning', // 有性能问题时输出警告
        hints: 'error', // 有性能问题时输出错误
        hints: false, // 关闭性能检查
        maxAssetSize: 200000, // 最大文件大小 (单位 bytes)
        maxEntrypointSize: 400000, // 最大入口文件大小 (单位 bytes)
        assetFilter: function(assetFilename) { // 过滤要检查的文件
            return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
        }
    },
    devtool: 'source-map', // 配置 source-map 类型
    context: __dirname, // Webpack 使用的根目录，string 类型必须是绝对路径
    // 配置输出代码的运行环境
    target: 'web', // 浏览器，默认
    target: 'webworker', // WebWorker
    target: 'node', // Node.js，使用 `require` 语句加载 Chunk 代码
    target: 'async-node', // Node.js，异步加载 Chunk 代码
    target: 'node-webkit', // nw.js
    target: 'electron-main', // electron, 主线程
    target: 'electron-renderer', // electron, 渲染线程
    externals: { // 使用来自 JavaScript 运行环境提供的全局变量
        jquery: 'jQuery'
    },
    stats: { // 控制台输出日志控制
        assets: true,
        colors: true,
        errors: true,
        errorDetails: true,
        hash: true,
    },
    devServer: { // DevServer 相关的配置
        proxy: { // 代理到后端服务接口
            '/api': 'http://localhost:3000'
        },
        contentBase: path.join(__dirname, 'public'), // 配置 DevServer HTTP 服务器的文件根目录
        compress: true, // 是否开启 gzip 压缩
        historyApiFallback: true, // 是否开发 HTML5 History API 网页
        hot: true, // 是否开启模块热替换功能
        https: false, // 是否开启 HTTPS 模式
    },
    profile: true, // 是否捕捉 Webpack 构建的性能信息，用于分析什么原因导致构建性能不佳
    cache: false, // 是否启用缓存提升构建速度
    watch: true, // 是否开始
    watchOptions: { // 监听模式选项
        // 不监听的文件或文件夹，支持正则匹配。默认为空
        ignored: /node_modules/,
        // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
        // 默认为300ms 
        aggregateTimeout: 300,
        // 判断文件是否发生变化是不停的去询问系统指定文件有没有变化，默认每隔1000毫秒询问一次
        poll: 1000
    },
}
```


#### 判断如何配置Webpack？
通常你可用如下经验去判断如何配置Webpack：


1. 想让源文件加入到构建流程中去被Webpack控制，配置entry。
2. 想自定义输出文件的位置和名称，配置output。
3. 想自定义寻找依赖模块时的策略，配置resolve。
4. 想自定义解析和转换文件的策略，配置module，通常是配置module.rules里的Loader。
5. 其它的大部分需求可能要通过Plugin去实现，配置plugin。