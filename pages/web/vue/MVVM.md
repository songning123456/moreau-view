#### MVC、MVP与MVVM模式
**MVC**


MVC是应用最广泛的软件架构之一，一般MVC分为：<span class='forest-green'>Model(模型)</span>、<span class='forest-green'>Controller(控制器)</span>、<span class='forest-green'>View(视图)</span>。


这主要是基于分层的目的，让彼此的职责分开。View一般通过Controller来和Model进行联系，Controller是Model和View的协调者，View和Model不直接联系，基本联系都是单向的。<br>
1. View传送指令到Controller；<br>
2. Controller完成业务逻辑后，要求Model改变状态；<br>
3. Model将新的数据发送到View，用户得到反馈。


![MVC](/images/Web/MVC.png)


**MVP**


MVP模式将Controller改名为Presenter，同时改变了通信方向。<br>
1. 各部分之间的通信，都是双向的；<br>
2. View与Model不发生联系，都通过Presenter传递；<br>
3. View非常薄，不部署任何业务逻辑，称为"被动视图"(Passive View)，即没有任何主动性，而Presenter非常厚，所有逻辑都部署在那里。


![MVP](/images/Web/MVP.png)


**MVVM**


MVVM是把MVC的Controller和MVP的Presenter改成了ViewModel。View的变化会自动更新到ViewModel，ViewModel的变化也会自动同步到View上显示。这种自动同步是因为ViewModel 中的属性实现了Observer，当属性变更时都能触发对应的操作。


![MVVM1](/images/Web/MVVM1.png)


![MVVM2](/images/Web/MVVM2.png)


#### MVVM模式的优点以及与MVC模式的区别？
| MVVM模式的优点 | 解释 | 
| :----- | :----- | 
|<div style='width: 130px'>低耦合</div>|视图(View)可以独立于Model变化和修改，一个ViewModel可以绑定到不同的"View"上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。|
|<div style='width: 130px'>可重用性</div>|你可以把一些视图逻辑放在一个ViewModel里面，让很多View重用这段视图逻辑。|
|<div style='width: 130px'>独立开发</div>|开发人员可以专注于业务逻辑和数据的开发(ViewModel)，设计人员可以专注于页面设计。|
|<div style='width: 130px'>可测试</div>|界面素来是比较难于测试的，而现在测试可以针对ViewModel来写。|


MVC和MVVM其实区别并不大，都是一种设计思想。


主要区别：<br>
1. MVC中Controller演变成MVVM中的ViewModel；<br>
2. MVVM通过数据来显示视图层而不是节点操作。


MVVM主要解决了：MVC中大量的DOM操作使页面渲染性能降低，加载速度变慢，影响用户体验。


#### 常见的实现数据绑定的做法有哪些？
**发布者-订阅者模式**


一般通过sub/pub的方式实现数据和视图的绑定监听，更新数据方式通常做法是vm.set('property', value)。这种方式现在毕竟太low了，我们更希望通过vm.property = value这种方式更新数据，同时自动更新视图，于是有了下面两种方式。


**脏值检查**


angular.js是通过脏值检测的方式比对数据是否有变更，来决定是否更新视图，最简单的方式就是通过setInterval()定时轮询检测数据变动，angular只有在指定的事件触发时进入脏值检测，大致如下：<br>
1. DOM事件，譬如用户输入文本，点击按钮等(ng-click)；<br>
2. XHR响应事件($http)；<br>
3. 浏览器Location变更事件($location)；<br>
4. Timer事件($timeout，$interval)；<br>
5. 执行$digest()或$apply()。


**数据劫持**


vue.js则是采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter。在数据变动时发布消息给订阅者，触发相应的监听回调。


#### Object.defineProperty()方法的作用是什么？
Object.defineProperty()方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象。


```javascript
// 语法
Object.defineProperty(obj, prop, descriptor);

// 参数说明
// obj：必需，目标对象；
// prop：必需，需定义或修改的属性的名字；
// descriptor：必需，目标属性所拥有的特性。

// 返回值：传入函数的对象。即第一个参数obj。
```


针对属性，我们可以给这个属性设置一些特性，比如是否只读不可以写；是否可以被for..in或Object.keys()遍历。给对象的属性添加特性描述，目前提供两种形式：<span class='forest-green'>数据描述</span>和<span class='forest-green'>存取器描述</span>。


**数据描述**


当修改或定义对象的某个属性的时候，给这个属性添加一些特性：


```javascript
let obj = {
    test:"hello"
};
//对象已有的属性添加特性描述
Object.defineProperty(obj,"test",{
    configurable: true | false,
    enumerable: true | false,
    value: 任意类型的值,
    writable: true | false
});
//对象新添加的属性的特性描述
Object.defineProperty(obj,"newKey",{
    configurable: true | false,
    enumerable: true | false,
    value: 任意类型的值,
    writable: true | false
});
```


