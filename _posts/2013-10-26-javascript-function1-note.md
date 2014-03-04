---
layout: post
category: javascript
title: javascript权威指南-函数-上
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、函数定义</h4>

<pre>
    function printprops(o) {
      var p;
      for (p in o) {
	    console.log('p-o'); //没有返回值的函数有时称之为过程
      }
    }
    
    function distance(x1, y1, x2, y2) {
      var dx = x2 - x1;
      var dy = y2 - y1;
      return Math.sqrt(dx * dx + dy * dy);
    }
    
    function factorial(x) {
      if (x <= 1) {
        return 1;
      }
      return x * factorial(x - 1);
    }
    
    // 栈结构代替函数递归。
    function factorial(x) {
      var stackArray = [],
          sum = 1;
      while(x >= 1 || stackArray.length != 0) {
        if (x > 1) {
          stackArray.push(x);
        } else {
          // 出栈
          sum = sum * stackArray.pop();
        }
      }
    }

    var square = function (x) {
    	return x * x;
    }

    var f = function fact(x) {
      if (x <= 1) {
        return 1;
      } else {
        return x * fact(x - 1);
      }
    }

    data.sort(function(a, b) { 
    		  return a - b; 
    		});

    var tensquared = (function (x) { 
    		  return x * x; 
    		} (10));

</pre>

函数命名

*   link_this
*   linkThis：
*   _linkThis：私有方法

以表达式方式定义的函数，函数的名称是可选的。一条函数声明语句实际上声明了一个变量，并把一个函数对象赋值给它。相对而言，定义函数表达式时并没有声明一个变量。

*   函数声明语句“被提前”到外部脚本或外部函数作用域的顶部。所以以这种方式声明的函数，可以被在它定义之前出现的代码所调用。
*   以表达式定义的函数在函数定义之前无法调用。
*   没有返回值的函数有时候称之为过程。

<h4>二、嵌套函数</h4>
<pre>
    function hypotenuse(a, b) {
      function square(x) {
        return x * x;
      }
      return Math.sqrt(square(a) + square(b));
    }
</pre>

<h4>三、函数调用</h4>
调用Javascript函数的4种方式：

*   作为函数调用

<pre>
    printprops({x: 1});
    var total = distance(0, 0, 2, 1) + distance(2, 1, 3, 5);
    var probability = factorial(5) / factorial(13);
</pre>
调用上下文（this的值）是全局变量。在严格模式下，调用上下文则是undefined。

<pre>
    var strict = (function () { return !this; });
</pre>

*   作为方法调用

<pre>
    o.m = f;
    o.m();
    o.m(x, y);
</pre>
对象o成为调用上下文，函数体可以使用关键字this引用该对象。

<pre>
    var calculator = {
        operand1: 1,
        operand2: 1,
    	add: function () {
    	  this.result = this.operand1 + this.operand2;
    	}
    };
    calculator.add();
    calculator.result;
</pre>

<pre>
    o["m"](x, y);
    a[0](z);
	customer.surname.toUpperCase();
	f().m();
</pre>
方法和this关键字是面向对象编程范例的核心。任何函数只要作为方法调用实际上都会传入一个隐式的实参（对象），方法调用的母体就是这个对象。

<pre>
    rect.setSize(width, height);
    setRectSize(rect, width, height);
</pre>

方法链：当方法的返回值是一个对象，这个对象还可以再调用它的方法。这种方法调用序列成为链。

<pre>
    $(":header").map(function () { return this.id; }).get().sort();
</pre>

当方法不需要返回值时，最好直接返回this。如果设计的API中一直采用这种方式，使用API就可以进行“链式调用”风格编程。

<pre>
    shape.setX(100).setY(100).setSize(50).setOutline("red").setFill("blue").draw();
</pre>

嵌套函数

1.   如果嵌套函数作为函数调用，其this值不是全局对象就是undefined，不是调用外层函数的上下文，如果想访问外部函数的this值，需要将this的值保存在一个变量里。
2.   如果嵌套函数作为方法调用，其this的值指向调用它的对象。

