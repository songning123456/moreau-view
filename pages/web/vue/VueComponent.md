#### Vue组件间如何进行通信？
本章主要讲解的同一个页面index.vue下的不同组件间footer.vue和header.vue，header.vue里面又使用了headerLeft.vue组件通信的问题。例如：父子组件通信index.vue与header.vue同级组件通信header.vue与footer.vue、父孙组件通信index.vue与headerLeft.vue。


对于这个问题的一般回答：<span class='forest-green'>在项目中采用bus中央事件总线的方法</span>。


对于这个问题的详细解答：


<span class='forest-green'>组件间数据相互传递：</span><br>
1. 可以通过props向子组件中传值；<br>
2. 可以通过v-model语法糖进行向子组件传值，子组件调用父组件的方法；<br>
3. 可以通过.sync修饰符，进行父子组件间数据的相互传递；<br>
4. 可以通过v-bind="$attrs"，将父组件的值传递到孙组件中。


<span class='forest-green'>组件间方法相互调用：</span><br>
1. 可以通过v-on监听方法，进行子组件调用父组件的方法；<br>
2. 可以通过v-on="$listeners"，进行孙组件调用父组件的方法。


<span class='forest-green'>组件间属性相互查询：</span><br>
1. 可以通过ref属性，父组件查询子组件的方法；<br>
2. 可以通过this.$parent属性，子组件查询父组件；<br>
3. 可以通过this.$children属性，父组件查询子组件。


#### Vue中父子组件、同级组件间如何通信？
Vue中央事件总线这种方法适用于任何情况的父子组件通信，同级别组件通信，相当于组件通信间的万金油。但是碰到多人合作时，代码的维护性较低，代码可读性低(这个缺点可以忽略)。


在vue-cli中使用集中式事件管理机制：


1.在src新建emit/emit.js，.js文件内容：


```javascript
import Vue from 'vue';

let Emit = new Vue({});

export {
    Emit
}
```


2.在src/main.js下引入：


```html
import { Emit } from './emit/emit.js';

Vue.prototype.Emit = Emit;
```


示例：


index.vue为父组件，两个子组件header.vue、footer.vue。


在mounted生命周期里通过this.Emit.$on()监听。在destroyed生命周期里面通过this.Emit.$off()解除绑定。一定要解除绑定事件！


```html
//父组件：index.vue：
<template>
	<div class="indexPageWrap">
		<header></header>
        <footer></footer>
	</div>
</template>
<script type="text/javascript">
   import header from './header.vue';
   import footer from './footer.vue';
	export default{
		data () {
			return {
				index:0
			}
		},
		mounted () {
			this.Emit.$on('fromHeader',this.indexFormHeader);
            this.Emit.$on('fromFooter',this.indexFromFooter);
		},
        //注意：在组件销毁时，一定要解除绑定事件：
        destroyed(){
                this.Emit.$off('fromHeader');
                this.Emit.$off('fromFooter');
        },
		components : {
			header
		},
		methods : {
			indexFormHeader(vlaue){
            	console.log('from header');
                console.log(value)
            },
            indexFromFooter(){
            	console.log('from footer')
            }
		}
	}
</script>
<style type="text/css">
	
</style>
```


子组件header.vue通过this.Emit.$emit()传递数据。


```html
//子组件 header.vue
<template>
	<div class="headerPageWrap">
		<div @click="headerEmit">Emit<div>
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
			this.Emit.$on('headerTofooter',this.headerFromFoot)
		},
		components : {
			
		},
		methods : {
			headerEmit(){
            	this.Emit.$emit('fromHeader',{value:"123"}); //可以传递数据
            },
            headerFromFoot(){
            	console.log('from footer');
            }
		}
	}
</script>
<style type="text/css">
	
</style>
```


```html
//子组件 footer.vue
<template>
	<div class="footerPageWrap">
		<div @click="footerEmit">Emit<div>
        <div @click="footerEmitToHeader">Emit<div>
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
			footerEmit(){
            	this.Emit.$emit('fromFooter'); //不传递数据
            },
            footerEmitToHeader(){
               this.Emit.$emit('headerTofooter'); //不传递数据
            }
		}
	}
</script>
<style type="text/css">
	
</style>
```


#### Vue父组件如何向子组件中传递数据？
可以分为**静态传递**或者**v-bind**动态传递：


例如给prop传入一个静态的值：


```html
<blog-post title="My journey with Vue"></blog-post>
```


也知道prop可以通过v-bind动态赋值，例如：


