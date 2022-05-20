#### .vue组件的scoped属性的作用？
当`<style>`标签有scoped属性时，它的CSS只作用于当前组件中的元素。


你可以在一个组件中同时使用有scoped和非scoped样式：


```html
<style>
/* 全局样式 */
</style>

<style scoped>
/* 本地样式 */
</style>
```


但有时在vue-cli中引入UI库后，修改UI库一些样式，可能不生效。


#### 如何让CSS只在当前组件中起作用？
可在vue组件中，引入css预处理器，在最外层div容器里，加上一个最外层class，这样不用scoped属性，即可让css只在当前组件中起作用。


```html
<template>
	<div class="commonPageWrap">
		
	</div>
</template>
<script type="text/javascript">
	export default{
		data () {
			return {
				index:0
			}
		},
		mounted () {

		},
		components : {
			
		},
		methods : {
			
		}
	}
</script>

<style type="text/css" lang="less">
    .commonPageWrap{
        
    }
</style>
```


#### 如何适配移动端？
不要仅简单的回答用媒体查询或者用JS单位适配函数，回答用JS尤其是最不好的。


结果我们发现，想让网页在一堆不同的设备上合理展示，只需要在最终产品上添加一点CSS媒体查询就可以了。这件事情之所以这么简单，关键在于我们的布局原本就是弹性可伸缩的。因此，优化网页在小屏幕上的表现，其实只意味着把一些外边距收拢到最小程度，然后把因为屏幕太窄而无法显示成双列的侧栏调整为单列布局而已。


在《css揭秘》一书中关于响应式布局推荐有一些布局建议：<br>
1. 使用百分比长度来取代固定长度。如果做不到这一点，也应该尝试使用与视口相关的单位(vw、vh、vmin和vmax)，它们的值解析为视口宽度或者高度的百分比。
2. 当你需要在较大分辨率下得到固定宽度时，使用max-width而不是width，因为它可以适应较小的分辨率，而无需使用媒体查询。
3. 不要忘记为替换元素(比如img、object、video、iframe等)设置一个max-width，值为100%。
4. 假如背景图片需要完整地铺满一个容器，不管容器的尺寸如何变化，background-size:cover; 这个属性都可以做到。但是，我们也要时刻牢记--带宽并不是无限的，因此在移动网页中通过css把一张大图缩小显示往往是不明智的。
5. 当图片(或其他元素)以行列式进行布局时，让视口的宽度来决定列的数量。弹性盒布局(即Flexbox)或者 display:inline-block; 加上常规的文本折行行为，都可以实现这一点。
6. 在使用多行文本时，指定column-width(列宽)而不是指定column-count(列数)，这样它就可以在较小的屏幕上自动显示为单例布局。


#### 移动端常用媒体查询？
```css
/*引入适配的less*/
html {
    font-size: 16px;
}
@media only screen and (min-width: 320px) {
    html {
        font-size: 13.6533px !important;
    }
}
@media only screen and (min-width: 360px) {
    html {
        font-size: 15.36px !important;
    }
}
@media only screen and (min-width: 375px) {
    html {
        font-size: 16px !important;
    }
}
@media only screen and (min-width: 400px) {
    html {
        font-size: 17.0667px !important;
    }
}
@media only screen and (min-width: 414px) {
    html {
        font-size: 17.664px !important;
    }
}
@media only screen and (min-width: 480px) {
    html {
        font-size: 20.48px !important;
    }
}
@media only screen and (min-width: 750px) {
    html {
        font-size: 32px !important;
    }
}
```


#### 媒体查询less写法？
```less
@device-bps: 320px, 360px,375px, 400px, 414px, 480px, 750px;
.html-font-size(@i, @design-font-size, @design-width) when (@i <= length(@device-bps)) {  //注意less数组是从1开始的
  @bp: extract(@device-bps, @i);
  @font: round(@bp / @design-width * @design-font-size, 4);
  @media only screen and (min-width: @bp){
    html {
      font-size: @font !important;
    }
  }
  .html-font-size((@i + 1), @design-font-size, @design-width);
}

.html-font-size(@design-font-size, @design-width) {
   html {
     font-size: @design-font-size;
   }
   .html-font-size(1, @design-font-size, @design-width);
}

.px2rem(@name, @px, @design-font-size) {
    @{name}: 1rem * round(@px / @design-font-size, 2);
}

@design-font-size : 16px; 
@design-width : 375px;

.px2rem(@name, @px) {
    .px2rem(@name, @px, @design-font-size);
}
```


