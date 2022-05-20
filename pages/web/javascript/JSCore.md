#### 原始类型有哪几种？null是对象嘛？
在JS中，存在着6种原始值，分别是：<span class='forest-green'>boolean</span>、<span class='forest-green'>null</span>、<span class='forest-green'>undefined</span>、<span class='forest-green'>number</span>、<span class='forest-green'>string</span>、<span class='forest-green'>symbol</span>。


首先原始类型存储的都是值，是没有函数可以调用的，比如undefined.toString()。此时你肯定会有疑问，这不对呀，明明'1'.toString()是可以使用的。其实在这种情况下，'1'已经不是原始类型了，而是被强制转换成了String类型也就是对象类型，所以可以调用toString函数。


除了会在必要的情况下强转类型以外，原始类型还有一些坑：


其中JS的number类型是浮点类型的，在使用中会遇到某些Bug，比如0.1 + 0.2 !== 0.3，但是这一块的内容会在进阶部分讲到。string类型是不可变的，无论你在string类型上调用何种方法，都不会对值有改变。


另外对于null来说，很多人会认为他是个对象类型，其实这是错误的。虽然typeof null会输出object，但是这只是JS存在的一个悠久Bug。在JS的最初版本中使用的是32位系统，为了性能考虑使用低位存储变量的类型信息，000开头代表是对象，然而null表示为全零，所以将它错误的判断为object。虽然现在的内部类型判断代码已经改变了，但是对于这个Bug却是一直流传下来。


#### 对象类型和原始类型的不同之处？函数参数是对象会发生什么问题？
在JS中，除了原始类型那么其他的都是对象类型了。对象类型和原始类型不同的是，原始类型存储的是值，对象类型存储的是地址(指针)。当你创建了一个对象类型的时候，计算机会在内存中帮我们开辟一个空间来存放值，但是我们需要找到这个空间，这个空间会拥有一个地址(指针)。


```javascript
const a = [];
```


对于常量a来说，假设内存地址(指针)为#001，那么在地址#001的位置存放了值[]，常量a存放了地址(指针)#001，再看以下代码：


```javascript
const a = [];
const b = a;
b.push(1);
```


当我们将变量赋值给另外一个变量时，复制的是原本变量的地址(指针)，也就是说当前变量b存放的地址(指针)也是#001，当我们进行数据修改的时候，就会修改存放在地址(指针)#001上的值，也就导致了两个变量的值都发生了改变。


接下来我们来看函数参数是对象的情况：


```javascript
function test(person) {
    person.age = 26;
    person = {
        name: 'yyy',
        age: 30
    };
    return person;
}
const p1 = {
    name: 'yck',
    age: 25
};
const p2 = test(p1);
console.log(p1); // -> ?
console.log(p2); // -> ?
```


对于以上代码，你是否能正确的写出结果呢？接下来让我为了解析一番：<br>
1. 首先，函数传参是传递对象指针的副本；<br>
2. 到函数内部修改参数的属性这步，我相信大家都知道，当前p1的值也被修改了；<br>
3. 但是当我们重新为了person分配了一个对象时就出现了分歧。


所以最后person拥有了一个新的地址(指针)，也就和p1没有任何关系了，导致了最终两个变量的值是不相同的。


#### typeof是否能正确判断类型？instanceof能正确判断对象的原理是什么？
typeof对于原始类型来说，除了null都可以显示正确的类型。


```javascript
typeof 1; // 'number'
typeof '1'; // 'string'
typeof undefined; // 'undefined'
typeof true ;// 'boolean'
typeof Symbol(); // 'symbol'
```


typeof对于对象来说，除了函数都会显示object，所以说typeof并不能准确判断变量到底是什么类型。


```javascript
typeof []; // 'object'
typeof {}; // 'object'
typeof console.log; // 'function'
```


如果我们想判断一个对象的正确类型，这时候可以考虑使用instanceof，因为内部机制是通过原型链来判断的。


```javascript
const Person = function() {};
const p1 = new Person();
p1 instanceof Person; // true

var str = 'hello world';
str instanceof String; // false

var str1 = new String('hello world');
str1 instanceof String; // true
```


对于原始类型来说，你想直接通过instanceof来判断类型是不行的，当然我们还是有办法让instanceof判断原始类型的。


```javascript
class PrimitiveString {
    static [Symbol.hasInstance](x) {
        return typeof x === 'string';
    }
}
console.log('hello world' instanceof PrimitiveString); // true
```


你可能不知道Symbol.hasInstance是什么东西，其实就是一个能让我们自定义instanceof行为的东西，以上代码等同于typeof 'hello world' === 'string'，所以结果自然是true了。这其实也侧面反映了一个问题，instanceof也不是百分之百可信的。


#### 类型转换？
首先我们要知道，在JS中类型转换只有三种情况，分别是：<br>
1. 转换为布尔值；<br>
2. 转换为数字；<br>
3. 转换为字符串。


**转Boolean**


在条件判断时，除了undefined，null，false，NaN，''，0，-0，其他所有值都转为true，包括所有对象。


**对象转原始类型**


对象在转换类型的时候，会调用内置的[[ToPrimitive]]函数，对于该函数来说，算法逻辑一般来说如下：<br>
1. 如果已经是原始类型了，那就不需要转换了；<br>
2. 调用x.valueOf()，如果转换为基础类型，就返回转换的值；<br>
3. 调用x.toString()，如果转换为基础类型，就返回转换的值；<br>
4. 如果都没有返回原始类型，就会报错。


