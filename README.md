# JavaScript规范 
- 前端开发指南里面的javascript部分，本指南非官方文档，而是由Q.js开源项目组发起，目的是让前端代码更健壮。
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
1. [命名约定](#naming-conventions)
1. [存取器](#accessors)
1. [构造器](#constructors)
1. [事件](#events)
1. [模块](#modules)
1. [jQuery](#jquery)
1. [ES5 兼容性](#es5)
1. [性能](#performance)
1. [资源](#resources)
1. [哪些人在使用](#in-the-wild)
1. [翻译](#translation)
1. [JavaScript风格指南](#guide-guide)
1. [贡献者](#contributors)
## <a name='promise'>约定</a>
+ `我们统一使用jshint来预编译js文件`
+ `四个空格缩进`
+ `不扩展内置对象`
+ `尽量不使用全局变量`
+ `不随意使用闭包`
+ `对有意义的变量使用语义的驼峰命名，临时非重要的变量极简短命名`
+ `块语句和循环里面不嵌套函数声明式`
+ `不使用"=="用"==="代替`
+ `函数使用表达式或者自执行函数声明`
+ `当拼接字符串过多的时候用数组方式`
+ `每个语句结束必须使用分号`
+ `在函数作用域里面尽可能的最先声明变量`
+ `如果循环简单就用while循环`
+ `代码不向文档写入样式比如"style="color:#fff""而是控制class的值`
+ `只书写重要的注释'
**[[↑]](#TOC)**
## <a name='types'>类型</a>
- ** javascript一共有5种原始值 ** 在某种意义上它被称为数据类型
+ `string`
+ `number`
+ `boolean`
+ `null`
+ `undefined`
- ** 我们在使用原始值的时候直接这样 **
```javascript
var a='我是字符串',
	b=1,
	c=false,
	d=null;
```
- ** 不要这样 **
```javascript
	var a=string('我是字符串');
```
- ** object **对象
#};