#### css如何实现垂直居中样式？
外层边框部分高度固定，内部块级结构居中。


html结构：


```html
<div class="verMiddle outerLayerWrap">
    <div class="innerLayer"></div>
</div>
```


style样式：


```css
<!-- 通用样式 -->
.verMiddle::before {
    display: inline-block;
    vertical-align: middle;
    content: '';
    height: 100%;
    width: 0;
    overflow: hidden;
}
<!-- 外层样式 -->
.outerLayerWrap {
    width:100px;
    height:100px;
    border:1px solid #ccc;
    text-align:center;
}
<!-- 内层样式 -->
.innerLayer {
    width:40px;
    height:40px;
    background:red;
    vertical-align:middle;
    display:inline-block;
}
```


#### vue-cli工程中如何使用背景图？
**通过import引入**


首先，引入要使用的背景图片：


```html
<script type="text/javascript">
    import  cover  from  "../assets/images/cover.png";
    export  default{
        ...
    }
</script>
```


然后，通过v-bind:style使用：


```html
<div :style="{ backgroundImage:'url(' + cover + ')' }"></div>
```


**通过require引入**


直接通过v-bind和require配合使用：


```html
<div :style="{ backgroundImage:'url(' + require('../assets/images/couver.png') + ')' }"></div>
```


#### 如何解决禁用表单后移动端样式不统一问题？
```css
input:disabled{
    color: 'xxx';
    opacity:1;
    //text-fill-color文本填充颜色，只兼容webkit内核
    -webkit-text-fill-color:xxx;
    -webkit-opacity:1;
    font-size:16px;
}
```


#### 如何实现文件禁止折行，超过部分用省略号隐藏？
```css
.hidden {
    white-space:nowrap; 
    overflow:hidden; 
    text-overflow:ellipsis;
    <!-- 强制不换行，超出部分隐藏且以省略号形式出现 -->
}
```


#### 如何实现文件显示两行，超过部分用省略号隐藏？
```css
.hidden {
    overflow:hidden; 
    text-overflow:ellipsis;
    display:-webkit-box; // 将对象作为弹性伸缩盒子模型显示。
    -webkit-box-orient:vertical;// 从上到下垂直排列子元素(设置伸缩盒子的子元素排列方式)
    -webkit-line-clamp:2;// 这个属性不是css的规范属性，需要组合上面两个属性，表示显示的行数。
}
```


#### 如何在vue-cli工程中引入全局less、sass变量？
当我们使用vue-cli开发的时候，会使用一些全局配置，但是每当我们使用这些全局变量时，每个Vue组件都必须导入该文件@import ‘rem.less’，那得重复做这样的工作很多次，过程显得非常繁琐。


**全局引入less方法**


1.在package.json下检查是否有sass-resources-loader这个插件，如有则进行下一步，如没有，则进行安装：


```
npm install sass-resource-loader --save-dev
```


2.找到项目build目录下的util.js文件：在`exports.cssLoaders = function (options) {}`里面添加方法：


```javascript
function resolveResource(name) {
    return path.resolve(__dirname, '../src/styles/' + name)
}
function generateLessResourceLoader() {
    let loaders = [
        cssLoader,
        // 'postcss-loader',
        'sass-loader',
        {
            loader: 'sass-resources-loader',
            options: {
                resources: [resolveResource('rem.less')] // 需要一个全局路径
            }
        }
    ];
    if (options.extract) {
        return ExtractTextPlugin.extract({
            use: loaders,
            fallback: 'vue-style-loader'
        })
    } else {
        return ['vue-style-loader'].concat(loaders)
    }
}
```


3.然后修改util.js文件下的return下的less：


```javascript
return {
    css:  generateLoaders(),
    postcss:  generateLoaders(),
    //修改less
    less:  generateLessResourceLoader('less'),
    sass:  generateLoaders('sass', { indentedSyntax:  true }),
    scss:  generateLoaders('sass'),
    stylus:  generateLoaders('stylus'),
    styl:  generateLoaders('stylus')
}
```


4.然后src/style/rem.less文件下的less变量或者mixin即可全局直接使用。