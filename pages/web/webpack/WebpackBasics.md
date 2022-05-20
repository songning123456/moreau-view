#### 什么是模块化？为什么出现模块化？
模块化是指把一个复杂的系统分解到多个模块以方便编码。


很久以前，开发网页要通过命名空间的方式来组织代码，例如jQuery库把它的API都放在了window.$下，在加载完jQuery后其他模块再通过window.$去使用jQuery。这样做有很多问题，其中包括：


```
1. 命名空间冲突，两个库可能会使用同一个名称，例如Zepto也被放在window.$下；
2. 无法合理地管理项目的依赖和版本；
3. 无法方便地控制依赖的加载顺序。
```


当项目变大时这种方式将变得难以维护，需要用模块化的思想来组织代码。


#### 构建的作用及常见功能是什么？
构建就是当源代码无法直接运行时，通过转化将源代码转换成可执行的JavaScript、Css、HTML代码。一般包括如下内容：


1. 代码转换：TypeScript编译成JavaScript、SCSS 编译成CSS等。
2. 文件优化：压缩JavaScript、CSS、HTML 代码，压缩合并图片等。
3. 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
4. 模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
5. 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
6. 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
7 .自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。


#### 你了解的构建工具有哪些，各自有什么优缺点？
**Npm Script**


Npm Script是一个任务执行者。Npm是在安装Node.js时附带的包管理器，Npm Script则是Npm内置的一个功能，允许在package.json文件里面使用scripts字段定义任务：


```
{
    "scripts": {
        "dev": "node dev.js",
        "pub": "node build.js"
    }
}
```


里面的scripts字段是一个对象，每个属性对应一段Shell脚本，以上代码定义了两个任务dev和pub。 其底层实现原理是通过调用Shell去运行脚本命令，例如执行npm run pub命令等同于执行命令node build.js。Npm Script的优点是内置，无须安装其他依赖。Npm Script的缺点是功能太简单，虽然提供了pre和post两个钩子，但不能方便地管理多个任务之间的依赖。


**Grunt**


Grunt和Npm Script类似，也是一个任务执行者。Grunt有大量现成的插件封装了常见的任务，也能管理任务之间的依赖关系，自动化执行依赖的任务，每个任务的具体执行代码和依赖关系写在配置文件Gruntfile.js里，例如：


```javascript
module.exports = function(grunt) {
    // 所有插件的配置信息
    grunt.initConfig({
    // uglify 插件的配置信息
    uglify: {
        app_task: {
            files: {
            'build/app.min.js': ['lib/index.js', 'lib/test.js']
            }
        }
    },
    // watch 插件的配置信息
    watch: {
        another: {
            files: ['lib/*.js'],
        }
    }
    });

    // 告诉 grunt 我们将使用这些插件
    grunt.loadNpmTasks('grunt-contrib-uglify');
    grunt.loadNpmTasks('grunt-contrib-watch');
    
    // 告诉grunt当我们在终端中启动 grunt 时需要执行哪些任务
    grunt.registerTask('dev', ['uglify','watch']);
};
```


在项目根目录下执行命令grunt dev就会启动JavaScript文件压缩和自动刷新功能。


Grunt的优点是灵活，它只负责执行你定义的任务；大量的可复用插件封装好了常见的构建任务。Grunt的缺点是集成度不高，要写很多配置后才可以用，无法做到开箱即用。Grunt相当于进化版的Npm Script，它的诞生其实是为了弥补Npm Script的不足。


**Gulp**


Gulp是一个基于流的自动化构建工具。 除了可以管理和执行任务，还支持监听文件、读写文件。Gulp被设计得非常简单，只通过下面5个方法就可以胜任几乎所有构建场景：


```
1. 通过gulp.task注册一个任务；
2. 通过gulp.run执行任务；
3. 通过gulp.watch监听文件变化；
4. 通过gulp.src读取文件；
5. 通过gulp.dest写文件。
```


Gulp的最大特点是引入了流的概念，同时提供了一系列常用的插件去处理流，流可以在插件之间传递，大致使用如下：


```javascript
// 引入 Gulp
var gulp = require('gulp'); 
// 引入插件
var jshint = require('gulp-jshint');
var sass = require('gulp-sass');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');

// 编译 SCSS 任务
gulp.task('sass', function() {
    // 读取文件通过管道喂给插件
    gulp.src('./scss/*.scss')
    // SCSS 插件把 scss 文件编译成 CSS 文件
    .pipe(sass())
    // 输出文件
    .pipe(gulp.dest('./css'));
});

// 合并压缩 JS
gulp.task('scripts', function() {
    gulp.src('./js/*.js')
    .pipe(concat('all.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./dist'));
});

// 监听文件变化
gulp.task('watch', function(){
    // 当 scss 文件被编辑时执行 SCSS 任务
    gulp.watch('./scss/*.scss', ['sass']);
    gulp.watch('./js/*.js', ['scripts']);    
});
```