当然你也可以重写Symbol.toPrimitive，该方法在转原始类型时调用优先级最高。


```javascript
let a = {
    valueOf() {
        return 0;
    },
    toString() {
        return '1';
    },
    [Symbol.toPrimitive]() {
        return 2;
    }
};
1 + a; // => 3
```


**四则运算符**


加法运算符不同于其他几个运算符，它有以下几个特点：<br>
1. 运算中其中一方为字符串，那么就会把另一方也转换为字符串；<br>
2. 如果一方不是字符串或者数字，那么会将它转换为数字或者字符串。


```javascript
1 + '1'; // '11'
true + true; // 2
4 + [1,2,3]; // "41,2,3"
```


如果你对于答案有疑问的话，请看解析：<br>
1. 对于第一行代码来说，触发特点一，所以将数字1转换为字符串，得到结果'11'；<br>
2. 对于第二行代码来说，触发特点二，所以将true转为数字1；<br>
3. 对于第三行代码来说，触发特点二，所以将数组通过toString转为字符串1,2,3，得到结果41,2,3。


另外对于加法还需要注意这个表达式'a' + + 'b'。


```javascript
'a' + + 'b'; // -> "aNaN"
```


因为+ 'b'等于NaN，所以结果为"aNaN"，你可能也会在一些代码中看到过+ '1'的形式来快速获取number类型。那么对于除了加法的运算符来说，只要其中一方是数字，那么另一方就会被转为数字。


```javascript
4 * '3'; // 12
4 * []; // 0
4 * [1, 2]; // NaN
```


**比较运算符**


1. 如果是对象，就通过toPrimitive转换对象；
2. 如果是字符串，就通过unicode字符索引来比较。


```javascript
let a = {
    valueOf() {
        return 0
    },
    toString() {
        return '1'
    }
};
a > -1; // true
```


在以上代码中，因为a是对象，所以会通过valueOf转换为原始类型再比较值。


#### 如何正确判断this？箭头函数的this是什么？
this是很多人会混淆的概念，但是其实它一点都不难，只是网上很多文章把简单的东西说复杂了。在这一小节中，你一定会彻底明白this这个概念的。


我们先来看几个函数调用的场景：


```javascript
function foo() {
    console.log(this.a)
}
let a = 1;
foo();
const obj = {
    a: 2,
    foo: foo
};
obj.foo();
const c = new foo();
```


接下来我们一个个分析上面几个场景：<br>
1. 对于直接调用foo来说，不管foo函数被放在了什么地方，this一定是window；<br>
2. 对于obj.foo()来说，我们只需要记住，谁调用了函数，谁就是this，所以在这个场景下foo函数中的this就是obj对象；<br>
3. 对于new的方式来说，this被永远绑定在了c上面，不会被任何方式改变this。


说完了以上几种情况，其实很多代码中的this应该就没什么问题了，下面让我们看看箭头函数中的this：


```javascript
function a() {
    return () => {
        return () => {
            console.log(this);
        }
    }
}
console.log(a()()());
```


首先箭头函数其实是没有this的，箭头函数中的this只取决包裹箭头函数的第一个普通函数的this。在这个例子中，因为包裹箭头函数的第一个普通函数是a，所以此时的this是window。另外对箭头函数使用bind这类函数是无效的。最后种情况也就是bind这些改变上下文的API了，对于这些函数来说，this取决于第一个参数，如果第一个参数为空，那么就是window。


那么说到bind，不知道大家是否考虑过，如果对一个函数进行多次bind，那么上下文会是什么呢？


```javascript
let a = {};
let fn = function () { console.log(this) };
fn.bind().bind(a)(); // => ?
```


如果你认为输出结果是a，那么你就错了，其实我们可以把上述代码转换成另一种形式：


```javascript
// fn.bind().bind(a) 等于
let fn2 = function fn1() {
    return function() {
        return fn.apply()
    }.apply(a)
};
fn2()
```


可以从上述代码中发现，不管我们给函数bind几次，fn中的this永远由第一次bind决定，所以结果永远是window。


```javascript
let a = { name: 'vue' };
function foo() {
    console.log(this.name)
}
foo.bind(a)(); // => 'vue'
```


以上就是this的规则了，但是可能会发生多个规则同时出现的情况，这时候不同的规则之间会根据优先级最高的来决定this最终指向哪里。


首先，new的方式优先级最高，接下来是bind这些函数，然后是obj.foo()这种调用方式，最后是foo这种调用方式，同时，箭头函数的this一旦被绑定，就不会再被任何方式所改变。


#### ==和===有什么区别？
对于==来说，如果对比双方的类型不一样的话，就会进行类型转换，这也就用到了第四题：类型转换的问题。假如我们需要对比x和y是否相同，就会进行如下判断流程：


1. 首先会判断两者类型是否相同。相同的话就是比大小了；<br>
2. 类型不相同的话，那么就会进行类型转换；<br>
3. 会先判断是否在对比null和undefined，是的话就会返回true；<br>
4. 判断两者类型是否为string和number，是的话就会将字符串转换为number；


```
1 == '1'
      ↓
1 ==  1
```


5.判断其中一方是否为boolean，是的话就会把boolean转为number再进行判断；


```
'1' == true
        ↓
'1' ==  1
        ↓
 1  ==  1
```


6.判断其中一方是否为object且另一方为string、number或者symbol，是的话就会把object转为原始类型再进行判断。


```
'1' == { name: 'vue' }
        ↓
'1' == '[object Object]'
```


