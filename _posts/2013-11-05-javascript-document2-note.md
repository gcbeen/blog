---
layout: post
category: javascript
title: javascript权威指南-脚本化文档-上
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、属性</h4>

<pre>
    var image = document.getElementById("myImage");
    var imgUrl = image.src;
    image.id === "myImage";
</pre>

<pre>
    var f = document.forms[0];
    f.action = "http://www.example.com/submit.php";
    f.method = "POST";
</pre>

Javascript属性名：

*   大小写敏感。从HTML属性名转换到Javascript属性名应该小写。
*   属性名包含多个单词除第一个外其他都首字母大写（defaultChecked或tabIndex）
*   HTML属性名在Javascript中是保留字。一般规则是属性名加前缀“html”。如HTML的for属性在javascript中变为htmlFor属性。但是class除外，在Javascript中变为className。

表示HTML属性的值通常是字符串。当属性为布尔值或数值，属性也是布尔值或数值。事件处理程序属性值总是为Function对象（或null）。任何HTML元素的style属性值是CSSStyleDeclaration对象，而不是字符串。

获取和设置非标准HTML属性（getAttribute()、setAttribute()方法）。

<pre>
    var image = document.images[0];
    var width = parseInt(image.getAttribute("WIDTH"));
    image.setAttribute("class", "thumbnail");
</pre>

getAttribute()获取的属性值都被看作是字符串。使用标准属性名，甚至当这些名称为Javascript保留字也不例外。

hasAttribute()和removeAttribute()方法

有些属性（如HTML的表单元素的disabled属性）在一个元素中是否存在是重点关键。

<h4>二、数据集属性</h4>

<pre>
&ltspan class="sparkline" data-ymin="0" data-ymax="10"&gt
1 1 1 2 2 2346 54 2456 5456 
&lt/span&gt
</pre>

<pre>
    var sparklines = document.getElementByClassName("sparkline");
    for (var i = 0; i < sparklines.length; i += 1) {
      var dataset = sparklines[i].dataset;
      var ymin = parseFloat(dataset.ymin);
      var ymax =parseFloat(dataset.ymax);
      var data = sparklines[i].textContent.split(' ').map(parseFloat);
      drawSparkline(sparklines[i], ymin, ymas, data);
    }
</pre>

<pre>
    var sparklines = document.getElementByClassName("sparkline");
    for (var i = 0; i < sparklines.length; i += 1) {
      var elt = sparklines[i];
      var ymin = parseFloat(elt.getAttribute("data-ymin"));
      var ymax = parseFloat(elt.getAttribute("data-ymax"));
      var points = elt.getAttribute("data-points");
      var data = elt.textContent.split(" ").map(parseFloat);
      drawSparkline(elt, ymin, ymax, data);
    }
</pre>

<h4>三、作为Attr节点的属性</h4>
非Element对象的任何节点，该属性为null。Element对象，attributes属性是只读的类数组对象，它代表元素的所有属性。attributes对象也是实时的。

<pre>
    document.body.attributes[0]
    document.body.attributes.bgcolor;
    document.body.attributes["ONLOAD"];
</pre>

<h4>四、元素的内容</h4>

*   作为HTMl元素的内容

innerHTML、outerHTML 返回元素内容。

insertAdjacentHTML()插入指定元素指定位置。

<pre>
    beforebegin &ltdiv id="target"&gt afterbegin This is the element content beforeend &lt/div&gt afterend
	
	obj.insertAdjacentHTML("afterend", "&ltbr&gt&ltinput name='txt1' &gt");
</pre>

*   作为纯文本的元素内容

<pre>
    var para = document.getElementByTagName("p")[0];
    var text = para.textContent;
    para.textContent = "Hello Word!"
</pre>

<pre>
    
    function textContent(element, value) {
      var content = element.textContent;
      if (value === undefined) {
        if (content !== undefined) {
    	  return content;
    	} else {
    	  return element.innerText;
    	}
      } else {
        if (content !== undefined) {
    	  element.textContent = value;
    	} else {
    	  element.innerText = value;
    	}
      }
    }
    
</pre>
textContent属性就是将指定元素的所有后代Text节点简单地串联在一起。innerText不返回script元素的内容，并试图保留表格格式，同时，innerText试图保留表格格式，同时针对某些表格元素（如 table tbody tr）是只读的属性。

内联script元素有一个text属性用来获取它们的文本，浏览器不显示，并且HTML解析器忽略脚本中尖括号和星号，这使得script元素成为应用程序用来嵌入任意文本内容的一个理想的地方，简单地将元素的type属性设置为某些值（“text/x-custom-data”），就标明了脚本为不可执行的Javascript代码，它的text属性将返回数据给你。

*   作为Text节点的元素内容

<pre>
    function textContent(e) {
      var child, type, s = "";
      for (child = e.firstChild; child != null; child = child.nextSibling) {
    	type = child.nodeType;
    	if (type === 3 || type === 4) {
    	  s += child.nodeValue;
    	} else if (type === 1) {
    	  s += textContent(child);
    	}
      }
      return s;
    }
</pre>

