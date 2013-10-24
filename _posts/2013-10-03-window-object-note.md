---
layout: post
category: javascript
title: Javascript权威指南-window对象
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}
<h4>一、setTimeout()和setInterval()</h4>

*   Window对象的setTimeout()方法用来实现一个函数在指定的毫秒数之后运行。setTimeout()返回一个值，这个值可以传递给clearTimeout()用于取消这个函数的执行。
*   setInterval()和setTimeout()一样，只不过这个函数会在指定毫秒数的间隔里重复调用。和setTimeout()一样，setInterval()也返回一个值，这个值可以传递给clearInterval(),用于取消后续函数的调用。

<pre>
    setInterval(updateClock, 60000);
</pre>

<pre>
    function invoke(f, start, interval, end) {
	    if (!start) {
		    start = 0;
		}
		if (arguments.length <= 2) {
			setTimeout(f, start);
		}else {
		    setTimeout(repeat, start);
			function repeat() {
			    var h = setInterval(f, interval);
				if (end) {
				  setTimeout(function() {
					  clearInterval(h);
			      }, end);
				}
			}
		}
	}
</pre>
如果以0毫秒的超时时间来调用setTimeout()，那么指定的函数不会立即执行，相反，会把它放到队列中，等到前面处于等待状态的事件处理程序全部执行完成后，再调用。
<h4>Location</h4>
Window对象的location属性引用Location对象，它表示该窗口中当前显示的文档的URL，并定义了方法来使窗口载入新的文档。

Document的对象的location属性也引用到Location对象。

<pre>
    window.location === document.location
</pre>

Document对象也有一个URL属性，是文档首次载入后保存该文档的URL的静态字符串。如果定位到文档中的片段标识符（如#table-of-contents），Location对象会做相应的更新，而document.URL属性却不会改变。

<h4>二、解析URL</h4>
Window对象的location属性引用的是Location对象，它表示该窗口中当前显示的文档的URL。
Location对象的属性。
*   href属性是一个字符串，包含URL的完整文本。Location对象的toString()方法返回href属性值，在会隐式调用toString()的情况下，可以使用location代替location.href。
*   protocol：
*   host：
*   hostname：
*   port：
*   pathname：
*   search：查询字符串，返回的是问号之后的url。
*   hash：URL中的“片段标识符”部分

<pre>
    /*
	 *
	 * var args = urlArgs();
	 * var q = args.q || "";
	 * var n = args.n ? parseInt(args.n) : 10;
	 */
    function urlArgs() {
	    var args = {},
		    query = location.search.substring(1),
		    pairs = query.split("&"),
			n = pairs.length,
			pos,
			name,
			value;
		while (n) {
			pos = pairs[i].indexOf('=');
		    if (pos == -1) {
				continue;
			}
			name = pairs[i].substring(0,pos);
			value = pairs[i].substring(post+1);
			args[name] = decodeURIComponent(value);
		}
		return args;

	}
</pre>
<h4>三、载入新文档</h4>
*   assign()方法：使窗口载入并显示指定的URL中的文档。
*   replace()方法：载入新的文档之前会从浏览历史中把当前文档删除。
*   reload()方法：重新载入当前文档。
*   使用浏览器跳转到新页面：直接把新的URL赋给location属性。

<pre>
    location = "http://www.oreilly.com"; 
    location = "page2.html";
	location = "#top"; // 跳转到顶部
	location.search = "?page=" + (pagenum + 1); //载入下一个页面
</pre>

*   如果脚本无条件地载入一个新文档，replace()方法可能是比assgin()方法更好的选择。否则，“后退”按钮会把浏览器带回到原始文档，而相同的脚本会再次载入新文档。
*   如果检测到用户的浏览器不支持某些特性来显示功能齐全的版本，可以用location.replace()来载入静态的HTML版本。

<pre>
    if (!XMLHttpRequest) {
	    location.replace("staticpage.html");
	}
</pre>
<h4>四、浏览历史</h4>
Window对象的history属性引用的是该窗口的History对象。History对象是用来把窗口的浏览历史用文档和文档状态列表的形式表示。History对象的length属性表示浏览历史列表中的元素数量，但脚本不能访问已保存的URL。

*   back()方法和forward()方法与浏览器的“后退”和“前进”按钮一样,使浏览器在浏览历史中前后跳转一格。
*   go()接受一个整数参数，可以在历史列表中向前或向后跳转任意多页。

<pre>
    history.go(-2);
    history.go(2);
</pre>
如果窗口包含多个子窗口（比如iframe元素），子窗口的浏览历史会按时间顺序穿插在主窗口的历史中。主窗口调用history.back()可能会导致其中一个子窗口往回跳转到前一个显示文档，但主窗口保留当前状态不变。

浏览历史管理

1.   应用程序必须要在窗口浏览历史中创建一个新条目来管理自身的历史记录
2.   用历史条目关联自身的状态信息，判断什么时候用户使用了“后退”按钮来移动到不同的历史条目，联合那个条目获取状态信息，并且重新创建应用程序之前的状态。

