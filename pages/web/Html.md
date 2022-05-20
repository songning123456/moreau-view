#### 说一下<label>标签的用法？
<label>标签主要是方便鼠标点击使用，扩大可点击的范围，增强用户操作体验。


#### 遍历A节点的父节点下的所有子节点？
```javascript
let b = document.getElementById("a").parentNode.children;
console.log(b);
```


#### 页面渲染html的过程？
1.浏览器解析html源码，然后创建一个 DOM树。并行请求css/image/js在DOM树中，每一个HTML标签都有一个对应的节点，并且每一个文本也都会有一个对应的文本节点。DOM树的根节点就是documentElement，对应的是html标签。


2.浏览器解析CSS代码，计算出最终的样式数据。构建CSSOM树。对CSS代码中非法的语法它会直接忽略掉，解析CSS的时候会按照如下顺序来定义优先级：浏览器默认设置 < 用户设置 < 外链样式 < 内联样式 < html中的style。


3.DOM Tree + CSSOM --> 渲染树(rendering tree)。渲染树和DOM树有点像，但是是有区别的。DOM树完全和html标签一一对应，但是渲染树会忽略掉不需要渲染的元素，比如head、display: none的元素等。而且一大段文本中的每一个行在渲染树中都是独立的一个节点。渲染树中的每一个节点都存储有对应的css属性。


4.一旦渲染树创建好了，浏览器就可以根据渲染树直接把页面绘制到屏幕上。


以上四个步骤并不是一次性顺序完成的。如果DOM或者CSSOM被修改，以上过程会被重复执行。实际上，CSS和JavaScript往往会多次修改DOM或者CSSOM。


#### 说一下CORS？
CORS是一种新标准，支持同源通信，也支持跨域通信。fetch是实现CORS通信的。


#### 如何中断ajax请求？
一种是设置超时时间让ajax自动断开，另一种是手动停止ajax请求，其核心是调用XML对象的abort()方法，ajax.abort()。