```html
<!-- 动态赋予一个变量的值 -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- 动态赋予一个复杂表达式的值 -->
<blog-post v-bind:title="post.title + ' by ' + post.author.name"></blog-post>
```


在上述两个示例中，我们传入的值都是字符串类型的，但实际上任何类型的值都可以传给一个prop。子组件接受：


```html
    export default {
        props : ["title"]
    }
    //或者
    export default {
        props : {
            title:{
                type:string,
                default:""
            }
        }
    }
```


**传入一个数字：**


```html
<!-- 即便 `42` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:likes="42"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:likes="post.likes"></blog-post>
```


**传入一个布尔值：**


```html
<!-- 包含该 prop 没有值的情况在内，都意味着 `true`。-->
<blog-post is-published></blog-post>

<!-- 即便 `false` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:is-published="false"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:is-published="post.isPublished"></blog-post>
```


**传入一个数组：**


```html
<!-- 即便数组是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>
```


**传入一个对象：**


```html
<!-- 即便对象是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:author="{ name: 'Veronica', company: 'Veridian Dynamics' }"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:author="post.author"></blog-post>
```


**传入一个对象的所有属性：**


如果你想要将一个对象的所有属性都作为prop传入，你可以使用不带参数的v-bind(取代v-bind:prop-name)。例如，对于一个给定的对象post：


```html
post: {
    id: 1,
    title: 'My Journey with Vue'
}
```


下面的模板：


```html
<blog-post v-bind="post"></blog-post>
```


等价于：


```html
<blog-post v-bind:id="post.id" v-bind:title="post.title"></blog-post>
```


#### v-model语法糖在组件上的使用？
需要实现效果：如果在一个页面中我们需要引入一个弹窗组件，点击按钮a显示弹窗，然后点击弹窗的关闭按钮，关闭弹窗，用v-model实现。使用v-model来进行双向数据绑定的时：


```html
<input v-model="something">
```


仅仅是一个语法糖：


```html
<input v-bind:value="something" v-on:input="something = $event.target.value">
```


所以在组件中使用的时候，相当于下面的简写：


```html
<custom v-bind:value="something" v-on:input="something = $event.target.value"></custom>
```


所以要组件的v-model生效，它必须：<br>
1. 接受一个value属性；<br>
2. 在有新的value时触发input事件。


使用示例：
 

```html
<template>
	<div class="toggleClassWrap">
	    <modelVue v-if="ifShow" v-model="ifShow"></modelVue>
    </div>
</template>
<script type="text/javascript">
	import modelVue from '../../components/model.vue'
	export default{
		data () {
			return {
				ifShow:true,
			}
		},
		components : {
			modelVue
		}
	}
</script>
```


model.vue组件


```html
<template>
    <div id="showAlert">
        <div>showAlert 内容</div>
        <button class="close" @click="close">关闭</button>
    </div>
</template>

<script>
    export default{
        props:{
            value:{
                type:Boolean,
                default:false,
            }
        },
        data(){
            return{}
        },
        mounted(){
        },
        methods:{
            close(){
                this.$emit('input',false);//传值给父组件, 让父组件监听到这个变化
            }
        },
    }
</script>

<style scoped>
    .close{
        background:red;
        color:white;
    }
</style>
```


#### .sync修饰符的作用？
允许prop进行双向绑定，以this.$emit(update:PropName, newValue)的模式触发事件。


```html
<text-document v-bind:title.sync="doc.title"></text-document>
```


相当于：


```html
<text-document v-bind:title="doc.title" v-on:update:title="doc.title = $event"></text-document>
```


以在index.vue下引入childrenOne子组件为例，使用.sync属性，会在mounted生命周期里面alert弹出childrenOne，而不是index。


```html
<template>
	<div class="vuexWrap common">
            <childrenOne :title.sync="doc.title"></childrenOne>
		</div>
	</div>
</template>
<script type="text/javascript">
	import childrenOne from '../../components/childrenOne.vue'
	export default{
		data () {
			return {
				doc:{
					title:'index'
				},
			}
		},
		mounted (){
             //childrenOne
			alert(this.doc.title);
		},
		components : {
			childrenOne
		}
	}
</script>
```


在childrenOne.vue的生命周期mounted里面通过this.$emit('update:title', this.newTitle)设置title属值。


```html
<template>
	<div class="OneWrap common">
        {{title}}
	</div>
</template>
<script type="text/javascript">
	export default{
		props:{
			title:""
		},
		data () {
			return {
				newTitle:"childrenOne"
			}
		},
		mounted (){
			this.$emit('update:title', this.newTitle);
		},
	}
</script>
```