Gulp的优点是好用又不失灵活，既可以单独完成构建也可以和其它工具搭配使用。Gulp的缺点是和Grunt类似，集成度不高，要写很多配置后才可以用，无法做到开箱即用。可以将Gulp看作Grunt的加强版。相对于Grunt，Gulp增加了监听文件、读写文件、流式处理的功能。


**Fis3**


Fis3是一个来自百度的优秀国产构建工具。相对于Grunt、Gulp这些只提供基本功能的工具，Fis3集成了Web开发中的常用构建功能，如下所述。


1. 读写文件：通过fis.match读文件，release配置文件输出路径。
2. 资源定位：解析文件之间的依赖关系和文件位置。
3. 文件指纹：通过useHash配置输出文件时给文件URL加上md5戳来优化浏览器缓存。
4. 文件编译：通过parser配置文件解析器做文件转换，例如把ES6编译成ES5。
5. 压缩资源：通过optimizer配置代码压缩方法。
6. 图片合并：通过spriter配置合并CSS里导入的图片到一个文件来减少HTTP请求数。


```javascript
// 加 md5
fis.match('*.{js,css,png}', {
    useHash: true
});

// fis3-parser-typescript 插件把 TypeScript 文件转换成 JavaScript 文件
fis.match('*.ts', {
    parser: fis.plugin('typescript')
});

// 对 CSS 进行雪碧图合并
fis.match('*.css', {
    // 给匹配到的文件分配属性 `useSprite`
    useSprite: true
});

// 压缩 JavaScript
fis.match('*.js', {
    optimizer: fis.plugin('uglify-js')
});

// 压缩 CSS
fis.match('*.css', {
    optimizer: fis.plugin('clean-css')
});

// 压缩图片
fis.match('*.png', {
    optimizer: fis.plugin('png-compressor')
});
```


可以看出Fis3很强大，内置了许多功能，无须做太多配置就能完成大量工作。Fis3的优点是集成了各种Web开发所需的构建功能，配置简单开箱即用。Fis3的缺点是目前官方已经不再更新和维护，不支持最新版本的Node.js。Fis3是一种专注于Web开发的完整解决方案，如果将Grunt、Gulp比作汽车的发动机，则可以将Fis3比作一辆完整的汽车。


**Webpack**


Webpack是一个打包模块化JavaScript的工具，在Webpack里一切文件皆模块，通过Loader转换文件，通过Plugin注入钩子，最后输出由多个模块组合成的文件。Webpack专注于构建模块化项目。其官网的首页图很形象的画出了Webpack是什么，如下：


![Webpack](/images/Web/Webpack.png)


一切文件JavaScript、CSS、SCSS、图片、模板，在Webpack眼中都是一个个模块，这样的好处是能清晰的描述出各个模块之间的依赖关系，以方便Webpack对模块进行组合和打包。 经过Webpack的处理，最终会输出浏览器能使用的静态资源。Webpack具有很大的灵活性，能配置如何处理文件，大致使用如下：


```javascript
module.exports = {
    // 所有模块的入口，Webpack 从入口开始递归解析出所有依赖的模块
    entry: './app.js',
    output: {
        // 把入口所依赖的所有模块打包成一个文件 bundle.js 输出 
        filename: 'bundle.js'
    }
}
```


Webpack的优点是：


```
1. 专注于处理模块化的项目，能做到开箱即用一步到位；
2. 通过Plugin扩展，完整好用又不失灵活；
3. 使用场景不仅限于Web开发；
4. 社区庞大活跃，经常引入紧跟时代发展的新特性，能为大多数场景找到已有的开源扩展；
5. 良好的开发体验。
```


Webpack的缺点是只能用于采用模块化开发的项目。


**Rollup**


Rollup是一个和Webpack很类似但专注于ES6的模块打包工具。Rollup的亮点在于能针对ES6源码进行Tree Shaking以去除那些已被定义但没被使用的代码，以及Scope Hoisting以减小输出文件大小提升运行性能。 然而Rollup的这些亮点随后就被Webpack模仿和实现。 由于Rollup的使用和Webpack差不多，这里就不详细介绍如何使用了，而是详细说明它们的差别：


1. Rollup是在Webpack流行后出现的替代品；
2. Rollup生态链还不完善，体验不如Webpack；
3. Rollup功能不如Webpack完善，但其配置和使用更加简单；
4. Rollup不支持Code Spliting，但好处是打包出来的代码中没有Webpack那段模块的加载、执行和缓存的代码。


Rollup在用于打包JavaScript库时比Webpack更加有优势，因为其打包出来的代码更小更快。但功能不够完善，很多场景都找不到现成的解决方案。