实现的一种方式

使用隐藏的iframe来保存状态信息并在浏览器历史中创建条目，需要用Document对象的open()和write()方法动态地把一个新文档写入一个隐藏的窗体。文档内容应该包含重新创建应用状态所需的状态信息。当用户单击“后退”按钮，隐藏的窗体的内容会改变。使用setInterval()每秒对隐藏的窗体检测两到三次，来看它是否改变了。

<h4>五、Navigator对象</h4>
Window对象的navigator属性引用的是包含浏览器厂商和版本信息的Navigator对象。IE还支持clientInformation属性。

过去，Navigator对象通常被脚本用来确定它们是在IE中还是在Netscape中运行。这种浏览器嗅探方法有问题，因为它要求随着新浏览器和现有浏览器的新版本的引入而不断地调整。有一种更好的功能测试方法，只需要测试所需要的功能。

<pre>
    function clientIsIE() {
	  if (window.addEventListener) {
	    return false;
	  } else if (window.attachEvent) {
	    return true;
	  } else {
	    return false;
	  }
	}
</pre>
浏览器嗅探有时候仍然有价值，当需要解决存在于某个特定浏览器的特定版本中的特殊bug时。

Navigator对象有4个属性

*   appName Web浏览器的全称。在IE中，就是“Microsoft Internet Explorer”。在Firefox及其他浏览器中，该属性就是“Netscape”。
*   appVersion 通常以数字开始，并跟着包含浏览器厂商和版本信息的详细字符串。没有标准格式，没有办法直接用它来判断浏览器类型。
*   userAgent 浏览器在它的USER-AGENT HTTP头部中发送的字符串。这个属性通常包含appVersion中的所有信息，并且常常可能包含其他细节。没有标准格式，由于userAgent包含绝大部分信息，因此浏览器嗅探代码通常用它来嗅探。
*   platform 在其上运行浏览器的操作系统（并且可能是硬件）的字符串

<pre>
    var browser = (function (){
		var s = navigator.userAgent.toLowerCase();
		var match = /(webkit)[ \/]([\w.]+)/.exec(s) || 
					/(opera)(?:.*version)?[ \/]([\w.]+)/.exec(s) || 
					/(msie) ([\w.]+)/.exec(s) || 
					!/compatible/.test(s) && /(mozilla)(?:.*? rv:([\w.]+))?/.exec(s) || 
					[];
		return { name: match[1] || "", version: match[2] || "0" };
		}());
</pre>

*   onLine navigator.onLine属性表示浏览器当前是否连接到网络。应用程序可能希望在离线状态下把状态保存在本地。
*   geolocation Geolocation对象定义用于确定用户地理位置信息的接口。
*   javaEnabled() 一个非标准的方法，当浏览器可以运行Java小程序时返回true。
*   cookieEnable() 非标准方法，如果浏览器可以保存永久的cookie时，返回true。当cookie配置为“视具体情况而定”时，可能返回不正确的值。

<h4>六、Screen对象</h4>
Window对象的screen属性引用Screen对象。提供width和height属性指定以像素为单位的窗口大小。属性availWidth和availHeight指定实际可用的显示大小。它们排除了像桌面任务这样的特性所占的空间。 属性colorDepth指定显示的BPP（bits-per-pixel）值，典型的值是16，24，32。

<h4>七、对话框</h4>

*   alert() 向用户显示一条消息并等待用户关闭对话框。
*   confirm() 显示一条消息，要求用户单击“确定”或“取消”按钮，并返回布尔值。会产生阻塞。
*   prompt() 显示一条消息，等待用户输入字符串，并返回那个字符串。会产生阻塞。
*   showModalDialog() 显示一个包含HTML格式的模态对话框。可以给他传递参数，以及对话框里返回值。

<pre>
    do {
	  var name = prompt("what is your name ?");
	  var correct = confirm("You entered '" + name + "'.\n" );
	} while (!correct)
    alert("Hello, " + name);
</pre>
showModalDialog()的参数。

*   第一个参数指定提供对话框HTMl内容的url。
*   第二个参数是一个任意值（数组和对象均可），这个值在对话框里的脚本中可以通过window.dialogArguments属性的值访问。
*   第三个参数是一个非标准的列表，包含以分号隔开的name=value对。可以设置对话框尺寸或其他属性。用“dialogwidth”和“dialogheight”来设置对话框窗口的大小，用“resizable=yes”来允许用户改变窗口大小。

<h4>八、错误处理</h4>
Window对象的onerror属性是一个事件处理程序，当未捕获的异常传播到调用栈上时就会调用它，并把错误消息输出到浏览器的javascript控制台上。

Window对象的onerror事件处理函数的调用通过三个字符串参数。

*   第一个参数是描述错误信息的一条消息。
*   第二个参数是一个字符串，存放着引发错误的javascript代码所在文档的url。
*   第三个参数是文档中发生错误的行数。