思考题：看完了上面的步骤，对于 [] == ![] 你是否能正确写出答案呢？


```javascript
[] == ![];
// true
```


#### 什么是闭包？
闭包的定义其实很简单：函数A内部有一个函数B，函数B可以访问到函数A中的变量，那么函数B就是闭包。


```javascript
function A() {
    let a = 1;
    window.B = function () {
        console.log(a);
    }
}
A();
B(); // 1
```


很多人对于闭包的解释可能是函数嵌套了函数，然后返回一个函数。其实这个解释是不完整的，就比如我上面这个例子就可以反驳这个观点。在JS中，闭包存在的意义就是让我们可以间接访问函数内部的变量。


经典面试题，循环中使用闭包解决var定义函数的问题：


```javascript
for (var i = 1; i <= 5; i++) {
    setTimeout(function timer() {
        console.log(i);
    }, i * 1000)
}
```


首先因为setTimeout是个异步函数，所以会先把循环全部执行完毕，这时候i就是6了，所以会输出一堆6。解决办法有三种，第一种是使用闭包的方式。


```javascript
for (var i = 1; i <= 5; i++) {
    (function(j) {
        setTimeout(function timer() {
            console.log(j)
        }, j * 1000)
    })(i)
}
```


在上述代码中，我们首先使用了立即执行函数将i传入函数内部，这个时候值就被固定在了参数j上面不会改变，当下次执行timer这个闭包的时候，就可以使用外部函数的变量j，从而达到目的。


第二种就是使用setTimeout的第三个参数，这个参数会被当成timer函数的参数传入。


```javascript
for (var i = 1; i <= 5; i++) {
    setTimeout(
        function timer(j) {
            console.log(j)
        },
        i * 1000,
        i
    )
}
```


第三种就是使用let定义i了来解决问题了，这个也是最为推荐的方式。


```javascript
for (let i = 1; i <= 5; i++) {
    setTimeout(function timer() {
        console.log(i)
    }, i * 1000)
}
```


#### 什么是浅拷贝？如何实现浅拷贝？什么是深拷贝？如何实现深拷贝？
在上面，我们了解了对象类型在赋值的过程中其实是复制了地址，从而会导致改变了一方其他也都被改变的情况。通常在开发中我们不希望出现这样的问题，我们可以使用浅拷贝来解决这个情况。


```javascript
let a = {
    age: 1
};
let b = a;
a.age = 2;
console.log(b.age); // 2
```


**浅拷贝**


首先可以通过Object.assign来解决这个问题，很多人认为这个函数是用来深拷贝的。其实并不是，Object.assign只会拷贝所有的属性值到新的对象中，如果属性值是对象的话，拷贝的是地址，所以并不是深拷贝。


```javascript
let a = {
    age: 1
};
let b = Object.assign({}, a);
a.age = 2;
console.log(b.age); // 1
```


另外我们还可以通过展开运算符...来实现浅拷贝。


```javascript
let a = {
    age: 1
};
let b = { ...a };
a.age = 2;
console.log(b.age); // 1
```


通常浅拷贝就能解决大部分问题了，但是当我们遇到如下情况就可能需要使用到深拷贝了。


```javascript
let a = {
    age: 1,
    jobs: {
        first: 'FE'
    }
};
let b = { ...a };
a.jobs.first = 'native';
console.log(b.jobs.first); // native
```


浅拷贝只解决了第一层的问题，如果接下去的值中还有对象的话，那么就又回到最开始的话题了，两者享有相同的地址。要解决这个问题，我们就得使用深拷贝了。


**深拷贝**


这个问题通常可以通过JSON.parse(JSON.stringify(object))来解决。


```javascript
let a = {
    age: 1,
    jobs: {
        first: 'FE'
    }
};
let b = JSON.parse(JSON.stringify(a));
a.jobs.first = 'native';
console.log(b.jobs.first); // FE
```


但是该方法也是有局限性的：<br>
1. 会忽略undefined；<br>
2. 会忽略symbol；<br>
3. 不能序列化函数；<br>
4. 不能解决循环引用的对象。


```javascript
let obj = {
    a: 1,
    b: {
        c: 2,
        d: 3,
    },
};
obj.c = obj.b;
obj.e = obj.a;
obj.b.c = obj.c;
obj.b.d = obj.b;
obj.b.e = obj.b.c;
let newObj = JSON.parse(JSON.stringify(obj));
console.log(newObj);
```


如果你有这么一个循环引用对象，你会发现并不能通过该方法实现深拷贝。在遇到函数、undefined或者symbol的时候，该对象也不能正常的序列化。


```javascript
let a = {
    age: undefined,
    sex: Symbol('male'),
    jobs: function() {},
    name: 'vue'
};
let b = JSON.parse(JSON.stringify(a));
console.log(b); // {name: "vue"}
```


你会发现在上述情况中，该方法会忽略掉函数和undefined。但是在通常情况下，复杂数据都是可以序列化的，所以这个函数可以解决大部分问题。如果你所需拷贝的对象含有内置类型并且不包含函数，可以使用MessageChannel。


```javascript
function structuralClone(obj) {
    return new Promise(resolve => {
        const { port1, port2 } = new MessageChannel();
        port2.onmessage = ev => resolve(ev.data);
        port1.postMessage(obj)
    })
}

const obj = {
  a: 1,
  b: {
    c: 2
  }
};

obj.b.d = obj.b;

// 注意该方法是异步的
// 可以处理 undefined 和循环引用对象
const test = async () => {
    const clone = await structuralClone(obj);
    console.log(clone);
};
test()
```


