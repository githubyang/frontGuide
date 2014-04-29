# jQuery 1.0 源码分析说明文档(以后会慢慢丰富和完善细节)
注:前端开发指南里面的javascript部分，本指南非官方文档，而是由Q.js开源项目组发起，目的是让前端代码更健壮。因为是开源项目，所以我们能花在上面的时间有限，但是我们一直在努力！
## <a name='TOC'>内容列表</a>
1. [简介](#introduction)
1. [框架设计思路](#ideas)
1. [DOM选择器](#selector)
1. [事件系统](#events)
1. [DOM属性操作](#attribute)

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
- DOM选择器

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
概念:接受一个字符串，其中包含了用于匹配元素集合的 CSS 选择器。
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

- **jQuery( element )**
例子：
```javascript
$(document.body);
```
原理:

参数将会被this.get方法里面的参数进行判断，调用find方法进行处理，由于参数非字符串，所以直接包装成数组返回，然后this.get开始执行返回的数组，执行之后返回jquery对象。

最后完成了，这个选择方法内部处理是不是特别简单啊。

- **jQuery( elementArray )**
例子：
```javascript
$([document.body,window]);
```
原理:
参数将会被this.get方法里面的参数进行判断，调用jQuery.merge方法进行处理，返回数组，然后this.get开始执行返回的数组，执行之后返回jquery对象。

**[[⬆]](#TOC)**

- **jQuery( object )**
例子：
```javascript
$($());
```
概念:当以参数的形式向 $() 函数传递 jQuery 对象后，会创建一个该对象的副本。
原理:
参数将会被this.get方法里面的参数进行判断，调用jQuery.merge方法进行处理，返回数组，然后this.get开始执行返回的数组，执行之后返回jquery对象。


**[[⬆]](#TOC)**

- **jQuery()**
例子：
```javascript
$();
```
概念:返回包含document的jquery对象的集合。
原理:
参数将会被this.get方法里面的参数进行判断，调用jQuery.merge方法进行处理，返回数组，然后this.get开始执行返回的数组，执行之后返回jquery对象。

这几个的处理方法都一样。


**[[⬆]](#TOC)**

- **jQuery( html, [ ownerDocument ] )**
例子：
```javascript
$("<p id="test">My <em>new</em> text</p>");
```

概念：可以理解为用来创建html字符串。

原理：参数将会被正则匹配，然后转入分支执行，由jquery.clean方法执行，现在我们进入它的内部

```javascript
clean: function(a) {
    var r = [];
    for ( var i = 0; i < a.length; i++ ) {
        if ( a[i].constructor == String ) {
            var table = "";
            if ( !a[i].indexOf("<thead") || !a[i].indexOf("<tbody") ) {
                table = "thead";
                a[i] = "<table>" + a[i] + "</table>";
            } else if ( !a[i].indexOf("<tr") ) {
                table = "tr";
                a[i] = "<table>" + a[i] + "</table>";
            } else if ( !a[i].indexOf("<td") || !a[i].indexOf("<th") ) {
                table = "td";
                a[i] = "<table><tbody><tr>" + a[i] + "</tr></tbody></table>";
            }
            var div = document.createElement("div");
            div.innerHTML = a[i];
            if ( table ) {
                div = div.firstChild;
                if ( table != "thead" ) div = div.firstChild;
                if ( table == "td" ) div = div.firstChild;
            }
    
            for ( var j = 0; j < div.childNodes.length; j++ )
                r.push( div.childNodes[j] );
        } else if ( a[i].jquery || a[i].length && !a[i].nodeType ){
            for ( var k = 0; k < a[i].length; k++ ){
                r.push( a[i][k] );
            }
        }else if ( a[i] !== null ){
            r.push( a[i].nodeType ? a[i] : document.createTextNode(a[i].toString()) );
        }
    }
    return r;
},
```
这个循环只会循环一次，然后进入第一个分支，这里面有几个分支是专门用来处理表格的，第一个分支里面的最后一个循环是关键，它把创建的内容压入数组r。

最后返回r给a变量，然后进入this.get方法在变量里面进行判断然后通过jQuery.merge方法进行数组合并接着this.get方法执行返回包装好的jquery对象。

jquery里面的函数穿插虽然让代码阅读带来困难，但是它能让函数高度复用。


**[[⬆]](#TOC)**


- **jQuery( callback )**

概念：jquery有名的延迟加载。

原理：在jquery第一个分支就判断了，如果是函数就调用 jQuery(document).ready(a)执行,这是个链式调用。现在我们进入该函数内部：
```javascript
ready: function(f) {
    if ( jQuery.isReady )
        f.apply( document );
    else {
        jQuery.readyList.push( f );
    }
        return this;
    }
```
这个方法的意思就是，如果文档就绪了那么立即执行函数否则加入等待队列。

这里还有个关键代码部分，他在初始化jquery对象的时候就执行了。
```javascript
// 如果Mozilla
if ( jQuery.browser.mozilla || jQuery.browser.opera ) {
    // 使用方便的事件回调
    document.addEventListener( "DOMContentLoaded", jQuery.ready, false );
    
    // 如果IE
    // http://www.outofhanwell.com/blog/index.php?title=the_window_onload_problem_revisited
} else if ( jQuery.browser.msie ) {
    document.write("<scr" + "ipt id=__ie_init defer=true " + "src=//:><\/script>");
    var script = document.getElementById("__ie_init");
    script.onreadystatechange = function() {
        if ( this.readyState == "complete" )
            jQuery.ready();
    };
    script = null;
    
    // 对于Safari 每10毫秒检测一次文档的加载状态
} else if ( jQuery.browser.safari ) {
    jQuery.safariTimer = setInterval(function(){
        if ( document.readyState == "loaded" || 
            document.readyState == "complete" ) {
            clearInterval( jQuery.safariTimer );
            jQuery.safariTimer = null;
            jQuery.ready();
        }
    }, 10);
}
jQuery.event.add( window, "load", jQuery.ready );
```
三个分支都是判断浏览器从而实现兼容，对于ie的判断这里要重点说下，如果是ie浏览那么就向文档流写入一个脚本资源，设置“defer=true”属性，最后检测脚本对象“onreadystatechange”。
如果三个判断都失效的话就绑定加载事件“onload”。
以上基本的都分析完了，当然肯定有些地方还不够详细，那只能后面慢慢完善了。如果你意犹未尽，那可以自己再详细阅读源码。


**[[⬆]](#TOC)**

## <a name='events'>事件系统</a>

- **bind方法分析**
概念：bind方法是jquery里面的事件绑定方法。

原理：jquery的事件系统1.0版本是很简单的，也没有用到数据缓存，不过它为以后事件系统的发展指明了方向。下面我们开始进入事件绑定方法的入口文件：
```javascript
each: {

    removeAttr: function( key ) {
        this.removeAttribute( key );
    },
    show: function(){
        this.style.display = this.oldblock ? this.oldblock : "";
        if ( jQuery.css(this,"display") == "none" )
            this.style.display = "block";
    },
    hide: function(){
        this.oldblock = this.oldblock || jQuery.css(this,"display");
        if ( this.oldblock == "none" )
            this.oldblock = "block";
        this.style.display = "none";
    },
    toggle: function(){
        $(this)[ $(this).is(":hidden") ? "show" : "hide" ].apply( $(this), arguments );
    },
    addClass: function(c){
        jQuery.className.add(this,c);
    },
    removeClass: function(c){
        jQuery.className.remove(this,c);
    },
    toggleClass: function( c ){
        jQuery.className[ jQuery.className.has(this,c) ? "remove" : "add" ](this,c);
    },

    remove: function(a){
        if ( !a || jQuery.filter( [this], a ).r )
            this.parentNode.removeChild( this );
    },
    empty: function(){
        while ( this.firstChild )
            this.removeChild( this.firstChild );
    },
    bind: function( type, fn ) {
        if ( fn.constructor == String )
            fn = new Function("e", ( !fn.indexOf(".") ? "$(this)" : "return " ) + fn);
        jQuery.event.add( this, type, fn );
    },

    unbind: function( type, fn ) {
        jQuery.event.remove( this, type, fn );
    },
    trigger: function( type, data ) {
        jQuery.event.trigger( type, data, this );
    }
}
```
这一段代码是属于jquery.macros对象里面的，里面定义了我们常用事件的绑定方法，现在我们一个一个的分析，bind方法可以用来绑定很多的常用事件需要两个参数。

第一个是事件的类型，第二个是需要绑定的函数。分支语句判断第二个参数如果是字符串并且第一个字符是"."的话那么执行第一个元(这个主要是调用已有的插件或者jquery自带的方法)否则执行第二个元。

然后执行jQuery.event.add方法进行绑定。

现在我们来看下jQuery.event.add方法的源码，这个是关键，很多方法的绑定都用到它。
```javascript
event: {
    // 绑定一个事件到元素
    add: function(element, type, handler) {
        // 针对IE浏览器
        if ( jQuery.browser.msie && element.setInterval != undefined )
            element = window;
        
        // 给事件函数绑定一个唯一id
        if ( !handler.guid )
            handler.guid = this.guid++;

        // 在dom里面扩展events对象
        if (!element.events)
            element.events = {};
            
        // 获取绑定到当前元素的事件函数
        var handlers = element.events[type];
            
        if (!handlers) {
            handlers = element.events[type] = {};
                
            // 如果当前元素上已经绑定了事件函数那么把它加入到handlers
            if (element["on" + type])
                handlers[0] = element["on" + type];
        }

        // 将当前元素的事件绑定列表添加到回调处理程序
        handlers[handler.guid] = handler;
            
        // 添加事件绑定程序到当前元素
        element["on" + type] = this.handle;
    
        // 把绑定到当前元素的事件函数加入到global对象里面缓存
        if (!this.global[type])
            this.global[type] = [];
        this.global[type].push( element );
    },
        
    guid: 1,
    global: {},
        
    // 删除事件绑定 如果传入的参数是空那么移除所有的事件函数
    remove: function(element, type, handler) {
        if (element.events)
            if (type && element.events[type])
                if ( handler )
                    delete element.events[type][handler.guid];
                else
                    for ( var i in element.events[type] )
                        delete element.events[type][i];
                else
                    for ( var j in element.events )
                        this.remove( element, j );
    },
        
    trigger: function(type,data,element) {
        data = data || [];
    
        // Handle a global trigger
        if ( !element ) {
            var g = this.global[type];
            if ( g )
                for ( var i = 0; i < g.length; i++ )
                    this.trigger( type, data, g[i] );
    
            // 处理函数
        } else if ( element["on" + type] ) {
            // 模拟事件传递
            data.unshift( this.fix({ type: type, target: element }) );
    
            // 触发事件
            element["on" + type].apply( element, data );
        }
    },
        
    handle: function(event) {
        if ( typeof jQuery == "undefined" ) return;

        event = event || jQuery.event.fix( window.event );
        if ( !event ) return;
        
        var returnValue = true;

        var c = this.events[event.type];
        // 执行事件绑定函数
        for ( var j in c ) {
            if ( c[j].apply( this, [event] ) === false ) {
                event.preventDefault();
                event.stopPropagation();
                returnValue = false;
            }
        }
            
        return returnValue;
    },
    // 阻止浏览器的默认行为和事件冒泡 其实属于事件修正函数
    fix: function(event) {
        if ( event ) {
            event.preventDefault = function() {
                this.returnValue = false;
            };
            
            event.stopPropagation = function() {
                this.cancelBubble = true;
            };
        }
            
        return event;
    }
    
}
```
event对象里面的add方法用来绑定事件到元素，第一个参数是需要绑定的当前元素，第二个是事件类型，第三个是函数。

这里面的代码都已经注释，所以就不一一再叙述。


**[[⬆]](#TOC)**

## <a name='attribute'>DOM属性操作</a>



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