Window对象的返回值 false 事件处理程序已经处理了错误。Firefox里的错误处理程序必须返回true来表示它已经处理了错误。

<pre>
    window.onerror = function (msg, url, line){
	  if (onerror.num < onerror.max) {
	    alert("ERROR:" + msg + "\n" + url + ":" + line);
		onerror.num += 1;
		return true;
	  }
	  onerror.max = 3;
	  onerror.num = 0;
</pre>

<h4>九、作为Window对象的属性的文档元素</h4>
如果在HTML文档中用id属性来为元素命名，并且如果Window对象没有此名字的属性，Window对象会赋予一个属性，它的名字是id属性的值，而它们的值指向表示文档元素的HTMLElement对象。

如果Window对象已经有此名字的属性，就不能使用此属性指向HTMlElement对象。 如id是“history”、“location”、或“navigator”的元素。

id元素在文档中必须是唯一的。如果元素中有一个相同的name属性（或者一个元素有name属性，而另一个元素有相同值的id属性），该名称的隐式全局变量会引用一个类数组对象，这个类数组对象的元素是所有命名元素。

有name或id属性的iframe元素是个特殊的例子。隐式创建的变量引用表示iframe元素创建的嵌套浏览器窗体的Window对象。

<h4>十、多窗口和窗口</h4>
每个桌面窗口或标签页中的Window对象都是独立的。和其他桌面窗口没有任何关系。

如果一个窗口或标签页中的脚本打开新窗口或标签页，这样这个窗口就可以和另一个窗口的文档之间互操作。

对于客户端Javascript来说，窗口、标签页、iframe和框架都是浏览上下文。对于Javascript来说，它们都是Window对象。和相互独立的标签页不同，嵌套的浏览上下文之间并不是相互独立的。在一个窗体中运行的Javascript程序总是可以看到它的祖先和子孙窗体。

<h4>十一、打开和关闭窗口</h4>
open

*   第一个参数 在新的窗口中显示的文档的URL。如果省略了或者空串，url为about:blank。
*   第二个参数 新打开的窗口的名字。如果指定的是一个已经存在的窗口的名字，会直接用已存在的窗口。否则打开新窗口。并命名为指定的名字。如果省略此参数，打开未命名窗口。
*   第三个参数 以逗号分隔开的列表，包括大小和各种属性，用以表明新窗口驶入和打开的。
*   第四个参数 只在第二个参数命名的是一个存在的窗口时采用。是个布尔值，由第一个参数指定的URL替换掉窗口浏览历史的当前条目 (true)，或在新窗口浏览历史中创建一个新的条目（false），默认是false。

HTML的target属性（如a，form标签）表示应该显示在的窗口。如“_blank”、“_parent”、“_top”。

open的返回值是代表命名或新创建的窗口的Window对象。可以在自己的Javascript代码中使用这个Window对象来引用新创建的窗口。

<pre>
    var w = window.open();
    w.alert("About to visit http://example.com");
    w.location = "http://example.com";
</pre>

在由window.open()方法创建的窗口中，opener属性引用的是打开它的脚本的Window对象。在其它的窗口中，opener为null。

<pre>
    w.opener !== null;      // true
    w.open().opener === w; // true
</pre>

如果一个窗口是内嵌在另一个窗口里的窗体，那么在它们脚本之间就可以相互操作。 _top表示顶级祖先窗口，_parent表示直接父级窗口。

<h4>十二、关闭窗口</h4>

<pre>
    w.close();
    window.close(); // 不关闭嵌入的窗体
</pre>
<h4>十三、窗体之间的关系</h4>

<pre>
    parent.history.back();
    parent == self;
</pre>

<pre>
   &ltiframe id="f1"&gt
   var iframeElement = document.getElementById("f1");
   var childFrame = document.getElementById("f1").contentWindow;
</pre>

<pre>
    var elt = document.getElementById("f1");
    var win = elt.contentWindow;
    win.frameElement === elt;  // true, frameElement窗体元素
    window.frameElement === null; // true
</pre>

frames属性

<pre>
   frames[0]
   frames[1]
   frames[2]
   parent.frames[1]
   frames['f1']
   frames.f1
</pre>
<h4>交互窗口中的Javascript</h4>
例如Web页面里有两个iframe元素，分别叫“A”和“B”，并假定这些窗体包含的文档来自相同的一个服务器，并且包含交互脚本。


顶级窗口

<pre>
    function Set() {
      // coding
    }
    var s = new Set();
</pre>

窗体A

<pre>
    var i = 3;
    window.i;
    var f = parent.B.f();
    var s = new parent.Set();
    var Set = top.Set();
    var s = new Set();
</pre>

窗体B

<pre>
   parent.A.i = 4;
   function f() {
     // coding
   }
</pre>

事实上，每个Window都有自己的原型对象，因此instanceof操作符不能跨窗口工作。例如用instanceof来比较窗体B的一个字符串和窗体A的String()构造函数时，结果为false。

