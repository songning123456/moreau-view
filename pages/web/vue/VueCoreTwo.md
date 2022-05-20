#### Vue中子组件调用父组件的方法？
通过v-on监听和$emit触发来实现：<br>
1. 在父组件中通过v-on监听当前实例上的自定义事件；<br>
2. 在子组件中通过'$emit'触发当前实例上的自定义事件。


父组件：


```html
<template>
    <div class="fatherPageWrap">
        <h1>这是父组件</h1>
        <!-- 引入子组件，v-on监听自定义事件 -->
        <emitChild v-on:emitMethods="fatherMethod"></emitChild>
    </div>
</template>

<script type="text/javascript">
    import emitChild from '@/page/children/emitChild.vue';
	export default{
		data () {
		    return {}
		},
		components : {
                    emitChild
		},
		methods : {
		    fatherMethod(params){
                alert(JSON.stringify(params));
            }
		}
	}
</script>
```


子组件：


```html
<template>
    <div class="childPageWrap">
        <h1>这是子组件</h1>
    </div>
</template>

<script type="text/javascript">
    export default{
		data () {
		   return {}
		},
		mounted () {
            //通过 emit 触发
            this.$emit('emitMethods',{"name" : 123});
		}
	}
</script>
```


结果：子组件会调用父组件的fatherMethod()方法，该并且会alert传递过去的参数：{"name":123}。


#### Vue中keep-alive组件的作用？
<span class='forest-green'>keep-alive</span>主要用于保留组件状态或避免重新渲染。


比如有一个列表页面和一个详情页面，那么用户就会经常执行打开详情=>返回列表=>打开详情这样的话列表和详情都是一个频率很高的页面，那么就可以对列表组件使用`<keep-alive></keep-alive>`进行缓存，这样用户每次返回列表的时候，都能从缓存中快速渲染，而不是重新渲染。


**属性：**


<span class='forest-green'>include：</span>字符串或正则表达式。只有匹配的组件会被缓存。<br>
<span class='forest-green'>exclude：</span>字符串或正则表达式。任何匹配的组件都不会被缓存。


**用法：**


包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和`<transition>`相似，`<keep-alive>`是一个抽象组件：它自身不会渲染一个DOM元素，也不会出现在父组件链中。


当组件在`<keep-alive>`内被切换，在2.2.0及其更高版本中，activated和deactivated生命周期将会在树内的所有嵌套组件中触发。


```html
<!-- 基本 -->
<keep-alive>
    <component :is="view"></component>
</keep-alive>

<!-- 多个条件判断的子组件 -->
<keep-alive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
</keep-alive>

<!-- 和 `<transition>` 一起使用 -->
<transition>
    <keep-alive>
        <component :is="view"></component>
    </keep-alive>
</transition>
```


注意：`<keep-alive>`是用在其一个直属的子组件被开关的情形。如果你在其中有v-for则不会工作。如果有上述的多个条件性的子元素，`<keep-alive>`要求同时只有一个子元素被渲染。


**include和exclude属性的使用：(2.1.0新增)**


include和exclude属性允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示：


```html
<!-- 逗号分隔字符串 -->
<keep-alive include="a,b">
    <component :is="view"></component>
</keep-alive>

<!-- 正则表达式 (使用 `v-bind`) -->
<keep-alive :include="/a|b/">
    <component :is="view"></component>
</keep-alive>

<!-- 数组 (使用 `v-bind`) -->
<keep-alive :include="['a', 'b']">
    <component :is="view"></component>
</keep-alive>
```


匹配首先检查组件自身的name选项，如果name选项不可用，则匹配它的局部注册名称(父组件components选项的键值)。匿名组件不能被匹配。


不会在函数式组件中正常工作，因为它们没有缓存实例。


#### Vue中如何编写可复用的组件？
在编写组件的时候，时刻考虑组件是否可复用是有好处的。一次性组件跟其他组件紧密耦合没关系，但是可复用组件一定要定义一个清晰的公开接口。


Vue.js组件API来自三部分：<span class='forest-green'>prop</span>、<span class='forest-green'>事件</span>、<span class='forest-green'>slot</span>：<br>
1. prop允许外部环境传递数据给组件，在vue-cli工程中也可以使用vuex等传递数据；<br>
2. 事件允许组件触发外部环境的action；<br>
3. slot允许外部环境将内容插入到组件的视图结构内。


代码示例：


```html
<my-component :foo="bar" :bar="qux"
    //子组件调用父组件方法
    @event-a="doThis" @event-b="doThat">
    <!-- content -->
<img slot="icon" src="..." />
<p slot="main-text">Hello!</p>
</my-component>
```


#### 什么是Vue生命周期和生命周期钩子函数？
Vue的生命周期是：Vue实例从创建到销毁，也就是从<span class='forest-green'>开始创建</span>、<span class='forest-green'>初始化数据</span>、<span class='forest-green'>编译模板</span>、<span class='forest-green'>挂载Dom→渲染</span>、<span class='forest-green'>更新→渲染</span>、<span class='forest-green'>卸载</span>等一系列过程。


