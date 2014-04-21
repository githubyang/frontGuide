# jQuery 1.0 源码分析说明文档
注:前端开发指南里面的javascript部分，本指南非官方文档，而是由Q.js开源项目组发起，目的是让前端代码更健壮。因为是开源项目，所以我们能花在上面的时间有限，但是我们一直在努力！
## <a name='TOC'>内容列表</a>
1. [简介](#introduction)
1. [框架设计思路](#ideas)
1. [DOM选择器](#selector)

## <a name='introduction'>简介</a>

jquery是一个伟大的框架，从2006年到现在，全球至少有50%以上的网站使用它。

在这里我们将对它的运作机制以及框架的思想做些蹩脚的阐述，我们的目的是让我们以后的前端代码更加的健壮。

你也许会觉得它不是最高版的jquery源码，对，没错，它是最原始的版本，但是我告诉你，很多东西都是从简单原理再到复杂的。



**[[⬆]](#TOC)**


## <a name='ideas'>框架设计思路</a>

画素描有这么一个定律，”由整体到局部再由局部到整体“。那么我们先从整体到局部吧。

剖开整个框架我们发现，里面有7个核心分别是：DOM选择、DOM属性操作、文档操作、Ajax、事件绑定、动画、延迟加载，原来jQuery就是围绕操作DOM而服务啊。

框架提供的所有公共方法都封装成jQuery对象从而实现链式调用，框架基于原型链实现继承和扩展以及挂载插件，当然，里面有很大一部分代码为了浏览器兼容而存在。


**[[⬆]](#TOC)**

## <a name='selector'>DOM选择器</a>

jquery1.0版本的选择器实现按现在的技术来看确实实现的并不怎么高明，但是在2006年的时候它绝对算得上明星。

支持的选择：
```javascript
// 来自官方API 总共支持9种选择

jQuery( selector [, context ] )
jQuery( element )
jQuery( elementArray )
jQuery( object )
jQuery( jQuery object )
jQuery()
jQuery( html [, ownerDocument ] )
jQuery( html, attributes )
jQuery( callback )
```
- **jQuery( selector [, context ] )**
例子：
```javascript
$('div.foo');
```
原理:

在框架的内部通过this.get方法来进行初始判断然后通过jQuery.find方法来选择元素，那么我们现在进入jQuery.find源码。

**[[⬆]](#TOC)**



**[[⬆]](#TOC)**



**[[⬆]](#TOC)**


**[[⬆]](#TOC)**

**[[⬆]](#TOC)**


**[[⬆]](#TOC)**


**[[⬆]](#TOC)**


**[[⬆]](#TOC)**


**[[⬆]](#TOC)**


**[[⬆]](#TOC)**


**[[⬆]](#TOC)**


**[[⬆]](#TOC)**


**[[⬆]](#TOC)**



**[[⬆]](#TOC)**

## <a name='contributors'>贡献者</a>
单骑闯天下  (前端攻城狮，现居长沙，喜欢画画和军事)
**[[⬆]](#TOC)**