#### 简单介绍一下webpack？
Webpack是一个打包模块化javascript的工具，它会从main.js出发，识别出源码中的模块化导入语句，递归地找出入口文件的所有依赖，将入口和其所有依赖打包到一个单独的文件中。从webpack2版本开始，webpack已经内置了对ES6、CommonJS、AMD模块化语句的支持。


#### Loader机制的作用是什么？
Loader可以看作具有文件转换功能的翻译员，配置里的module.rules数组配置了一组规则，告诉Webpack在遇到哪些文件时使用哪些Loader去加载和转换。


#### css-loader与style-loader的作用？
css-loader读取CSS文件；style-loader把CSS内容注入到JavaScript里。


#### 配置Loader时需要注意的地方？
use属性的值需要是一个由Loader名称组成的数组，Loader的执行顺序是由后到前的；每一个Loader都可以通过URL querystring的方式传入参数，例如css-loader?minimize中的minimize告诉css-loader要开启CSS压缩。


#### Plugin(插件)的作用是什么？
Plugin是用来扩展Webpack功能的，通过在构建流程里注入钩子实现，它给Webpack带来了很大的灵活性。Webpack是通过plugins属性来配置需要使用的插件列表的。plugins属性是一个数组，里面的每一项都是插件的一个实例，在实例化一个组件时可以通过构造函数传入这个组件支持的配置属性。


#### ExtractTextPlugin插件的作用？
ExtractTextPlugin插件的作用是提取出JavaScript代码里的CSS到一个单独的文件。对此你可以通过插件的filename属性，告诉插件输出的CSS文件名称是通过[name]_[contenthash:8].css字符串模版生成的，里面的[name]代表文件名称，[contenthash:8]代表根据文件内容算出的8位hash值， 还有很多配置选项可以在ExtractTextPlugin的主页上查到。


#### DevServer开发工具？
DevServer会启动一个HTTP服务器用于服务网页请求，同时会帮助启动Webpack ，并接收Webpack发出的文件更变信号，通过WebSocket协议自动刷新网页做到实时预览。


```
// 安装DevServer
npm i -D webpack-dev-server
```


#### 实时预览？
Webpack在启动时可以开启监听模式，开启监听模式后Webpack会监听本地文件系统的变化，发生变化时重新构建出新的结果。Webpack默认是关闭监听模式的，你可以在启动Webpack时通过webpack --watch来开启监听模式。


通过DevServer启动的Webpack会开启监听模式，当发生变化时重新执行完构建后通知DevServer。DevServer会让Webpack在构建出的JavaScript代码里注入一个代理客户端用于控制网页，网页和DevServer之间通过WebSocket协议通信， 以方便DevServer主动向客户端发送命令。DevServer在收到来自**Webpack的文件变化**通知时通过注入的客户端控制网页刷新。


#### 什么是模块热替换？
模块热替换能做到在不重新加载整个网页的情况下，通过将被更新过的模块替换老的模块，再重新执行一次来实现实时预览。


模块热替换相对于默认的刷新机制能提供更快的响应和更好的开发体验。模块热替换默认是关闭的，要开启模块热替换，你只需在启动DevServer时带上--hot参数，重启DevServer后再去更新文件就能体验到模块热替换的神奇了。


#### 什么是Source Map及其使用？
Source Map能够提供将压缩文件恢复到源文件原始位置的映射代码的方式。这意味着你可以在优化压缩代码后轻松的进行调试。在编译器输出的代码上进行断点调试是一件辛苦和不优雅的事情，调试工具可以通过Source Map映射代码，让你在源代码上断点调试。


Source Map使用：Webpack支持生成Source Map，只需在启动时带上--devtool source-map参数。加上参数重启DevServer后刷新页面，再打开Chrome浏览器的开发者工具，就可在Sources栏中看到可调试的源代码了。


#### Webpack的几个核心概念？
1. Entry：入口，Webpack执行构建的第一步将从Entry开始，可抽象成输入。
2. Module：模块，Webpack里一切皆模块，一个模块对应着一个文件。Webpack会从配置的Entry开始递归找出所有依赖的模块。
3. Chunk：代码块，一个Chunk由多个模块组合而成，用于代码合并与分割。
4. Loader：模块转换器，用于把模块原内容按照需求转换成新内容。
5. Plugin：扩展插件，在Webpack构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要的事情。
6. Output：输出结果，在Webpack经过一系列处理并得出最终想要的代码后输出结果。


#### Webpack简单工作原理？
Webpack启动后会从Entry里配置的Module开始递归解析Entry依赖的所有Module。每找到一个Module，就会根据配置的Loader去找出对应的转换规则，对Module进行转换后，再解析出当前Module依赖的Module。这些模块会以Entry为单位进行分组，一个Entry和其所有依赖的Module被分到一个组也就是一个Chunk。最后Webpack会把所有Chunk转换成文件输出。在整个流程中Webpack会在恰当的时机执行Plugin里定义的逻辑。