在这个过程中也会运行一些叫做生命周期钩子的函数，这给了用户在不同阶段添加自己的代码的机会。


#### Vue生命周期钩子函数有哪些？
| 生命周期钩子函数(11个) | 类型 | 详细 |
| :----- | :----- | :----- | 
|<div style='width: 210px'>beforeCreate</div>|Function|在实例初始化之后，数据观测(data observer)和event/watcher事件配置之前被调用。|
|<div style='width: 210px'>created</div>|Function|在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测(data observer)，属性和方法的运算，watch/event事件回调。然而，挂载阶段还没开始，$el属性目前不可见。|
|<div style='width: 210px'>beforeMount</div>|Function|在挂载开始之前被调用：相关的render函数首次被调用。|
|<div style='width: 210px'>mounted</div>|Function|el被新创建的vm.$el替换，并挂载到实例上去之后调用该钩子。如果root实例挂载了一个文档内元素，当mounted被调用时vm.$el也在文档内。|
|<div style='width: 210px'>beforeUpdate</div>|Function|数据更新时调用，发生在虚拟DOM打补丁之前。这里适合在更新之前访问现有的DOM，比如手动移除已添加的事件监听器。<span class='forest-green'>该钩子在服务器端渲染期间不被调用</span>，因为只有初次渲染会在服务端进行。|
|<div style='width: 210px'>updated</div>|Function|由于数据更改导致的虚拟DOM重新渲染和打补丁，在这之后会调用该钩子。|
|<div style='width: 210px'>activated</div>|Function|keep-alive组件激活时调用，<span class='forest-green'>该钩子在服务器端渲染期间不被调用</span>。|
|<div style='width: 210px'>deactivated</div>|Function|keep-alive组件停用时调用，<span class='forest-green'>该钩子在服务器端渲染期间不被调用</span>。|
|<div style='width: 210px'>beforeDestroy</div>|Function|实例销毁之前调用。在这一步，实例仍然完全可用。<span class='forest-green'>该钩子在服务器端渲染期间不被调用</span>。|
|<div style='width: 210px'>destroyed</div>|Function|Vue实例销毁后调用。调用后，Vue实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。<span class='forest-green'>该钩子在服务器端渲染期间不被调用</span>。|
|<div style='width: 210px'>errorCaptured(2.5.0+新增)</div>|(err: Error, vm: Component, info: string) => ?boolean|当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回false以阻止该错误继续向上传播。|


mounted、updated不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用<span class='forest-green'>vm.$nextTick替换掉mounted、updated</span>：


```html
updated: function () {
    this.$nextTick(function () {
        // Code that will run only after the
        // entire view has been re-rendered
    })
}
```


http请求建议在<span class='forest-green'>created</span>生命周期内发出。


![lifecycle](/images/Web/lifecycle.png)


#### Vue如何监听键盘事件中的按键？
**按键修饰符**


在监听键盘事件时，我们经常需要检查常见的键值。Vue允许为v-on在监听键盘事件时添加按键修饰符：


```html
<!-- 只有在keyCode=13时调用vm.submit() -->
<input v-on:keyup.13="submit">
```


记住所有的keyCode比较困难，所以Vue为最常用的按键提供了别名：


```html
<input v-on:keyup.enter="submit">
<!-- 缩写语法 -->
<input @keyup.enter="submit">
```


全部的按键别名：.enter、.tab、.delete(捕获“删除”和“退格”键)、.esc、.space、.up、.down、.left、.right。


可以通过全局config.keyCodes对象自定义按键修饰符别名：


```javascript
// 可以使用 `v-on:keyup.f1`
Vue.config.keyCodes.f1 = 112;
```


**系统修饰键(2.1.0新增)**


可以用.ctrl、.alt、.shift、.meta修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器。


```
注意：在Mac系统键盘上，meta对应command键(⌘)。在Windows系统键盘meta对应Windows徽标键(⊞)。在Sun操作
系统键盘上，meta对应实心宝石键(◆)。在其他特定键盘上，尤其在MIT和Lisp机器的键盘、以及其后继产品，比如
Knight键盘、space-cadet键盘，meta被标记为“META”。在Symbolics键盘上，meta被标记为“META”或者“Meta”。
```


```html
// e.g
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```


```html
请注意修饰键与常规按键不同，在和keyup事件一起用时，事件触发时修饰键必须处于按下状态。换句话说，
只有在按住ctrl的情况下释放其它按键，才能触发keyup.ctrl。而单单释放ctrl也不会触发事件。如果你
想要这样的行为，请为ctrl换用keyCode：keyup.17。
```


**.exact修饰符(2.5.0新增)**


.exact修饰符允许你控制由精确的系统修饰符组合触发的事件。


```html
<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button @click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button @click.exact="onClick">A</button>
```


**鼠标按钮修饰符(2.2.0新增)**


.left、.right、.middle这些修饰符会限制处理函数仅响应特定的鼠标按钮。