<pre>
    var o = {
        m: function () {
    		 var self = this;
    		 console.log(this === o);
    		 f();
    		 
    		 function f() {
    		   console.log(this === o);
    		   console.log(self === o);
    		 }
    	   }
    };
    o.m();
</pre>

*   作为构造函数
如果函数或者方法调用之前带有关键字new，他就构成构造函数调用。构造函数调用创建一个新的空对象，这个对象继承自构造函数的prototype属性。构造函数试图初始化这个新创建的对象，并将这个对象用做其调用上下文，构造函数的this关键字引用这个新创建的对象。构造函数通常不使用return关键字。

*   通过它们的call()和apply()方法间接调用

<h4>四、函数的实参和形参</h4>

<pre>
    // 可选参数
    function getPropertyNames(o, /*optional*/ a) {
      var property;
      a = a || [];
      for (property in o) {
        a.push(property);
      }
      return a;
    }
    var a = getPropertyNames(o);
    getPropertyNames(P, a);
</pre>

<pre>
    // 可变长实参列表（实参对象）
    function f(x, y, z) {
      if (arguments.length != 3) {
        throw new Error("function f called with " + arguments.length +
    			"arguments, but it expects 3 arguments.");
      }
    }
    
    function max() {
      var max = Number.NEGATIVE_INFINITY,
    	  i,
    	  argsLen = arguments.length;
      for (i = 0; i < argsLen; i += 1) {
        if (arguments[i] > max) {
    	  max = arguments[i];
    	}
      }
      return max;
    }
    var largest = max(1, 10, 100, 2, 3, 1000, 4, 5, 10000, 6);
</pre>
arguments并不是真正的数组，它是一个实参对象。以数字为索引的一组元素以及length属性。

<pre>
    function f(x) {
      console.log(x);
      arguments[0] = null;
      console.log(x);          // null
    }
</pre>

calle和caller属性

1.   ECMAScript标准规范规定calle属性指代当前正在执行的函数。
2.   caller是非标准的，大多数浏览器都实现了这个属性，指代调用当前正在执行的函数的函数。通过caller属性可以访问调用栈。

<pre>
    var factorial = function (x) {
	  if (x <= 1) {
	    return 1;
	  }
	  return x * arguments.callee(x - 1);
	}
</pre>

将对象属性用作实参。

<pre>
    function arraycopy(/* array */ from, /* index */ from_start, /* array */ to, /* index */ to_start, /* integer */ length) {
    }
    function easecopy(args) {
      arraycopy(args.from, args.from_start || 0, args.to, args.to_start || 0, args.length);
    }
    var a = [1, 2, 3, 4], b = [];
    easycopy({from: a, to: b, length: 4});
</pre>

实参类型检查

<pre>
    function sum(a) {
      if (isArrayLike(a) ) {
        var total = 0,
    		i = 0,
    		element,
    		len = a.length;
    	while (i < len) {
    	  element = a[i];
    	  if (element == null) {
    	    continue;
    	  }
    	  if (isFinite(element) ) {
    	    total += element;
    	  } else {
    	    throw new Error("sum(): elements must be finite numbers");
    	  }
    	  i += 1;
    	}
    	return total;
      } else {
        throw new Error("sum(): argument must be array-like");
      }
    }
</pre>

<pre>
    function flexisum(a) {
      var total = 0,
    	  i = 0,
    	  len = arguments.length,
    	  element,
		  n;
      while (i < len) {
        element = arguments[i];
    	if (element == null) {
    	  continue;
    	}
    	if (isArray(element) ) {
    	  n = flexisum.apply(this, element);
    	} else if (typeof element === "function") {
    	  n = Number(element() );
    	} else {
    	  n = Number(element);
    	}
    	if (isNaN(n) ) {
    	  throw Error("flexisum(): can't convert " + element + " to number");
    	}
    	total += n;
      }
      return total;
    }
