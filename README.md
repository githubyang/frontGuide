# JavaScript规范 1.0
注:前端开发指南里面的javascript部分，本指南非官方文档，而是由Q.js开源项目组发起，目的是让前端代码更健壮，但是她基于Q.js框架。因为是开源项目，所以我们能花在上面的时间有限，但是我们一直在努力！
## <a name='TOC'>内容列表</a>
1. [约定](#promise)
1. [类型](#types)
1. [对象](#objects)
1. [数组](#arrays)
1. [字符串](#strings)
1. [函数](#functions)
1. [属性](#properties)
1. [变量](#variables)
1. [条件表达式和等号](#conditionals)
1. [块](#blocks)
1. [注释](#comments)
1. [空白](#space)
1. [分号](#semicolons)
1. [类型转换](#type-coercion)
1. [事件](#events)
1. [模块](#modules)
1. [性能](#performance)
1. [贡献者](#contributors)

## <a name='promise'>约定</a>
	约定的好处可以让我们代码风格至少在大体上保持一致
	1、我们统一使用jshint来预编译js文件，从而减少未知错误
	2、四个空格缩进
	3、不扩展内置对象
	4、尽量不使用全局变量
	5、不随意使用闭包
	6、对有意义的变量使用语义的驼峰命名，临时非重要的变量极简短命名
	7、块语句和循环里面不嵌套函数声明式
	8、不使用"=="用"==="代替
	9、函数使用表达式或者自执行函数声明
	10、当拼接字符串过多的时候用数组方式
	11、每个语句结束必须使用分号
	12、在函数作用域里面尽可能的最先声明变量
	13、如果简单循环就用while循环
	14、代码不向文档写入样式比如"style="color:#fff"而是控制class的值
	15、html的id属性值只提供给js调用
	16、不到万不得已不要用class属性值做被选取DOM对象
	17、尽量减少DOM操作，因为它开销昂贵
	18、对需要多次操作并且操作对象性能开销大，必须在第一次操作时进行缓存
	19、不使用eval
	20、尽量使用内置函数代替正则匹配，正则开销也很昂贵
	21、只书写重要的注释

**[[⬆]](#TOC)**

## <a name='types'>类型</a>

- **javascript一共有5种原始值** 在某种意义上它被称为数据类型

+ `string`
+ `number`
+ `boolean`
+ `null`
+ `undefined`

- **我们在使用原始值的时候直接这样**

```javascript
var a='我是字符串',
	b=1,
	c=false,
	d=null;
```
- **不要这样**

```javascript
	var a=string('我是字符串');
```
- **Object**对象
对象声明采用字面量方式
```javascript
var obj={}
```
- **Array**对象
数组声明采用字面量方式
```javascript
var arr=[]
```

**[[⬆]](#TOC)**

## <a name='objects'>对象</a>
在js里面一切皆是对象，我们除了可以扩展内置对象外还可以自定义对象。
当我们在使用Q.js框架的时候，我们希望对自定义对象进行扩展，那么我们可以这么做：
```javascript
var obj={
	a:function(){},
	b:function(){}
};
var o=Q.extend(obj,{c:function(){}});
```
我们也可以基于原型链
```javascript
var obj={
	a:function(){},
	b:function(){}
};
obj.prototype.c=function(){};
```

**[[⬆]](#TOC)**

## <a name='arrays'>数组</a>
数组可以存很多东西，比如字符串、函数、对象、数字等。
声明数组不能用new声明必须以字面量的方式如：
```javascript
var arr=[];
```
我们在借用数组的方法时候不推荐这样[].prototype.slice.call()，必须这样Array.prototype.slice.call()

**[[⬆]](#TOC)**

## <a name='strings'>字符串</a>
字符串我们经常用到很多时候我们用“+”号来链接字符串，如果字符串很长那么必须截取字符串存入数组，建议字符串超过80个字符的时候进行截取。
split();join();这两个方法足够拼接字符串。
“+”号的性能在高版本的浏览器性能理论上别join的性能还高

**[[⬆]](#TOC)**

## <a name='functions'>函数</a>
自执行函数用(function(){}())方法不要用new function(){}方法。
声明函数使用var 函数名=function(){}，请勿用function 函数名(){}。
每个函数应当是有意义的，即具有可复用性和可扩展性。

**[[⬆]](#TOC)**

## <a name='properties'>属性</a>
js里面只要是对象就可以扩展属性，在这里我想说的就是利用一些原生属性来代替自己写的功能函数和正则。

**[[⬆]](#TOC)**

## <a name='variables'>变量</a>
多个变量声明:
```javascript
var a=1,
	b=2,
	c=3;
```
不能这样声明变量:
```javascript
var a=b=1;
```

**[[⬆]](#TOC)**

## <a name='conditionals'>条件表达式和等号</a>
用“===”代替“==”

**[[⬆]](#TOC)**

## <a name='blocks'>块</a>
js用作用域来代替像高级语言块的概念。

**[[⬆]](#TOC)**

## <a name='comments'>注释</a>
js的注释如果是单行就用“//”，多行就用“/****/”。
不要写没意义的注释。

**[[⬆]](#TOC)**

## <a name='space'>空格</a>
js解析引擎和java等语言的解析引擎不同，在非必要的情况下不留空格

**[[⬆]](#TOC)**

## <a name='semicolons'>分号</a>
类似于以下的自执行函数必须两头加分号：
```javascript
;(function(){}());
```
每一个语句结束必须分号结尾;

**[[⬆]](#TOC)**

## <a name='type-coercion'>类型转换</a>
当我们执行类型转换的时候必须这样：
```javascript
var a=1,
	b,
	c;
// number 转 string
b=''+a;
// string 转 number 只适合取整数
c=parseInt(b,10);
```

**[[⬆]](#TOC)**

## <a name='events'>事件</a>
事件是前端的又一个核心。
对那些开销大的事件我们必须在她完成任务立即销毁。

**[[⬆]](#TOC)**

## <a name='modules'>模块</a>
模块化的好处是让我们的前端结构清晰，利于以后的维护和扩展，并且让局部错误不影响整体运行。
Q.js提供了模块加载。
模块的划分以功能为主，在设计模块的时候尽量降低耦合度。
模块之间的依赖关系不能是功能依赖关系。

**[[⬆]](#TOC)**

## <a name='performance'>性能</a>
性能在现在的前端领域日益重要，提升性能的提升请参考一些专门的书籍。
- **以下为我们约定：**
- **用new Function("return"+a);代替elva();**
- **尽量不使用new Function("return"+a);和elva();**
- **尽量减少全局变量。**
- **不能让闭包引起内存泄露。**
- **不能大面积使用闭包。**
- **循环的时候尽量减少非必要循环。**
- **局部变量永远比全局变量的访问速度要快。**
- **适量的做一些缓存。**
- **dom操作非常昂贵。**

**[[⬆]](#TOC)**

## <a name='contributors'>贡献者</a>
单骑闯天下  (前端攻城狮，现居长沙，喜欢画画和军事)
**[[⬆]](#TOC)**