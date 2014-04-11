# JavaScript规范 
注:前端开发指南里面的javascript部分，本指南非官方文档，而是由Q.js开源项目组发起，目的是让前端代码更健壮，但是她基于Q.js框架。
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
1. [空白](#whitespace)
1. [逗号](#commas)
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
- **

**[[⬆]](#TOC)**

## <a name='contributors'>贡献者</a>
单骑闯天下  (前端攻城狮，现居长沙，喜欢画画和军事)
**[[⬆]](#TOC)**