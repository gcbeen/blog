---
layout: post
category: javascript
title: javascript权威指南-脚本化文档-上
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、DOM概览</h4>
文档对象模型（DOM）表示：和操作HTML和XML文档内容的基础API。

*   树形的根部是HTMLDocument节点，它代表整个文档。
*   代表HTML元素的节点是HTMLElement节点。
*   代表文本的节点是Text节点。
*   代表注释的节点是comment节点。

<h4>二、选取文档元素</h4>

选取文档的元素的方法。

*   用指定的id属性。

<pre>
    var section1 = document.getElementById("section1");
</pre>

<pre>
    
    function foreach(aryEle, f) {
	  var i,
		  len = aryEle.length;
	  for (i = 0; i < len; i += 1) {
	    f.call(i);
	  }
	}
    
    function getElements(/* ids */) {
      var elements = {};
	  foreach(arguments, function (i) {
			    id = arguments[i];
				ele = document.getElementById(id);
				if (ele == null) {
				  throw new Error("No element with id: " + id);
				}
				elements[id] = ele;
			  });
      return elements;
    }
</pre>

低于IE 8版本的浏览器中，getElementById()对匹配元素的ID不区分大小写，而且也返回匹配name属性的元素。

*   用指定的name属性。

name属性只在少数HTML元素中有效，包括表单、表单元素、&ltiframe&gt和&ltimg&gt元素。

<pre>
    var radiobuttons = document.getElementsByName('favorite_color');
</pre>

在IE中，getElementsByName()也返回id属性匹配指定的元素。为了兼容，不要将同样的字符串同时用作名字和ID。

<pre>
    // 针对&ltform name="shipping_address"&gt元素，得到Element对象。
    var form = document.shipping_address;
</pre>

文档对象自动创建的属性可以查找到元素，最好还是显示调用getElementByName()来查找它们。

*   用指定的标签名字。

<pre>
    // 返回NodeList对象。
    var spans = document.getElementsByTagName('span');
    
	var firstPara = document.getElementByTagName('p')[0];

	// 文档中所有的元素
	var allEles = document.getElementByTagName('*');
    
	var firstParaSpans = firstPara.getElementsByTagName('span');

</pre>

HTMLDocument类定义一些快捷属性来访问各种各样的节点。如：images、forms和links等属性指向行为类似只读数组的img、form和a元素（包含href属性）的集合。这些属性指代HTMLCollection对象。它们可以用元素的ID或名字来索引。

<pre>
    document.shipping_address;
	document.forms.shipping_address;
</pre>

HTMLDocument也定义embeds属性，它们都是HTMLCollection类型的<embed>元素的集合。

anchors是非标准属性，它指代有一个name属性的a元素而并不是一个href属性。

scripts在HTML5中是标准属性，它是HTMLCollection类型的script元素的集合。

document.body是一个HTML文档的body元素，document.head是head元素。这些属性中是会定义。Document类的documentElement属性指代文档的根元素，HTML文档中，它总是指代html元素。

节点列表和HTML集合

getElementsByName()和getElementsByTagName()都返回NodeList对象，document.images和document.forms的属性为HTMLCollection对象。这些对象都是只读的类数组对象。

<pre>

    for (var i = 0; i < document.images.length; i += 1) {
      document.images[i].style.display = 'none';
    }

    var content = Array.prototype.map.call(document.getElementsByTagName('p'), function (e) { return e.innerHTML; });

</pre>

NodeList和HTMLCollection对象不是历史文档状态的一个静态快照，而是通常是实时的，而且当文档变化是它们所包含的元素列表能随之变化。

通常，NodeList和HTMLCollection的实时性非常有用。但是，如果要在迭代一个NodeList对象生成一个静态的副本：

<pre>
    var snaphot = Array.prototype.slice.call(nodelist, 0);
</pre>

*   用指定的css类。

<pre>
    var warnings = document.getElementByClassName("warning");
    var log = document.getElementById("log");
    var fatal = log.getElementByClassName("fatal error");
</pre>

*   匹配指定的css选择器。

<pre>

    //css选择器
    #nav
    div
    .warning
    
    p[lang='fr']
    *[name='x']
    
    span.fatal.error
    span[lang='fr'].warning
    
    #log span
    #log>span
    body>h1:first-child
    
    div, #log

</pre>


<pre>
    document.querySelectorAll();
	// 接受包含一个css选择器的字符串参数，
	// 返回一个表示文档中匹配选择器的所有元素的NodeList对象，并不是实时的。
	// 如果没有匹配元素返回一个空的NodeList对象。
	// 如果选择符非法，querySelector()将抛出一个异常。
	
	document.querySelector();
	// 返回第一个匹配的元素。没有匹配的元素返回null。
</pre>

这两个方法在Element节点中也有定义，在元素上调用时，指定的选择器仍然在整个文档中匹配，然后过滤出结果集以便它只包含指定元素的后代元素。这看起来是违反常规，因为选择器字符串能包含元素的祖先而不仅仅是上述所匹配的元素。