当然你可能想自己来实现一个深拷贝，但是其实实现一个深拷贝是很困难的，需要我们考虑好多种边界情况，比如原型链如何处理、DOM 如何处理等等，所以这里我们实现的深拷贝只是简易版，并且我其实更推荐使用lodash的深拷贝函数。


```javascript
function deepClone(obj) {
    function isObject(o) {
        return (typeof o === 'object' || typeof o === 'function') && o !== null
    }
    
    if (!isObject(obj)) {
        throw new Error('非对象')
    }
    
    let isArray = Array.isArray(obj);
    let newObj = isArray ? [...obj] : { ...obj };
    Reflect.ownKeys(newObj).forEach(key => {
    newObj[key] = isObject(obj[key]) ? deepClone(obj[key]) : obj[key]
    });
    
    return newObj
}

let obj = {
    a: [1, 2, 3],
    b: {
        c: 2,
        d: 3
    }
};
let newObj = deepClone(obj);
newObj.b.c = 1;
console.log(obj.b.c); // 2
```


#### 如何理解原型？如何理解原型链？
当我们创建一个对象时let obj = { age: 25 }，我们可以发现能使用很多种函数，但是我们明明没有定义过它们，对于这种情况你是否有过疑惑？


当我们在浏览器中打印obj时你会发现，在obj上居然还有一个__proto__属性，那么看来之前的疑问就和这个属性有关系了。其实每个JS对象都有__proto__属性，这个属性指向了原型。这个属性在现在来说已经不推荐直接去使用它了，这只是浏览器在早期为了让我们访问到内部属性[[prototype]]来实现的一个东西。


讲到这里好像还是没有弄明白什么是原型，接下来让我们再看看__proto__里面有什么吧。


看到这里你应该明白了，原型也是一个对象，并且这个对象中包含了很多函数，所以我们可以得出一个结论：对于obj来说，可以通过__proto__找到一个原型对象，在该对象中定义了很多函数让我们来使用。


在上面的图中我们还可以发现一个constructor属性，也就是构造函数。打开constructor属性我们又可以发现其中还有一个prototype属性，并且这个属性对应的值和先前我们在__proto__中看到的一模一样。所以我们又可以得出一个结论：原型的constructor属性指向构造函数，构造函数又通过prototype属性指回原型，但是并不是所有函数都具有这个属性，Function.prototype.bind()就没有这个属性。


其实原型就是那么简单，接下来我们再来看一张图，相信这张图能让你彻底明白原型和原型链。看完这张图，我再来解释下什么是原型链吧。其实原型链就是多个对象通过__proto__的方式连接了起来。为什么obj可以访问到valueOf函数，就是因为obj通过原型链找到了valueOf函数。


对于此问题知识点，总结起来就是以下几点：<br>
1. Object是所有对象的爸爸，所有对象都可以通过__proto__找到它；<br>
2. Function是所有函数的爸爸，所有函数都可以通过__proto__找到它；<br>
3. 函数的prototype是一个对象；<br>
4. 对象的__proto__属性指向原型，__proto__将对象和原型连接起来组成了原型链。


#### 并发与并行的区别？
异步和这小节的知识点其实并不是一个概念，但是这两个名词确实是很多人都常会混淆的知识点。其实混淆的原因可能只是两个名词在中文上的相似，在英文上来说完全是不同的单词。


| 名称 | 概念 | 解释 | 
| :----- | :----- | 
|<div style='width: 60px'>并发</div>|<div style='width: 70px'>宏观概念</div>|我分别有任务A和任务B，在一段时间内通过任务间的切换完成了这两个任务，这种情况就可以称之为并发。|
|<div style='width: 60px'>并行</div>|<div style='width: 70px'>微观概念</div>|假设CPU中存在两个核心，那么我就可以同时完成任务A、B。同时完成多个任务的情况就可以称之为并行。|


#### 什么是回调函数？回调函数有什么缺点？如何解决回调地狱问题？
回调函数应该是大家经常使用到的，以下代码就是一个回调函数的例子：


```javascript
ajax(url, () => {
    // 处理逻辑
});
```


但是回调函数有一个致命的弱点，就是容易写出回调地狱(Callback hell)。假设多个请求存在依赖性，你可能就会写出如下代码：


```javascript
ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})
```


以上代码看起来不利于阅读和维护。当然，你可能会想说解决这个问题还不简单，把函数分开来写不就得了。


```javascript
function firstAjax() {
    ajax(url1, () => {
    // 处理逻辑
        secondAjax()
    })
}
function secondAjax() {
    ajax(url2, () => {
    // 处理逻辑
    })
}
ajax(url, () => {
  // 处理逻辑
    firstAjax()
})
```


以上的代码虽然看上去利于阅读了，但是还是没有解决根本问题。


回调地狱的根本问题就是：
1. 嵌套函数存在耦合性，一旦有所改动，就会牵一发而动全身；
2. 嵌套函数一多，就很难处理错误。


当然，回调函数还存在着别的几个缺点，比如不能使用try catch捕获错误，不能直接return。


#### 你理解的Generator是什么？
Generator算是ES6中难理解的概念之一了，Generator最大的特点就是可以控制函数的执行。在这一小节中我们不会去讲什么是Generator，而是把重点放在Generator的一些容易困惑的地方。


