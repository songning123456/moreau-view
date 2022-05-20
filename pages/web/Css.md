#### support，calc，media各自的含义及用法？
**@support**主要是用于检测浏览器是否支持CSS的某个属性，其实就是条件判断。如果支持某个属性，你可以写一套样式，如果不支持某个属性，你也可以提供另外一套样式作为替补；


**calc()**函数用于动态计算长度值。calc()函数支持"+", "-", "*", "/" 运算；


**@media**查询，你可以针对不同的媒体类型定义不同的样式。


#### css水平、垂直居中的写法，请至少写出4种？
| 水平居中 | 垂直居中 | 
| :----- | :----- | 
|<span style='font-weight: bold'>行内元素</span> text-align: center|line-height: 具体height值|
|<span style='font-weight: bold'>块级元素</span> margin: 0 auto|position: absolute; top: 50%; transform: translateY(-50%)|
|position: absolute; left: 50%; transform: translateX(-50%)|display: flex; align-items: center|
|display: flex; justify-content: center|display: table; display: table-cell; vertical-align: middle|


#### 1rem、1em、1vh、1px各自代表的含义？
| 名称 | 解释 | 
| :----- | :----- | 
|rem|rem是全部的长度都相对于根元素<html>元素。通常做法是给html元素设置一个字体大小，然后其他元素的长度单位就为rem。|
|em|子元素字体大小的em是相对于父元素字体大小；元素的width/height/padding/margin用em的话是相对于该元素的font-size。|
|vw/vh|全称是Viewport Width和Viewport Height视窗的宽度和高度。相当于屏幕宽度和高度的1%，不过，处理宽度的时候%单位更合适，处理高度的话vh单位更好。|
|px|px像素(Pixel)，相对长度单位。像素px是相对于显示器屏幕分辨率而言的。一般电脑的分辨率有{1920*1024}等不同的分辨率，1920*1024前者是屏幕宽度总共有1920个像素，后者则是高度为1024个像素。|


#### 画一条0.5px的直线？
```css
.line {
    height: 1px;
    transform: scale(0.5);
}
```


#### 说一下盒模型？
![boxModel](/images/Web/boxModel.jpg)


盒模型的组成，由里向外**content padding border margin**。在IE盒子模型中，width表示content+padding+border这三个部分的宽度；在标准的盒子模型中，width指content部分的宽度。


```css
.box-model {
    // 是W3C盒子模型(默认属性)
    box-sizing: content-box;
    // 是IE盒子模型
    box-sizing: border-box;
}
```


#### 画一个三角形？
```css
.triangle {
    width: 0;
    height: 0;
    border-width: 100px;
    border-style: solid;
    border-color: transparent #0099CC transparent transparent;
    transform: rotate(90deg); /*顺时针旋转90°*/
}
```


```html
<div class='triangle'></div>
```


#### 清除浮动的几种方式及原理？
清除浮动简单，但这题要引出的是BFC。BFC(块级格式化上下文)是一个独立的渲染区域，让处于BFC内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。


```
// 方式
1. ::after / <br> / clear: both；
2. 创建父级BFC(overflow:hidden)；
3. 父级设置高度。
```


```
// 触发条件
1. 根元素;
2. position: absolute/fixed;
3. display: inline-block / table;
4. float元素;
5. overflow !== visible;
```


```
// 规则
1. 属于同一个BFC的两个相邻Box垂直排列；
2. 属于同一个BFC的两个相邻Box的margin会发生重叠；
3. BFC的区域不会与float的元素区域重叠；
4. 计算BFC的高度时，浮动子元素也参与计算；
5. 文字层不会被浮动层覆盖，环绕于周围。
```
