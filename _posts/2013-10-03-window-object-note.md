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