```javascript
function *foo(x) {
    let y = 2 * (yield (x + 1));
    let z = yield (y / 3);
    return (x + y + z)
}
let it = foo(5);
console.log(it.next());   // => {value: 6, done: false}
console.log(it.next(12)); // => {value: 8, done: false}
console.log(it.next(13)); // => {value: 42, done: true}
```


你也许会疑惑为什么会产生与你预想不同的值，接下来就让我为你逐行代码分析原因：<br>
1. 首先Generator函数调用和普通函数不同，它会返回一个迭代器；<br>
2. 当执行第一次next时，传参会被忽略，并且函数暂停在yield (x + 1)处，所以返回5 + 1 = 6；<br>
3. 当执行第二次next时，传入的参数等于上一个yield的返回值，如果你不传参，yield永远返回undefined。此时let y = 2 \* 12，所以第二个yield等于2 * 12 / 3 = 8；<br>
4. 当执行第三次next时，传入的参数会传递给z，所以z = 13, x = 5, y = 24，相加等于42。


Generator函数一般见到的不多，其实也于他有点绕有关系，并且一般会配合co库去使用。当然，我们可以通过Generator函数解决回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：


```javascript
function *fetch() {
    yield ajax(url, () => {});
    yield ajax(url1, () => {});
    yield ajax(url2, () => {});
}
let it = fetch();
let result1 = it.next();
let result2 = it.next();
let result3 = it.next();
```


#### Promise的特点是什么，分别有什么优缺点？什么是Promise链？Promise构造函数执行和then函数执行有什么区别？
Promise翻译过来就是承诺的意思，这个承诺会在未来有一个确切的答复，并且该承诺有三种状态，分别是：<br>
1. 等待中(pending)<br>
2. 完成了(resolved)<br>
3. 拒绝了(rejected)


这个承诺一旦从等待状态变成为其他状态就永远不能更改状态了，也就是说一旦状态变为resolved后，就不能再次改变。


```javascript
new Promise((resolve, reject) => {
    resolve('success');
    // 无效
    reject('reject');
})
```


当我们在构造Promise的时候，构造函数内部的代码是立即执行的。


```javascript
new Promise((resolve, reject) => {
    console.log('new Promise');
    resolve('success');
});
console.log('finifsh');
// new Promise -> finifsh
```


Promise实现了链式调用，也就是说每次调用then之后返回的都是一个Promise，并且是一个全新的Promise，原因也是因为状态不可变。如果你在then中使用了return，那么return的值会被Promise.resolve()包装。


```javascript
Promise.resolve(1)
    .then(res => {
        console.log(res); // => 1
        return 2 // 包装成 Promise.resolve(2)
    })
    .then(res => {
        console.log(res) // => 2
    })
```


当然了，Promise也很好地解决了回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：


```javascript
ajax(url)
    .then(res => {
        console.log(res);
        return ajax(url1);
    }).then(res => {
        console.log(res);
        return ajax(url2);
    }).then(res => console.log(res));
```


前面都是在讲述Promise的一些优点和特点，其实它也是存在一些缺点的，比如无法取消Promise，错误需要通过回调函数捕获。


#### async及await的特点，它们的优点和缺点分别是什么？await原理是什么？
一个函数如果加上async，那么该函数就会返回一个Promise。
```javascript
async function test() {
    return "1";
}
console.log(test()); // -> Promise {<resolved>: "1"}
```


async就是将函数返回值使用Promise.resolve()包裹了下，和then中处理返回值一样，并且await只能配套async使用。


```javascript
async function test() {
    let value = await sleep();
}
```


async和await可以说是异步终极解决方案了，相比直接使用Promise来说，优势在于处理then的调用链，能够更清晰准确的写出代码，毕竟写一大堆then也很恶心，并且也能优雅地解决回调地狱问题。当然也存在一些缺点，因为await将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了await会导致性能上的降低。


```javascript
async function test() {
    // 以下代码没有依赖性的话，完全可以使用 Promise.all 的方式
    // 如果有依赖性的话，其实就是解决回调地狱的例子了
    await fetch(url);
    await fetch(url1);
    await fetch(url2);
}
```


下面来看一个使用await的例子：


```javascript
let a = 0;
let b = async () => {
    a = a + await 10;
    console.log('2', a); // -> '2' 10
};
b();
a++;
console.log('1', a); // -> '1' 1
```


对于以上代码你可能会有疑惑，让我来解释下原因：<br>
1. 首先函数b先执行，在执行到await 10之前变量a还是0，因为await内部实现了generator，generator会保留堆栈中东西，所以这时候a = 0被保存了下来；<br>
2. 因为await是异步操作，后来的表达式不返回Promise的话，就会包装成Promise.resolve(返回值)，然后会去执行函数外的同步代码；<br>
3. 同步代码执行完毕后开始执行异步代码，将保存下来的值拿出来使用，这时候a = 0 + 10。


#### setTimeout、setInterval、requestAnimationFrame各有什么特点？
异步编程当然少不了定时器了，常见的定时器函数有setTimeout、setInterval、requestAnimationFrame。我们先来讲讲最常用的setTimeout，很多人认为setTimeout是延时多久，那就应该是多久后执行。


其实这个观点是错误的，因为JS是单线程执行的，如果前面的代码影响了性能，就会导致setTimeout不会按期执行。当然了，我们可以通过代码去修正setTimeout，从而使定时器相对准确。


