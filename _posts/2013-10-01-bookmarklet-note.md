---
layout: post
category: javascript
title: bookmarklet 编写
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、什么是Bookmarklet?</h4>
Bookmarklet是 书签是javascript: URL,保存的是一小段脚本。

<pre>
    &lta href="javascript:alert('hi');"&gtxxx&lt/a&gt
</pre>
<h4>二、Bookmarklet的优点</h4>

*   安装快速  安装Bookmarklet，就是在收藏夹中保存一段代码，一步就能完成。所有浏览器都原生支持。
*   使用方便  用的时候点一下这个链接就行。
*   开发容易  一段Javascript代码就是Bookmarklet的所有内容，不需要用到其他技术，不开发一个浏览器插件简单多了。
*   跨浏览器  所有的浏览器都支持Bookmarklet。如果写的正确，同样一个Bookmarklet在各种浏览器上都能正常使用。

<h4>三、Bookmarklet的编写规则</h4>

1.   必须以“javascript:”开头  浏览器把“javascript:”当作协议看待。等同于&ltscript&gt&lt/script&gt之间的代码。
2.   所有的代码必须在一行。
3.   使用单引号。因为html语言主要使用双引号，所以Bookmarklet优先使用单引号。必须使用双引号就采用URL编码形式"%22"。
4.   不要污染全局变量  Bookmarklet最好不要生成新的全局变量，可以采用直接运行匿名函数的方式。必须设置全局变量 就使用全部大写的形式。
5.   对文本和URL进行编码  为了防止出现非法字符，代码以外的文本都应该使用encodeURIComponent()函数进行编码，比如把空格变成%20。

<h4>四、Bookmarklet的编写技巧</h4>

获取网页信息

<pre>
    document.title
	location.href

	var t;
	t = (function () {
		if (window.getSelection) {
		  return window.getSelection().toString();
		} else if (document.getSelection) {
		  return document.getSelection();
		} else if (document.selection) {
		  return document.selection.createRange().text;
		}
		return '';
	}());

</pre>
防止刷新页面

使用void()命令。

<pre>
    javascript:document.links[0].href='http://www.ibm.com/'
</pre>

加void以后，页面不会跳转。
<pre>
    javascript:void(document.links[0].href='http://www.ibm.com/');
</pre>

排除框架。

对于使用“框架”（frameset）的网页，那些需要操作页面的Bookmarket一般不起作用。

iframe除外

<pre>
    if (frames.length > document.getElementsByTagName('iframe').length) {
		alert('对不起，不适用于框架。');
	} else {
	  // coding
	}
</pre>

连接外部javascript代码

<pre>
    javascript:(function () {
			var script = document.createElement('script');
			script.setAttribute('src, 'http://........./file.js');
			document.getElementByTagName('head')[0].appendChild(script);
			}());
</pre>
添加外部函数库
如果Bookmarklet需要用到函数库，就必须把它加进来。但是，前提是必须检查一下原页面是否已经加载了这个库。

<pre>
    if (!window.jQuery) {
	  script = document.createElement('script');
	  script.src="http://ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js";
	  script.onload = foo;
	  document.body.appendChild(script);
	} else {
		foo();
	}

    function foo() {
	  /* ... */
	}
</pre>