document.all[]已经被标准的方法（getElementById()和getElementByTagName()所取代）现在不应该使用。

<pre>
    
    document.all[0];              // 文档的第一个元素
    document.all["navbar"];       // id或name为“navbar”的元素
    document.all.navbar;          // id或name为“navbar”的元素
    document.all.tags("div");     // 文档中所有的<div>元素
    document.all.tags("p")[0];    // 文档中第一个<p>元素
    
</pre>

<h4>三、文档结构和遍历</h4>

Node定义了一下重要的属性。
<pre>
    parentNode：给节点的父节点，或者针对类似Document对象应该是null,因为它没有父节点。
</pre>
<pre>
    childNodes：只读的类数组对象（NodeList对象），它是节点的字节点的实时表示。
</pre>
<pre>
    firstChild、lastChild：该节点的字节点中的第一个和最后一个，如果该节点没有字节点则为null。
</pre>
<pre>
    nextSibling、previoursSibling：该节点的兄弟节点的前一个和后一个。这两个属性将节点之间以双链表的形式连接起来。
</pre>
<pre>
    nodeType：给节点的类型。
	9：代表Document节点，1：代表Element节点，3：代表Text节点，8：代表Comment节点，11：代表DocumentFragment节点。
</pre>
<pre>
    nodeValue：Text节点或Comment节点的文本内容。
</pre>
<pre>
    nodeName：元素的标签名，以大写形式表示。
</pre>

示例：
<pre>
    document.childNodes[0].childNodes[1];
    document.firstChild.firstChild.nextSibling;
    // 对文档的变化及其敏感。
    &lt;html&gt;&lt;head&gt;&lt;title&gt;Test&lt;/title&gt;&lt;/head&gt;&lt;body&gt;Hello&nbsp;Word&lt;/body&gt;&lt;/html&gt; // 返回 BODY
    &lt;html&gt;&lt;head&gt;&lt;title&gt;Test&lt;/title&gt;&lt;/head&gt;&lt;body&gt;Hello&nbsp;Word&lt;/body&gt;&lt;/html&gt; // 返回 HEAD
</pre>

<h4>四、作为元素树的文档</h4>

将主要兴趣集中在文档中的元素上，将文档看作是Element对象树，忽略部分文档：Text和Comment节点。

Element对象的children属性。只包含Element对象。

Element属性。

<pre>

    firstElementChild，lastElementChild
    nextElementSibling, previousElementSibling
    chileElementCount：字元素的数量。

</pre>
逐个元素的文档遍历的API并未完全标准化。可以通过可移植的遍历函数来实现。
<pre>
    function parent(e, n) {
      n = n || 1;
      while (n && e) {
    	e = e.parentNode;
    	n -= 1;
      }
      if (!e || e.nodeType !== 1) {
        return null;
      }
      return e;
    }

    function _sibling(e, nextFlag) {
	  if (nextFlag) {
	    e = e.nextElementSibling || _getEle(e, null, _nextSibling);
	  } else {
	    e = e.previousElementSibling || _getEle(e, null, _prevSibling);
	  }
	}

    function sibling(e, n) {
	  while (n > 0) {
	    _sibling(e, true);
		n -= 1;
	  }
	  while (n < 0) {
	    _sibling(e, false);
		n += 1;
	  }
	  return e;
	}
    
    function _getEle (e, f, g) {
	  f && f.call(e);
	  while (e && e.nodeType !== 1) {
	    g.call(e);
	  }
	  return e;
	}
    
    function _firstChild(e) {
	  e = e.firstChild;
	}
    
    function _nextSibling(e) {
	  e = e.firstElementChild || _getEle(e, _firstChild, function () { e = e.nextSibling; });
	}
    
    function _lastChild(e) {
	  e = e.lastChild;
	}
    
    function _prevSibling(e) {
	  e = e.lastElementChild || _getEle(e, _lastChild, function () { e = e.previoursSibling; });
	}
    
    function child(e, n) {
	  if (e.children) {
	    if (n < 0) {
		  n += e.children.length;
		}
		if (n < 0) {
		  return null;
		}
		return e.children[n];
	  }
	  if (n >= 0) {
	    _nextSibling(e);
		return sibling(e, n);
	  } else {
	    _prevSibling(e);
		return sibling(e, n + 1);
	  }
	}
</pre>

自定义Element的方法。

<pre>
    Element.prototype.next = function () {
      return this.nextElementSibling || (function () {
    		  var sib = this.nextSibling;
    		  while (sib && sib.nodeType !== 1) {
    		    sib = sib.nextSibling;
    		  }
    		  return sib;
    		} ());
    }
</pre>

在不包含children属性的非IE浏览器中模拟Element.children属性。

<pre>
    if (!document.documentElement.children) {
      Element.prototype.__defineGetter__("children", function () {
    		    var kids = [];
    			for (var c = this.firstChild; c != null; c = c.nextSibling) {
    			  if (c.nodeType === 1) {
    			    kids.push(c);
    			  }
    			}
    			return kids;
    		  });
    }
</pre>