```javascript
let period = 60 * 1000 * 60 * 2;
let startTime = new Date().getTime();
let count = 0;
let end = new Date().getTime() + period;
let interval = 1000;
let currentInterval = interval;

function loop() {
    count++;
    // 代码执行所消耗的时间
    let offset = new Date().getTime() - (startTime + count * interval);
    let diff = end - new Date().getTime();
    let h = Math.floor(diff / (60 * 1000 * 60));
    let hdiff = diff % (60 * 1000 * 60);
    let m = Math.floor(hdiff / (60 * 1000));
    let mdiff = hdiff % (60 * 1000);
    let s = mdiff / (1000);
    let sCeil = Math.ceil(s);
    let sFloor = Math.floor(s);
    // 得到下一次循环所消耗的时间
    currentInterval = interval - offset;
    console.log('时：'+h, '分：'+m, '毫秒：'+s, '秒向上取整：'+sCeil, '代码执行时间：'+offset, '下次循环间隔'+currentInterval);
    setTimeout(loop, currentInterval);
}
setTimeout(loop, currentInterval);
```


接下来我们来看setInterval，其实这个函数作用和setTimeout基本一致，只是该函数是每隔一段时间执行一次回调函数。通常来说不建议使用setInterval。第一，它和setTimeout一样，不能保证在预期的时间执行任务。第二，它存在执行累积的问题，请看以下伪代码：


```javascript
function demo() {
    setInterval(function(){
        console.log(2)
    },1000);
    sleep(2000)
}
demo()
```


以上代码在浏览器环境中，如果定时器执行过程中出现了耗时操作，多个回调函数会在耗时操作结束以后同时执行，这样可能就会带来性能上的问题。如果你有循环定时器的需求，其实完全可以通过requestAnimationFrame来实现。


```javascript
function setInterval(callback, interval) {
    let timer;
    const now = Date.now;
    let startTime = now();
    let endTime = startTime;
    const loop = () => {
        timer = window.requestAnimationFrame(loop);
        endTime = now();
        if (endTime - startTime >= interval) {
            startTime = endTime = now();
            callback(timer);
        }
    };
    timer = window.requestAnimationFrame(loop);
    return timer;
}
let a = 0;
setInterval(timer => {
    console.log(1);
    a++;
    if (a === 3) {
        cancelAnimationFrame(timer);
    }
}, 1000)
```


首先requestAnimationFrame自带函数节流功能，基本可以保证在16.6毫秒内只执行一次(不掉帧的情况下)，并且该函数的延时效果是精确的，没有其他定时器时间不准的问题，当然你也可以通过该函数来实现setTimeout。


#### 进程与线程区别？JS单线程带来的好处？
相信大家经常会听到JS是单线程执行的，但是你是否疑惑过什么是线程？


讲到线程，那么肯定也得说一下进程。本质上来说，两个名词都是CPU工作时间片的一个描述。


进程描述了CPU在运行指令及加载和保存上下文所需的时间，放在应用上来说就代表了一个程序。线程是进程中的更小单位，描述了执行一段指令所需的时间。


把这些概念拿到浏览器中来说，当你打开一个Tab页时，其实就是创建了一个进程，一个进程中可以有多个线程，比如渲染线程、JS引擎线程、HTTP请求线程等等。当你发起一个请求时，其实就是创建了一个线程，当请求结束后，该线程可能就会被销毁。


上文说到了JS引擎线程和渲染线程，大家应该都知道，在JS运行的时候可能会阻止UI渲染，这说明了两个线程是互斥的。这其中的原因是因为JS可以修改DOM，如果在JS执行的时候UI线程还在工作，就可能导致不能安全的渲染UI。这其实也是一个单线程的好处，得益于JS是单线程运行的，可以达到节省内存，节约上下文切换时间，没有锁的问题的好处。当然前面两点在服务端中更容易体现，对于锁的问题，形象的来说就是当我读取一个数字15的时候，同时有两个操作对数字进行了加减，这时候结果就出现了错误。解决这个问题也不难，只需要在读取的时候加锁，直到读取完毕之前都不能进行写入操作。


#### 什么是执行栈？
可以把执行栈认为是一个存储函数调用的栈结构，遵循先进后出的原则。


当开始执行JS代码时，首先会执行一个main函数，然后执行我们的代码。根据先进后出的原则，后执行的函数会先弹出栈，在图中我们也可以发现，foo函数后执行，当执行完毕后就从栈中弹出了。平时在开发中，大家也可以在报错中找到执行栈的痕迹。


```javascript
function foo() {
    throw new Error('error');
}
function bar() {
    foo();
}
bar();
```


大家可以在上图清晰的看到报错在foo函数，foo函数又是在bar函数中调用的。当我们使用递归的时候，因为栈可存放的函数是有限制的，一旦存放了过多的函数且没有得到释放的话，就会出现爆栈的问题。


```javascript
function bar() {
    bar();
}
bar();
```


#### 异步代码执行顺序？解释一下什么是Event Loop？
上一小节我们讲到了什么是执行栈，大家也知道了当我们执行JS代码的时候其实就是往执行栈中放入函数，那么遇到异步代码的时候该怎么办？其实当遇到异步的代码时，会被挂起并在需要执行的时候加入到Task(有多种Task)队列中。一旦执行栈为空，Event Loop就会从Task队列中拿出需要执行的代码并放入执行栈中执行，所以本质上来说JS中的异步还是同步行为。


不同的任务源会被分配到不同的Task队列中，任务源可以分为微任务(microtask)和宏任务(macrotask)。在ES6规范中，microtask称为jobs，macrotask称为task。下面来看以下代码的执行顺序：


