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

在框架的内部通过this.get方法作为入口(在这里该方法负责返回包装好的jquery对象)，然后在该参数里面进行判断，调用jQuery.find方法。

现在我们进入jQuery.find方法内部:
```javascript
for ( var i = 0; i < ret.length; i++ ){
    r = jQuery.merge( r,m[2] == "*" ?jQuery.getAll(ret[i]) :ret[i].getElementsByTagName(m[2]));
}
```
通过jQuery.merge和getElementsByTagName返回包装成数组的所有div标签。

然后通过jQuery.filter方法再对标签进行匹配筛选，现在进入jQuery.filter方法内部:
```javascript
filter: function(t,r,not) {
    var g = not !== false ? jQuery.grep : function(a,f) {return jQuery.grep(a,f,true);};
    while ( t && /^[a-z[({<*:.#]/i.test(t) ) {// 这里我们需要关注它的循环次数
        var p = jQuery.parse;// 正则匹配的数组
        for ( var i = 0; i < p.length; i++ ) {
            var re = new RegExp( "^" + p[i][0].replace( 'S', "([a-z*_-][a-z0-9_-]*)" ).replace( 'Q', " *'?\"?([^'\"]*?)'?\"? *" ), "i" );
            var m = re.exec( t );
			if ( m ) {
                if ( p[i][1] )
                    m = ["", m[1], m[3], m[2], m[4]];
                // 删除我们刚才匹配
                t = t.replace( re, "" );
				break;
            }
        }
        if ( m[1] == ":" && m[2] == "not" )
            r = jQuery.filter(m[3],r,false).r;
        else {
            var f = jQuery.expr[m[1]];
            if ( f.constructor != String ){
                f = jQuery.expr[m[1]][m[2]];
            }
            eval("f = function(a,i){" + ( m[1] == "@" ? "z=jQuery.attr(a,m[3]);" : "" ) + "return " + f + "}");
            r = g( r, f );
        }
    }
    // 返回过滤元素的数组 (r)
    // 修改后的字符串 (t)
    return { r: r, t: t };
},
```
大部分处理基本上在这里面完成了，声明变量g指向方法jQuery.grep，里面有个while循环，参数有个正则匹配，在这里可以匹配两次，也就是说while会有两次循环，记住这可是关键。

jQuery.parse是一个正则匹配二维数组，里面的数字值代表true or false后面会用到，里面我们会看到大写的字母，那是应为它是替换标识。

现在开始进行循环匹配，如果匹配不成立就会返回null。里面有个条件匹配，在这里它只干了一件事，就是删除匹配的字符串。

跳出for循环之后则开始执行下一个判断语句的第二个分支，jQuery.expr是取得筛选的操作方法，但是取出来是字符串的，所以就有下面的eval。

注意了，这里就开始执行真正的筛选了。grep方法里面有这么一个循环：

```javascript
for ( var i = 0; i < elems.length; i++ ){
    if ( !inv && fn(elems[i],i) || inv && !fn(elems[i],i) ){
        result.push( elems[i] );
    }
}
```
这里面调用了方法进行了匹配，1、m[2]== '*'||a.nodeName.toUpperCase()==m[2].toUpperCase()，jQuery.className.has(a,m[2])

第一个的意思是节点的名称全部转换成大写和正则捕获的m[2]转成大写进行对比。

第二个的意思是用正则匹配class值(看className.has方法源码)

在这个循环里面只有返回真的就压入result数组，然后返回。

filter方法最后一句是组成对象返回。

再回到find方法最后一个分支，filter方法返回的对象被val变量指向，最后通过jQuery.merge方法组装成数组返回。

再回到this.get方法，查看它的源码我们发现，它只干了一件事那就是把返回的数组加入到jquery对象。

这样就大功告成了，是不是感觉很多函数穿插，那没办法，jquery就是这样。如果觉得绕来绕去有点晕，那千万别灰心，刚开始看都是这样的。

记得有个大师说过“得道者犹如庖丁解牛，不得道者犹如坠云雾”。

如果你能看到这里，那么我们会发现，DOM操作开销昂贵，你看一个选择饶了这么大一个圈。（性能问题这里不细说）



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