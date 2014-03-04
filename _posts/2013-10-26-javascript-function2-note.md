---
layout: post
category: javascript
title: javascript权威指南-函数-中
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}


<h4>一、作用域链</h4>

Javascript是基于词法作用域的语言：通过阅读包含变量定义在内的数行源码就能知道变量的作用域。全局变量在程序中始终都有定义。局部变量在声明它的函数体内以及其所嵌套的函数内始终有定义。

如果将一个局部变量看作是自定义实现的对象的属性，换个角度来解读作用域。每一段Javascript代码（全局代码或函数）都有一个与之关联的作用域链（scope chain）。这个作用域链是一个对象列表或者链表，这组对象定义了这段代码“作用域中”的变量。

当Javascript需要查找变量x的值的时候（这个过程称作“变量解析”）。

1.   它会从链中的第一个对象开始查找，如果这个对象有一个名为x的属性，则会直接使用这个属性的值。
2.   如果第一个对象不存在名为x的属性，Javascript会继续查找链上的下一个对象。
3.   如果第二个对象依然没有名为x的属性，则会继续查找下一个对象，以此类推。
4.   如果作用链上没有任何一个对象含有属性x，那么就认为这段代码的作用域链上不存在x，并最终抛出一个引用错误异常。

作用域链。

1.   Javascript的最顶层代码中（也就是不包含在任何函数定义内的代码），作用域链由一个全局对象组成。
2.   在不包含嵌套的函数体内，作用域链上有两个对象，第一个是定义函数参数和局部变量的对象。第二个是全局对象。
3.   在一个嵌套的函数体内，作用链上至少有三个对象。

定义一个函数时，它实际上保存一个作用域链。当调用这个函数时，它创建一个新对象来存储它的局部变量，并将这个对象添加至保存的那个作用域链上，同时创建一个新的更长的表示函数调用作用域的“链”。


<h4>二、闭包</h4>

所有的Javascript函数都是闭包：它们都是对象，它们都关联到作用域链。

当调用函数时闭包所指向的作用域链和定义函数时的作用域链不是同一个作用域链时，事情就变得非常微妙。

<pre>
    var scope = "global scope";
    function checkscope() {
      var scope = "local scope";
      function f() {
    	  return scope;
      }
      return f();
    }
    checkscope(); // "local scope"
</pre>

比较：

<pre>
    var scope = "global scope";
    function checkscope() {
      var scope = "local scope";
      function f() {
        return scope;
      }
      return f;
    }
    checkscope()();
</pre>

<h4>三、实现闭包</h4>

每次调用Javascript函数的时候，都会为之创建一个新的对象来保存局部变量，把这个对象添加至作用域链中。当函数返回的时候，就从作用域链冲将这个绑定变量的对象删除。

1.   如果不存在嵌套函数，也没有其他引用指向这个绑定对象，它就会当作垃圾回收掉。
2.   如果定义了嵌套函数，如果这个嵌套函数对象在外部函数中保存下来，它们也会和所指向的绑定变量对象一样当作垃圾回收。
3.   如果这个函数定义了嵌套函数，并将它作为返回值返回或者存储在某处的属性里，这时就会有一个外部引用指向这个嵌套函数，他就不会被当作垃圾回收，并且它所指向的绑定变量对象也不会被当作垃圾回收。

<pre>
    var uniqueInteger = (function () {
      var counter = 0;
      return function () {
        return counter += 1;
      };
    } ());
</pre>

<pre>
    function counter() {
      var n = 0;
      return {
        count: function () {
    		     return n += 1;
    		   },
    	reset: function () {
    		     n = 0;
    		   }
      };
    }
    var c = counter(), d = counter();
    c.count();  // 1
    d.count();  // 1
    c.reset();  // 0
    c.count();  // 1
    d.count();  // 2
</pre>

<pre>
    function counter(n) {
      return {
        get count() {
    	  return n += 1;
    	},
    	set count(m) {
    	  if (m > n) {
    	    n = m;
    	  } else {
    	    throw Error("count can only be set to a larger value.");
    	  }
    	}
      };
    }
    
    var c = counter(1000);
    c.count;                // 1001
    c.count;                // 1002
    c.count = 2000;         // 2000
    c.count;                // 2001
    c.count = 2000;         // Error!
</pre>

<pre>
    function addPrivateProperty(o, name, predicate) {
      var value;
      o["get" + name] = function () {
        return value;
      };
      o["set" + name] = function (v) {
        if (predicate && !predicate(v) ) {
    	  throw Error("set" + name + ": invalid value " + v);
    	} else {
    	  value = v;
    	}
      };
    }
    var o = {};
    
    addPrivateProperty(o, "Name", function (x) {
    		  return typeof x == "string";
    		});
    o.setName("Frank");        // 设置属性
    console.log(o.getName() ); // 得到属性
    o.setName(o);              // 试图设置错误类型的值。
</pre>

在同一个作用域链中定义两个闭包，这两个闭包共享同样的私有变量或变量。

<pre>
    function constfunc(v) {
      return function () {
        return v;
      };
    }
    
    var funcs = [],
    	i;
    for (i = 0; i < 10;  i += 1) {
      funcs = constfunc(i);
    }
    funcs[5]();  // 5
</pre>

<pre>
    function constfuncs() {
      var funcs = [],
    	  i;
      for (i = 0; i < 10; i += 1) {
        funcs[i] = function () {
    	  return i;
    	};
      }
      return funcs;
    }
    var funcs = constfuncs();
    funcs[5]();       // 10
</pre>

<h4>四、书写闭包时注意的一些事</h4>