```javascript
console.log('script start');

async function async1() {
    await async2();
    console.log('async1 end');
}
async function async2() {
    console.log('async2 end');
}

async1();

setTimeout(function() {
    console.log('setTimeout');
}, 0);

new Promise(resolve => {
    console.log('Promise');
    resolve();
}).then(function() {
    console.log('promise1')
  }).then(function() {
    console.log('promise2')
  });

console.log('script end');
// script start => async2 end => Promise => script end => promise1 => promise2 => async1 end => setTimeout
```


首先先来解释下上述代码的async和await的执行顺序。当我们调用async1函数时，会马上输出async2 end，并且函数返回一个Promise，接下来在遇到await的时候会就让出线程开始执行async1外的代码，所以我们完全可以把await看成是让出线程的标志。


然后当同步代码全部执行完毕以后，就会去执行所有的异步代码，那么又会回到await的位置执行返回的Promise的resolve函数，这又会把resolve丢到微任务队列中，接下来去执行then中的回调，当两个then中的回调全部执行完毕以后，又会回到await的位置处理返回值，这时候你可以看成是Promise.resolve(返回值).then()，然后await后的代码全部被包裹进了then的回调中，所以console.log('async1 end')会优先执行于setTimeout。


如果你觉得上面这段解释还是有点绕，那么我把async的这两个函数改造成你一定能理解的代码：


```javascript
new Promise((resolve, reject) => {
    console.log('async2 end');
    // Promise.resolve() 将代码插入微任务队列尾部
    // resolve 再次插入微任务队列尾部
    resolve(Promise.resolve());
}).then(() => {
    console.log('async1 end');
})
```


也就是说，如果await后面跟着Promise的话，async1 end需要等待三个tick才能执行到。那么其实这个性能相对来说还是略慢的，所以V8团队借鉴了Node 8中的一个Bug，在引擎底层将三次tick减少到了二次tick。但是这种做法其实是违法了规范的，当然规范也是可以更改的，这是V8团队的一个PR，目前已被同意这种做法。


所以 Event Loop 执行顺序如下所示：<br>
1. 首先执行同步代码，这属于宏任务；<br>
2. 当执行完所有同步代码后，执行栈为空，查询是否有异步代码需要执行；<br>
3. 执行所有微任务；<br>
4. 当执行完所有微任务后，如有必要会渲染页面；<br>
5. 然后开始下一轮 Event Loop，执行宏任务中的异步代码，也就是setTimeout中的回调函数。


所以以上代码虽然setTimeout写在Promise之前，但是因为Promise属于微任务而setTimeout属于宏任务，所以会有以上的打印。


微任务包括process.nextTick，promise，MutationObserver。宏任务包括script，setTimeout，setInterval，setImmediate，I/O，UI rendering。这里很多人会有个误区，认为微任务快于宏任务，其实是错误的。因为宏任务中包括了script，浏览器会先执行一个宏任务，接下来有异步代码的话才会先执行微任务。


#### Node中的Event Loop和浏览器中的有什么区别？process.nextTick执行顺序？
Node中的Event Loop和浏览器中的是完全不相同的东西。


Node的Event Loop分为6个阶段，它们会按照顺序反复运行。每当进入某一个阶段的时候，都会从对应的回调队列中取出函数去执行。当队列为空或者执行的回调函数数量到达系统设定的阈值，就会进入下一阶段。


**timer**


timers阶段会执行setTimeout和setInterval回调，并且是由poll阶段控制的。同样，在Node中定时器指定的时间也不是准确时间，只能是尽快执行。


**I/O**


I/O阶段会处理一些上一轮循环中的少数未执行的I/O回调。


**idle, prepare**


idle, prepare阶段内部实现，这里就忽略不讲了。


**poll**


poll是一个至关重要的阶段，这一阶段中，系统会做两件事情：<br>
1. 回到timer阶段执行回调；<br>
2. 执行I/O回调。


并且在进入该阶段时如果没有设定了 timer 的话，会发生以下两件事情：<br>
1. 如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者达到系统限制；<br>
2. 如果 poll 队列为空时，会有两件事发生：<br>
    1. 如果有setImmediate回调需要执行，poll阶段会停止并且进入到check阶段执行回调；<br>
    2. 如果没有setImmediate回调需要执行，会等待回调被加入到队列中并立即执行回调，这里同样会有个超时时间设置防止一直等待下去。<br>


当然设定了timer的话且poll队列为空，则会判断是否有timer超时，如果有的话会回到timer阶段执行回调。


**check**


check阶段执行setImmediate。


**close callbacks**


close callbacks阶段执行close事件。在以上的内容中，我们了解了Node中的Event Loop的执行顺序，接下来我们将会通过代码的方式来深入理解这块内容。首先在有些情况下，定时器的执行顺序其实是随机的。


```javascript
setTimeout(() => {
    console.log('setTimeout');
}, 0);
setImmediate(() => {
    console.log('setImmediate');
})
```


对于以上代码来说，setTimeout可能执行在前，也可能执行在后：<br>
1. 首先setTimeout(fn, 0) === setTimeout(fn, 1)，这是由源码决定的；<br>
2. 进入事件循环也是需要成本的，如果在准备时候花费了大于1ms的时间，那么在timer阶段就会直接执行setTimeout回调；<br>
3. 那么如果准备时间花费小于 1ms，那么就是setImmediate回调先执行了。


当然在某些情况下，他们的执行顺序一定是固定的，比如以下代码：