#### Vue更新数组时触发视图更新的方法？
**变异方法**


Vue包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：<span class='forest-green'>push()</span>、<span class='forest-green'>pop()</span>、<span class='forest-green'>shift()</span>、<span class='forest-green'>unshift()</span>、<span class='forest-green'>splice()</span>、<span class='forest-green'>sort()</span>、<span class='forest-green'>reverse()</span>。


**替换数组**


例如：filter()、concat()和slice()。这些不会改变原始数组，但总是返回一个新数组。当使用这些非变异方法时，可以用新数组替换旧数组：


```javascript
example1.items = example1.items.filter(function (item) {
    return item.message.match(/Foo/)
})
```


你可能认为这将导致Vue丢弃现有DOM并重新渲染整个列表。幸运的是，事实并非如此。Vue为了使得DOM元素得到最大范围的重用而实现了一些智能的、启发式的方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。


注意事项：由于JavaScript的限制，Vue不能检测以下变动的数组：<br>
1. 当你利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue；<br>
2. 当你修改数组的长度时，例如：vm.items.length = newLength。


```javascript
// e.g
let vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
});
vm.items[1] = 'x'; // 不是响应性的
vm.items.length = 2 // 不是响应性的
```


为了解决第一类问题，以下两种方式都可以实现和vm.items[indexOfItem] = newValue 相同的效果，同时也将触发状态更新：


```javascript
// Vue.set
Vue.set(vm.items, indexOfItem, newValue);

// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue);
```


你也可以使用vm.$set实例方法，该方法是全局方法Vue.set的一个别名：


```javascript
vm.$set(vm.items, indexOfItem, newValue);
```


为了解决第二类问题，你可以使用splice：


```javascript
vm.items.splice(newLength);
```


#### Vue中对象更改检测的注意事项？
由于JavaScript的限制，<span class='forest-green'>Vue不能检测对象属性的添加或删除</span>：


```javascript
let vm = new Vue({
    data: {
        a: 1
    }
});
// `vm.a` 现在是响应式的

vm.b = 2
// `vm.b` 不是响应式的
```


对于已经创建的实例，Vue不能动态添加根级别的响应式属性。但是，可以使用Vue.set(object, key, value)方法向嵌套对象添加响应式属性。例如，对于：


```javascript
let vm = new Vue({
    data: {
        userProfile: {
            name: 'Anika'
        }
    }
})
```


你可以添加一个新的age属性到嵌套的userProfile对象：


```javascript
Vue.set(vm.userProfile, 'age', 27);
```


你还可以使用vm.$set实例方法，它只是全局Vue.set的别名：


```javascript
vm.$set(vm.userProfile, 'age', 27);
```


有时你可能需要为已有对象赋予多个新属性，比如使用Object.assign()或_.extend()。在这种情况下，你应该用两个对象的属性创建一个新的对象。所以，如果你想添加新的响应式属性，不要像这样：


```javascript
Object.assign(vm.userProfile, {
    age: 27,
    favoriteColor: 'Vue Green'
});
```


应该这样做：


```javascript
vm.userProfile = Object.assign({}, vm.userProfile, {
    age: 27,
    favoriteColor: 'Vue Green'
});
```


#### 如何解决非工程化项目，网速慢时初始化页面闪动问题？
使用v-cloak指令，v-cloak不需要表达式，它会在Vue实例结束编译时从绑定的HTML元素上移除，经常和CSS的display: none配合使用。


```html
<div id="app" v-cloak>
    {{ message }}
</div>
<script>
let app = new Vue({
    el:"#app",
    data:{
        message:"这是一段文本"
    }
})
</script>
```


这时虽然已经加了指令v-cloak，但其实并没有起到任何作用。当网速较慢、Vue.js文件还没加载完时，在页面上会显示{{message}}的字样，直到Vue创建实例、编译模版时，DOM才会被替换，所以这个过程屏幕是有闪动的。只要加一句CSS就可以解决这个问题了：


```css
[v-cloak]{
    display: none;
}
```


在一般情况下，v-cloak是一个解决初始化慢导致页面闪动的最佳实践，对于简单的项目很实用。


#### v-for产生的列表，如何实现active样式的切换？
通过设置当前currentIndex实现：


```html
<template>
	<div class="toggleClassWrap">
	 <ul>
		<li @click="currentIndex = index" v-bind:class="{clicked: index === currentIndex}" v-for="(item, index) in desc" :key="index">
			<a href="javascript:;">{{item.ctrlValue}}</a>
		</li>
	</ul>
	</div>
</template>
<script type="text/javascript">
	export default{
		data () {
			return {
				desc:[{
					ctrlValue:"test1"
				},{
					ctrlValue:"test2"
				},{
					ctrlValue:"test3"
				},{
					ctrlValue:"test4"
				}],
				currentIndex:0
			}
		}
	}
</script>
<style type="text/css" lang="less">
.toggleClassWrap{
	.clicked{
		color:red;
	}
}
</style>
```