设置的特性总结：


```html
value: 设置属性的值。
writable: 值是否可以重写(true | false)。
enumerable: 目标属性是否可以被枚举(true | false)。
configurable: 目标属性是否可以被删除或是否可以再次修改特性(true | false)。
```


注意：<br>
1. 除了可以给新定义的属性设置特性，也可以给已有的属性设置特性；<br>
2. 一旦使用Object.defineProperty给对象添加属性，那么如果不设置属性的特性，那么configurable、enumerable、writable这些值都为默认的false。


数据描述中的属性都是可选的，来看一下设置每一个属性详细解释。


<span class='forest-green'>value：属性对应的值，可以使任意类型的值，默认为undefined。</span>


```javascript
let obj = {};
//第一种情况：不设置value属性
Object.defineProperty(obj,"newKey",{

});
console.log( obj.newKey );  //undefined

//第二种情况：设置value属性
Object.defineProperty(obj,"newKey",{
    value:"hello"
});
console.log( obj.newKey );  //hello
```


<span class='forest-green'>writable：属性的值是否可以被重写。设置为true可以被重写；设置为false不能被重写(默认为false)。</span>


```javascript
let obj = {};
//第一种情况：writable设置为false，不能重写。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false
});
//更改newKey的值
obj.newKey = "change value";
console.log( obj.newKey );  //hello

//第二种情况：writable设置为true，可以重写
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:true
});
//更改newKey的值
obj.newKey = "change value";
console.log( obj.newKey );  //change value
```


<span class='forest-green'>enumerable：此属性是否可以被枚举(使用for...in或Object.keys())。设置为true可以被枚举；设置为false，不能被枚举(默认为false)。</span>


```javascript
let obj = {};
//第一种情况：enumerable设置为false，不能被枚举。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false
});

//枚举对象的属性
for(let attr in obj ){
    console.log(attr);  
}
//第二种情况：enumerable设置为true，可以被枚举。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:true
});

//枚举对象的属性
for(let attr in obj){
    console.log(attr);  //newKey
}
```


<span class='forest-green'>configurable：是否可以删除目标属性或是否可以再次修改属性的特性(writable、configurable、enumerable)。设置为true可以被删除或可以重新设置特性；设置为false不能被可以被删除或不可以重新设置特性(默认为false)。</span>


这个属性起到两个作用：<br>
1. 目标属性是否可以使用delete删除；<br>
2. 目标属性是否可以再次设置特性。


```javascript
//-----------------测试目标属性是否能被删除------------------------
let obj = {};
//第一种情况：configurable设置为false，不能被删除。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false,
    configurable:false
});
//删除属性
delete obj.newKey;
console.log( obj.newKey ); //hello

//第二种情况：configurable设置为true，可以被删除。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false,
    configurable:true
});
//删除属性
delete obj.newKey;
console.log( obj.newKey ); //undefined

//-----------------测试是否可以再次修改特性------------------------
let obj = {};
//第一种情况：configurable设置为false，不能再次修改特性。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false,
    configurable:false
});

//重新修改特性
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:true,
    enumerable:true,
    configurable:true
});
console.log( obj.newKey ); //报错：Uncaught TypeError: Cannot redefine property: newKey

//第二种情况：configurable设置为true，可以再次修改特性。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false,
    configurable:true
});

//重新修改特性
Object.defineProperty(obj,"newKey", {
    value:"hello",
    writable:true,
    enumerable:true,
    configurable:true
});
console.log( obj.newKey ); //hello
```


**存取器描述**


当使用存取器描述属性的特性的时候，允许设置以下特性属性：


```javascript
let obj = {};
Object.defineProperty(obj,"newKey",{
    get:function (){} | undefined,
    set:function (value){} | undefined,
    configurable: true | false,
    enumerable: true | false
});
```


注意：当使用了getter或setter方法，不允许使用writable和value这两个属性。


当设置或获取对象的某个属性的值的时候，可以提供getter/setter方法。<br>
1. getter是一种获得属性值的方法。<br>
2. setter是一种设置属性值的方法。<br>
在特性中使用get/set属性来定义对应的方法。


````javascript
let obj = {};
let initValue = 'hello';
Object.defineProperty(obj,"newKey",{
    get:function (){
        //当获取值的时候触发的函数
        return initValue;    
    },
    set:function (value){
        //当设置值的时候触发的函数,设置的新值通过参数value拿到
        initValue = value;
    }
});
//获取值
console.log( obj.newKey );  //hello
//设置值
obj.newKey = 'change value';
console.log( obj.newKey ); //change value
````


注意：<br>
1. get或set不是必须成对出现，任写其一就可以。如果不设置方法，则get和set的默认值为undefined。configurable和enumerable同上面的用法。<br>
2. 在ie8下只能在DOM对象上使用，尝试在原生的对象使用Object.defineProperty()会报错。