```javascript
const fs = require('fs');

fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('timeout');
    }, 0);
    setImmediate(() => {
        console.log('immediate')
    });
})
```


在上述代码中，setImmediate永远先执行。因为两个代码写在IO回调中，IO回调是在poll阶段执行，当回调执行完毕后队列为空，发现存在setImmediate回调，所以就直接跳转到 check 阶段去执行回调了。


上面介绍的都是macrotask的执行情况，对于microtask来说，它会在以上每个阶段完成前清空microtask队列，下图中的Tick就代表了microtask。


```javascript
setTimeout(() => {
    console.log('timer21');
}, 0);

Promise.resolve().then(function() {
    console.log('promise1');
});
```


对于以上代码来说，其实和浏览器中的输出是一样的，microtask永远执行在macrotask前面。


最后我们来讲讲Node中的process.nextTick，这个函数其实是独立于Event Loop之外的，它有一个自己的队列，当每个阶段完成后，如果存在nextTick队列，就会清空队列中的所有回调函数，并且优先于其他microtask执行。


```javascript
setTimeout(() => {
    console.log('timer1');
    Promise.resolve().then(function() {
        console.log('promise1')
    })
}, 0);

process.nextTick(() => {
    console.log('nextTick');
        process.nextTick(() => {
            console.log('nextTick');
                process.nextTick(() => {
                    console.log('nextTick');
                    process.nextTick(() => {
                        console.log('nextTick')
                    })
            })
    })
})
```


对于以上代码，大家可以发现无论如何，永远都是先把nextTick全部打印出来。


#### new的原理是什么？通过new的方式创建对象和通过字面量创建有什么区别？
在调用new的过程中会发生以上四件事情：<br>
1. 新生成了一个对象；<br>
2. 链接到原型；<br>
3. 绑定this；<br>
4. 返回新对象。


根据以上几个过程，我们也可以试着来自己实现一个new。


```javascript
function create() {
    let obj = {};
    let Con = [].shift.call(arguments);
    obj.__proto__ = Con.prototype;
    let result = Con.apply(obj, arguments);
    return result instanceof Object ? result : obj;
}
```


以下是对实现的分析：<br>
1. 创建一个空对象；<br>
2. 获取构造函数；<br>
3. 设置空对象的原型；<br>
4. 绑定this并执行构造函数；<br>
5. 确保返回值为对象。


对于对象来说，其实都是通过new产生的，无论是function Foo()还是let a = { b : 1 }。


对于创建一个对象来说，更推荐使用字面量的方式创建对象(无论性能上还是可读性)。因为你使用new Object()的方式创建对象需要通过作用域链一层层找到Object，但是你使用字面量的方式就没这个问题。


```javascript
function Foo() {}
// function 就是个语法糖
// 内部等同于 new Function()
let a = { b: 1 }
// 这个字面量内部也是使用了 new Object()
```


#### instanceof的原理是什么？
instanceof可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的prototype。我们也可以试着实现一下instanceof：


```javascript
function myInstanceof(left, right) {
    let prototype = right.prototype;
    left = left.__proto__;
    while (true) {
    if (left === null || left === undefined)
      return false;
    if (prototype === left)
      return true;
    left = left.__proto__
    }
}
```


以下是对实现的分析：<br>
1. 首先获取类型的原型；<br>
2. 然后获得对象的原型；<br>
3. 然后一直循环判断对象的原型是否等于类型的原型，直到对象原型为null，因为原型链最终为null。


#### 为什么0.1 + 0.2 != 0.3？如何解决这个问题？
先说原因，因为JS采用IEEE 754双精度版本(64位)，并且只要采用IEEE 754的语言都有该问题。我们都知道计算机是通过二进制来存储东西的，那么0.1在二进制中会表示为：


```
// (0011) 表示循环
0.1 = 2^-4 * 1.10011(0011)
```


我们可以发现，0.1在二进制中是无限循环的一些数字，其实不只是0.1，其实很多十进制小数用二进制表示都是无限循环的。这样其实没什么问题，但是JS采用的浮点数标准却会裁剪掉我们的数字。


IEEE 754双精度版本(64位)将64位分为了三段：<br>
1. 第一位用来表示符号；<br>
2. 接下去的 11 位用来表示指数；<br>
3. 其他的位数用来表示有效位，也就是用二进制表示0.1中的10011(0011)。


那么这些循环的数字被裁剪了，就会出现精度丢失的问题，也就造成了0.1不再是0.1了，而是变成了0.100000000000000002。


```javascript
0.100000000000000002 === 0.1 // true
```


那么同样的，0.2在二进制也是无限循环的，被裁剪后也失去了精度变成了0.200000000000000002。


```javascript
0.200000000000000002 === 0.2; // true
```


所以这两者相加不等于0.3而是0.300000000000000004。


```javascript
0.1 + 0.2 === 0.30000000000000004 // true
```


那么可能你又会有一个疑问，既然0.1不是0.1，那为什么console.log(0.1)却是正确的呢？


因为在输入内容的时候，二进制被转换为了十进制，十进制又被转换为了字符串，在这个转换的过程中发生了取近似值的过程，所以打印出来的其实是一个近似值，你也可以通过以下代码来验证：


```javascript
console.log(0.100000000000000002) // 0.1
```


那么说完了为什么，最后来说说怎么解决这个问题吧。其实解决的办法有很多，这里我们选用原生提供的方式来最简单的解决问题：


```javascript
parseFloat((0.1 + 0.2).toFixed(10)) === 0.3; // true
```