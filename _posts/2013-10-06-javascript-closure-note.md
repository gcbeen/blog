---
layout: post
category: javascript
title: javascript-闭包
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}
<h4>一、如何从外部读取局部变量</h4>
在函数内部再定义一个函数

<pre>
    function func1() {
	  var n = 999;
	  
	  function func2() {
		console.log(n);
	  }

	  return func;
	}
    
    var result = f1();
	result(); // 999
</pre>
这就是一个闭包。

<strong>闭包就是能够读取其他函数内部变量的函数。由于在javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。</strong>

闭包就是将函数内部和函数外部连接起来的一座桥梁。

<h4>二、闭包的用途</h4>
闭包可以用在很多地方。最大的两个用处

*   读取函数内部的变量。
*   让函数内部变量的值始终保持在内存中。

<pre>

    function func1() {
    
    	var n = 999;
    
    	nAdd = function () {
    	  n += 1;
    	};
    
    	function func2() {
    	  console.log(n);
    	}
    
    	return func2;
    }
    
    var result = func1();

    result();

    nAdd();

    result();

</pre>
在这段代码中，result实际上就是闭包func2函数。它一共运行了两次，第一次值是999，第二次的值是1000。这证明了，函数func1中的局部变量n一直保存在内存中，并没有在func1调用后被自动清除。

原因在于func1是func2的父函数，而func2被赋给了全局变量，这导致func2始终在内存中。而func2的存在依赖于func1,因此func1也始终在内存中，不会在调用结束后，被垃圾回收机制回收。

注意
<pre>
nAdd = function () {
	n += 1;
}
</pre>
nAdd 是全局变量。nAdd的是一个匿名函数，这个匿名函数本身也是一个闭包。

<h4>三、使用闭包注意点</h4>
*   闭包会使得函数内部变量被保存在内存中。在退出函数之前，将不使用的局部变量全部删除。
*   闭包会在父函数外部改变父函数内部变量的值。所以，如果把父函数当作对象（object）使用，把闭包当作他的公用方法，把内部变量当作它的私有属性，这是一定不要随便改变父函数内部变量的值。

<h4>四、两个示例</h4>
<pre>

    var name = "The Window";
    var object = {
      name: "My Object",
      getNameFunc: function () {
        return function () {
    	  return this.name;
    	};
      }
    };
    console.log(object.getNameFunc()()); // The Window

</pre>

<pre>

   var name = "The Window";
   var object = {
     name: "My Object",
     
     getNameFunc: function () {
       var that = this;
   	return function () {
   	  return that.name;
   	};
     }
   };
   console.log(object.getNameFunc()()); // My Object

</pre>
