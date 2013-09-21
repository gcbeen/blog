---
layout: post
category : javascript
title: javascript note
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}
<h4>一、全局变量的问题</h4>
全局变量对于任何代码块，都是可读可写，这是javascript最大的语法缺点，对代码的模块化和重复使用非常不利。例如：两个不同的部分定义同名但不同作用的全局变量的时候，命名冲突在所难免。

web页面包含不是该页面开发这所写的代码也是非常常见的，例如：

* 第三方的javascript库
* 广告方的脚本代码
* 第三方用户跟踪和分析脚本代码
* 不同类型的小组件，标志和按钮
比方说，第三方脚本定义了一个全局变量（result），接着，在你的函数中也定义了一个全局变量（result）。其结果就是后面的变量覆盖前面的，第三方的变量（result）就没作用了。

因此，要想和其他脚本成为好邻居，应尽可能减少使用全局变量。

由于Javascript的两个特征，不自觉地创建出全局变量出乎意料的容易。首先，你可以甚至不需要声明就可以使用变量；第二，Javascript有隐含的全局概念，意味着你不声明的任何变量都会成为一个全局对象属性。

<pre>
    function sum(x, y) {
	  result = x + y; // result 为全局变量
	  return result;
	}
</pre>
应改为：

<pre>
    function sum(x, y) {
	    var result = x + y;
		return result;
	}
</pre>
语句的合并

<pre>
    var a = b = 0;
</pre>
实际是：

<pre>
    b = 0;
	var a = b;
</pre>
<h4>二、所有的变量都声明在函数头部</h4>
这样做的好处：

* 提供一个单一的地方寻找功能所需的所有局部变量
* 防止变量在定义之前使用的逻辑错误

<pre>
    function func() {
	  var a = 1,
		  b = 2,
		  sum = a + b,
		  myobject = {},
		  i,
		  j;
	  // function body
	}
</pre>

你也可以在声明的时候做一些实际的工作

<pre>
    function updateElement() {
	  var el = document.getElementById("result"),
		  style = el.style;
	  //function body
	}
</pre>
Javascript中，你可以在函数的任何位置声明多个var语句，并且他们就好象是在函数顶部声明一样发挥作用，这种行为称为hoisting（悬置/置顶解析/预解析）。

当你使用了一个变量，然后不久在函数中又声明的话，就可能产生逻辑错误。

对于Javascript，只要你的变量是在同一作用域中（同一函数），他都被当作声明了的，即使是它在var声明前使用的时候。

<pre>
    myname = "global";
    function func() {
	  alert(myname);        // "undefined"
	  var myname = "local";
	  alert(myname);        // "local"
	}
</pre>
上面代码等同于：

<pre>
    myname = "global";
	function func() {
	  var myname;
	  alert(myname);
	  myname = "local";
	  alert(myname);
	}
</pre>

<h4>for循环</h4>

<code>
    function looper() {
	  var i = 0,
          max,
          myarray = [];
      for (max = myarray.length; i < max; i += 1) {
	     ...
	  }
	}
</code>
while循环

<pre>
    function looper() {
      var myarray = [],
		  i = myarray.length;
	  while (i) {
		i -= 1;
	    // body
	  }
	}

</pre>
<h4>for-in循环</h4>
<pre>
    var man = {
       hands: 2,
	   legs: 2,
	   heads: 1
	};
    var i;
    for ( i in man) {
	  if (Object.prototype.hasOwnProperty.call(man, i) {
	    console.log(i, ":", man[i]);
	  }
	}
</pre>
<h4>避免隐式类型转换</h4>
<pre>
    var zero = 0;
	if (zero === false) {
	  //不执行
	}

	if (zero == false) {
	  //执行
	}
</pre>
<h4>避免使用eval()</h4>
使用eval()带来安全隐患，被执行的代码（例如从网络来）可能被篡改。

例如：当处理Ajax请求得到的JSON的时候，最好使用Javascript内置方法（JSON.parse()）来解析JSON，以确保有效。或来自于JSON.org的库。

给setInterval(), setTimeout()和Function()构造函数传递字符串，大部分与使用eval类似。因此避免给他们传递字符串

<pre>
    setTimeout("myFunc()", 1000);

	setTimeout("myFunc(1,2,3)", 1000);
</pre>
应该成

<pre>
    setTimeout(myFunc, 1000);

	setTimeout(function () {
	  myFunc(1, 2, 3);
	}, 1000);
</pre>
如果必须使用eval(),你可以考虑使用 new Function()代替。因为在Function()中，在局部函数作用域中运行代码评估，任何通过var定义的变量都不会自动变成全局变量。

另一种替代方法在一个即时函数中调用eval()。

<pre>
    var jsstring = "var un = 1; console.log(un);";
    eval(jsstring);

	jsstring = "var deux = 2; console.log(deux);";
	new Function(jsstring)();

	jsstring = "var trois = 3; console.log(trois);";
	(function () {
	   eval(jsstring);
	}());
    
    console.log(typeof un);      // number
	console.log(typeof deux);    // "undefined"
	console.log(typeof trois);   // "undefined"

</pre>
另外eval会干扰作用域链，污染本地变量。

<pre>
    (function () {
	  var local = 1;
	  eval("local = 3; console.log(local);");  //logs "3"
	  console.log(local);                      //logs "3"
    }());

    (function () {
	   var local = 1;
	   Function("console.log(typeof local);")(); // log undefined
	 }());
</pre>
<h4>命名函数表达式</h4>
函数声明：
function 函数名称(参数：可选) { 函数体 }
函数表达式：
function 函数名称(可选)(参数：可选){ 函数体 };

<pre>
  var func = function () {};
</pre>
<h4>Javascript Module模式</h4>
基本用法：

<pre>
    var Calculator = function (eq) {
	  var eqCt1 = document.getElementById(eq);

	  return {
		add: function (x, y) {
	      var val = x + y;
		  eqCt1.innerHTML = val;
	    }
	  }
	};
    var calculator = new Calculator('eq');
	calculator.add(2, 2);
</pre>
匿名闭包

<pre>
    (function () {
	   //....所有的变量和function都在这里声明，并且作用域也只能在这个匿名闭包里
	 }());
</pre>
引入全局变量
<pre>
    (function ($, YAHOO) {
       // coding
     }(jQuery, YAHOO));
</pre>
返回全局变量

<pre>
    var blogModule = (function () {
	  var my = {},
	      privateName = '博客苑';
	  
	  function privateAddTopic(data) {
	    // coding
	  }

	  my.Name = privateName;
	  my.AddTopic = function (data) {
	    privateAddTopic(data);
	  };

	  return my;
	} ());
</pre>