</pre>
<h4>五、作为值的函数</h4>

*   赋值给变量

<pre>
    function square(x) {
      return x * x;
    }
    var s = square;
    square(4);
    s(4);
</pre>

*   作为对象的属性

<pre>
    var o = {square: function (x) { 
    	return x * x; 
    } };
    var y = o.square(16);
</pre>

*   赋值给数组元素

<pre>
    var a = [function (x) { return x * x; }, 20];
    a[0](a[1]);
</pre>

<pre>

    function add(x, y) {
      return x + y;
    }

    function subtract(x, y) {
      return x - y;
    }

    function multiply(x, y) {
      return x * y;
    }

    function divide(x, y) {
      return x / y;
    }

    function operate(operator, operand1, operand2) {
      return operator(operand1, operand2);
    }

    var i = operate(add, operate(add, 2, 3), operate(multiply, 4, 5));
    
    var operators = {
      add: function (x, y) {
    	     return x + y;
    	   },
      subtract: function (x, y) {
    			  return x - y;
    			},
      multiply: function (x, y) {
    			  return x * y;
    			},
      divide: function (x, y) {
    		    return x / y;
    		  },
      pow: Math.pow
    };

    function operate2(operation, operand1, operand2) {
      if (typeof operators[operation] === "function") {
        return operators[operation](operand1, operand2);
      } else {
	    throw "unknown operator";
	  }
    }
    
    var j = operate2("add", "hello", operate2("add", " ", "world") );

	var k = operate2("pow", 10, 2);

</pre>

<h4>六、自定义函数的属性</h4>

<pre>
    // 由于函数声明被提前，因此这里可以在函数声明之前给它的成员赋值。
    
    uniqueInteger.counter = 0;
    
    function uniqueInteger() {
      return uniqueInteger.counter += 1;
    }
</pre>

<pre>
    function factorial(n) {
      if (isFinite(n) && n > 0 && n == Math.round(n) ) {
        if (!(n in factorial) ) {
          factorial[n] = n * factorial(n - 1);
        }
        return factorial[n];
      } else {
        return NaN;
      }
    }
    factorial[1] = 1; //将函数自身当作数组对待。
    
    function factorial2(n) {
      var stackArray = [],
    	  sum = 1;
      if (isFinite(n) && n > 0 && n == Math.round(n) ) {
    	while (n > 0 || stackArray.length > 0 ) {
    	  if (n > 0) {
    	    stackArray.push(n);
    		n -= 1;
    	  } else {
    	    sum = sum * stackArray.pop();
    	  }
    	}
    	return sum;
      } else {
        return NaN;
      }
    }
</pre>

<h4>七、作为命名空间的函数</h4>

<pre>
    function mymodule() {
      // 模块代码
      // 局部变量
    }
    
    mymodule();

	// 立即执行函数。
	(function () {
	   // 模块代码
	 } ());
</pre>

<pre>
    var extend = (function () {
      var p;
      for (p in {toString: null}) {
        return function extend(o) {
    	  var i = 1,
    	      len = arguments.length,
    		  source,
    		  prop;
    	  while (i < len) {
    	    source = arguments[i];
    		for (prop in source) {
    		  o[prop] = source[prop];
    		}
    	  }
    	  return o;
    	}
      }
    
      return function patched_extend(o) {
        var i = 0,
    	    j = 0,
    	    len = arguments.length,
    		propLen = protoprops.length,
    		source,
    		prop;
        while (i < len) {
    	  source = arguments[i];
    	  for (prop in source) {
    	    o[prop] = source[prop];
    	  }
    	  while (j < propLen) {
    	    prop = protoprops[j];
    		if (souce.hasOwnProperty(prop) ) {
    		  o[prop] = source[prop];
    		}
    	  }
    	}
    	return o;
      };
    
      var protoprops = [ "toString", "valueOf", "constructor", "hasOwnProperty",
    	  "isPrototypeOf", "propertyIsEnumerable", "toLocaleString" ];
    
    } ());

</pre>

