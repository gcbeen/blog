---
layout: post
category: javascript
title: javascript权威指南-函数-下
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、使用函数处理数组</h4>

<pre>
    // 非函数式编程
    var data = [1, 1, 3, 5, 5],
    	len = data.length,
        total = 0,
    	mean,
    	deviation,
    	stddev,
    	i;
    for (i = 0; i < len; i += 1) {
      total += data[i];
    }
    mean = total / len;
    
    total = 0;
    for (i = 0; i < len; i += 1) {
      deviation = data[i] - mean;
      total += deviation * deviation;
    }
    stddev = Math.sqrt(total / (len - 1) );
</pre>

<pre>
    // 函数式编程
    var sum = function (x, y) {
      retur x + y;
    };
    var square = function (x) {
      return x * x;
    };
    var data = [1, 1, 3, 5, 5];
    var mean = data.reduce(sum) / data.length;
    var deviations = data.map(function (x) { return x - mean; } );
    var stddev = Math.sqrt(deviations.map(square).reduce(sum) / (data.length - 1) );
</pre>

<pre>
    // 基于ECMAScript 3的函数式编程。
    var map = Array.prototype.map ? function (a, f) { return a.map(f); } : implementMap;

	function implementMap(a, f) {
      var results = [],
    	  i,
    	  len = a.length;
      for (i = 0; i < len; i += 1) {
        if (i in a) {
    	  results[i] = f.call(null, a[i], i, a);
    	}
      }
      return results;
    }
    var reduce = Array.prototype.reduce ? function (a, f, initial) {
      if (arguments.length > 2) {
        return a.reduce(f, initial);
      } else {
        return a.reduce(f);
      }
    } : implementReduce;

    function implementReduce(a, f, initial) {
      var i = 0,
    	  len = a.length,
    	  accumulator;
      if (arguments.length > 2) {
        accumulator = initial;
      } else {
        if (len == 0) {
    	  throw TypeError();
    	}
    	for (i = 0; i < len; i += 1) {
    	  if (i in a) {
    	    accumulator = a[i];
    		break;
    	  }
    	}
    	if (i == len) {
    	  throw TypeError();
    	}
      }
    
      while (i < len) {
        if (i in a) {
    	  accumulator = f.call(undefined, accumulator, a[i], i, a);
    	}
    	i += 1;
      }
      return accumulator;
    };
    
    var data = [1, 1, 3, 5, 5];
	var len = data.length;
    var sum = function (x, y) { return x + y; };
    var square = function (x) { return x * x; };
    var mean = reduce(data, sum)/ len;
    var deviations = map(data, function (x) { return x - mean; });
    var stddev = Math.sqrt(reduce(map(deviations, square), sum) / (len - 1));
</pre>

<h4>二、高阶函数</h4>
高阶函数就是操作函数的函数。
<pre>
    function not(f) {
      return function () {
        var result = f.apply(this, arguments);
    	return !result;
      };
    }
    
    var even = function (x) {
      return x % 2 === 0;
    }
    var odd = not(even);
    [1, 2, 3, 5, 5].every(odd);
</pre>

<pre>
    function mapper(f) {
      return function (a) {
    	  return map(a, f);
      };
    }
    var increment = function (x) {
    	return x + 1;
    };
    var incrementer = mapper(increment);
    incrementer([1, 2, 3]);
</pre>

<pre>
    function compose(f, g) {
      return f.call(this, g.apply(this, arguments) );
    }
    var square = function (x) { return x * x; };
    var sum = function (x, y) { return x + y; };
    var squareofsum = compose(square, sum);
    squareofsum(2, 3);
</pre>

<h4>三、不完全函数</h4>

<pre>

    function array(a, n) {
      return Array.prototype.slice.call(a, n || 0);
    }
    
    // 将函数的实参传递至左侧。
    function partialLeft(f /* ,.... */) {
      var args = arguments;
      return function () {
        var a = array(args, 1);
    	a = a.concat(array(arguments) );
    	return f.apply(this, a);
      };
    }
    
    // 将函数的实参传递至右侧。
    function partialRight(f /*, ... */) {
      var args = arguments;
      return function () {
        var a = array(arguments);
    	a = a.concat(array(args, 1) );
    	return f.apply(this, a);
      };
    }
    
    function partial(f /*, ... */) {
      var args = arguments;
      return function () {
        var a = array(args, 1);
    	var i = 0, j = 0;
    	for (; i < a.length; i += 1) {
    	  if (a[i] == undefined) {
    	    a[i] = arguments[j];
    		j += 1;
    	  }
    	}
    	a = a.concat(array(arguments, j) );
    	return f.apply(this, a);
      };
    }
    
    var f = function (x, y, z) {
      return x * (y -z);
    }
    
    partialLeft(f, 2)(3, 4);
    partialRight(f, 2)(3, 4);
    partial(f, undefined, 2)(3, 4);

</pre>

<pre>
    
    var increment = partialLeft(sum, 1);
    var cuberoot = partialRight(Math.pow, 1/3);
    String.prototype.first = partial(String.prototype.charAt, 0);
    String.prototype.last = partial(String.prototype.substr, -1, 1);
    
</pre>

<pre>
    
    function compose(f, g) {
	  return function () {
	    return f.call(this, g.apply(this, arguments) );
	  };
	}
    
    var not = partialLeft(compose, function (x) { return !x; });
    var even = function (x) {
      return x % 2 === 0;
    };
    var odd = not(even);
    var isNumber = not(isNaN);
    
</pre>

<pre>
    var data = [1, 1, 3, 5, 5];
    var sum = function (x, y) { return x + y; };
    var product = function (x, y) { return x * y; };
    var neg = partial(product, -1);
    var square = partial(Math.pow, undefined, 2);
    var sqrt = partial(Math.pow, undefined, 0.5);
    var reciprocal = partial(Math.pow, undefined, -1);
    
    var mean = product(reduce(data, sum), reciprocal(data.length) );
    
	/*
	var stddev = sqrt(product( 
				reduce(map(data, compose(square, partial(sum, neg(mean)))), sum),
				reciprocal(sum(data.length, -1))
				));
	*/

	var composeFunc = compose(square, partial(sum, neg(mean) ) );
    var product1 = reduce(map(data, composeFunc ), sum );
	var product2 = reciprocal(sum(data.length, -1) );
	var stddev = sqrt(product(product1, product2));
</pre>

<h4>四、记忆</h4>

<pre>
    // 返回带有记忆功能的函数
    function memorize(f) {
      var cache = {};
      return function () {
        var key = arguments.length + Array.prototype.join.call(arguments, ",");
    	  return cache[key] || cache[key] = f.apply(this, arguments);
      };
    }
    
</pre>

<pre>
    
    function gcd(a, b) {
      var t;
      if (a < b) {
        t = b, b = a, a  = t;
      }
      while (b != 0) {
        t = b, b = a % b, a = t;
      }
      return a;
    }
    
    var gcdmemo = memorize(gcd);
    gcdmemo(85, 187);
    
	// 有记忆功能的递归函数
	var factorial = memorize( function (n) { return (n <= 1) ? 1 : n * factorial(n-1); });
	factorial(5);
    
</pre>