<pre>
    var self = this;                    // 将this保存至一个变量中，以便嵌套函数能够访问
    var outerArguments = arguments;     // 保存外部的参数
</pre>

<h4>五、函数的属性</h4>

*   length属性：函数形参的数量。

<pre>
    function check(args) {
      var actual = args.length,
    	  expected = args.callee.length;
      if (actual !== expected) {
        throw Error("Expected " + expected + "args; got " + actual);
      }
    }
    function f(x, y, z) {
      check(arguments);
      return x + y + z;
    }
</pre>

*   prototype属性：每个函数都包含一个prototype属性，这个属性指向一个原型对象的引用.

每个函数都包含不同的原型对象。当将函数用作构造函数的时候，新创建的对象会从原型对象上继承属性。

<h4>六、函数的方法</h4>
*   call()方法和apply()方法：通过方法的方式间接调用函数。

<pre>
    f.call(o);
    f.apply(o);
    
    o.m = f;
    o.m();
    delete o.m;
</pre>
在ECMAScript 5的严格模式中，call()和apply()的第一个实参都会变成this的值，哪怕传入的实参是原始值甚至是null或undefined。在ECMAScript 3和非严格模式中，传入的null和undefined都会被全局对象代替，而其他原始值则会被相应的包装对象所代替。

<pre>
    f.call(o, 1, 2);
    f.apply(o, [1, 2]);
</pre>

<pre>
    // apply()的参数数组可以是类数组对象也可以是真实数组。
    var biggest = Math.max.apply(Math, array_of_numbers);
    
    function trace(o, m) {
      var original = o[m];
      o[m] = function () {
        console.log(new Date(), "Entering:", m);
    	var result = original.apply(this, arguments);
    	console.log(new Date(), "Exiting:", m);
    	return result;
      };
    }
</pre>

*   bind()方法 ECMAScript 5新增的方法，在ECMAScript 3中可以轻易模拟bind()。

<pre>
    function f(y) {
      return this.x + y;
    }
    var o = {x: 1};
    var g = f.bind(o);
    g(2);
</pre>

<pre>
    function (f, o) {
      if (f.bind) {
        return f.bind(o);
      } else {
        return function () {
    	  return f.apply(o, arguments);
    	};
      }
    }
</pre>

函数式编程

<pre>
    var sum = function (x, y) {
      return x + y;
    }
    var succ = sum.bind(null, 1);
    succ(2);
    
    function f(y, z) {
      return this.x + y + z;
    }
    var g = f.bind({x: 1}, 2);
    g(3);
</pre>

<pre>
    if (!Function.prototype.bind) {
      Function.prototype.bind = function (o, /*, args */) {
        var self = this, boundArgs = arguments;
    
    	return function () {
    	  var args = [], 
    		  i;
    	  for (i = 1; i < boundArgs.length; i += 1) {
    	    args.push(boundArgs[i]);
    	  }
          for (i = 0; i < arguments.length; i += 1) {
    	    args.push(arguments[i]);
    	  }
    	  return self.apply(o, args);
    	};
      };
    }
</pre>

ECMAScript 5定义的bind()方法也有一些特性是上述ECMAScript 3代码无法模拟的。

1.   bind()返回一个函数对象，这个函数对象的length属性是绑定函数的形参个数减去绑定实参个数（不能小于0）。
2.   bind()返回的函数用作构造函数，将忽略传入bind()的this，原始函数就会以构造函数的形式调用，实参也已经绑定。
3.   bind()方法所返回的函数并不包含prototype属性（普通函数固有的prototype属性是不能删除的），并且将这些绑定的函数用作构造函数时所创建的对象从原始的为绑定的构造函数中继承prototype。
4.   在使用instanceof运算符时，绑定构造函数和未绑定构造函数并无两样。

*   toString()方法。
大多数的toString()方法的实现都返回函数的完整源码。内置函数往往返回一个类似“[native code]”的字符串作为函数体。

<h4>七、Function()的构造函数</h4>

<pre>
    var f = new Function("x", "y", "return x * y;");
    //等价于：
    var f = function (x, y) {
      return x * y;
    }
</pre>

Function()构造函数有几点需要特别注意：

1.   Function()构造函数允许Javascript在运行是动态创建并编译函数。
2.   每次调用Function()构造函数都会解析函数体，并创建新的函数对象。如果在一个循环或者多次调用的函数中执行这个构造函数，执行效率会受影响。相比之下，循环中的嵌套函数和函数定义表达式则不会每次执行时都重新编译。
3.   最后一点，他所创建的函数并不是使用词法作用域，相反，函数体代码的编译总是会在顶层函数执行。

<pre>
    var scope = "global";
    function constructFunction() {
      var scope = "local";
      return new Function("return scope");
    }
    constructFunction()();  // "global"
</pre>
我们可以将Function()构造函数认为是全局作用域中执行的eval()，eval()可以在自己的私有作用域内定义新变量和函数，Function()构造函数在实际编程过程中很少会用到。

<h4>八、可调用的对象</h4>
"可调用的对象"是一个对象，可以在函数调用表达式中调用这个对象。所有的函数都是可调用的，但并不是所有的可调用对象都是函数，截至目前，可调用对象在两个Javascript实现中不能算作函数。

*   浏览器实现了客户端方法（Window.alert()和Document.getElementsById()）,使用了可调用宿主对象。
*   RegExp对象，可以直接调用RegExp对象。

<pre>
    // 检测一个对象是否是真正的函数。
    function isFunction(x) {
      return Object.prototype.toString.call(x) === "[object Function]"
    }
</pre>
