---
layout: post
category: javascript
title: javascript权威指南-数组
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、Javascript数组</h4>
Javascript数组是无类型的：数组元素可以是任意类型，同一个数组中的不同元素可能有不同的类型，数组的元素可能是对象或其他数组。数组的实现是经过优化的，用数字索引来访问数组一般来说比访问常规对象属性要快很多。

数组继承自Array.prototype中的属性。

<h4>二、创建数组</h4>
数组直接量。

<pre>
    var empty = [];
    var primes = [2, 3, 5, 7, 11];
    var misc = [1.1, true, "a", ];
    
    var base = 1024;
    var table = [base, base + 1, base + 2, base + 3];
    
    var b = [[1, {x: 1, y: 2}], [2, {x: 3, y: 4}]];
    
    var count = [1, ,3];
    var undefs = [,,] // 2个undefined元素
</pre>

调用构造函数Array()创建数组。

<pre>
    var a = new Array();
    var a = new Array(10);
    var a = new Array(5, 4, 3, 2, 1, "testing, testing");
</pre>

<h4>三、数组元素的读和写</h4>

<pre>
    var a = ["world"];
    var value = a[0];
    a[1] = 3.14;
    i = 2;
    a[i] = 3;
    a[i + 1] = "hello";
    a[a[i]] = a[0];
</pre>

数组是对象的特殊形式。使用方括号访问数组元素就像用方括号访问对象的属性一样。Javascript将指定的数字索引值转换成字符串（索引值1变成“1”）然后将其作为属性名来使用。

索引值从数字转换为字符串，常规对象也会这么做。

<pre>
    var o = {};
    o[1] = "one";
</pre>

<pre>
    a[-1.23] = true; //这将创建一个名为“-1.23”的属性
    a["1000"] = 0;   //数组的第1001个元素
    a[1.000]         //和a[1]相等
</pre>

数组索引仅仅是对象属性名的一种特殊类型，Javascript数组没有“越界”错误的概念。当试图查询任何对象中不存在的属性时，不会报错，只会得到undefined值。类似于对象。

<h4>四、稀疏数组</h4>
稀疏数组是包含从0开始不连续索引的数组。通常，数组的length属性值代表数组中元素的个数。如果数组是稀疏的，length属性值大于元素的个数。

<pre>
    var a = new Array(5);
    var a = [];
    a[1000] = 0;
</pre>
当在数组直接量中省略值时不会创建稀疏数组。省略的元素是undefined。

<pre>
    var a1 = [,,,];
    var a2 = new Array(3);
    0 in a1                 // true
    0 in a2                 // false
</pre>

<pre>
    var a1 = [,];
    var a2 = [undefined];
    0 in a1;
    0 in a2;
</pre>

<h4>五、数组长度</h4>
length属性代表元素的个数或者最大索引值加1。

<pre>
    [].length;
    ['a', 'b', 'c'].length;
    a = [1, 2, 3, 4, 5];
    a.length = 3;
    a.length = 0;
    a.length = 5;
</pre>

<pre>
    a = [1, 2, 3];
    Object.defineProperty(a, "length", {writable: false});
    a.length = 0;     // a不会改变
</pre>

<h4>六、数组元素的添加和删除</h4>
<pre>
    var a = [];
    a[0] = "zero";
    a[1] = "one";

	var a = [];
	a.push("zero");
	a.push("one", "two"); // ["zero", "one", "two"]
	a.unshift("-one");    // ["-one", "zero", "one", "two"]
</pre>

<pre>
    var a = [1, 2, 3];
    delete a[1];
    1 in a               // false：数组索引1并未在数组中定义
    a.length             // 3：delete操作不影响数组长度
</pre>

<pre>
    var a = [1, 2, 3]
    a.push(4);
    a.pop();          // 4
    a.unshift(-1);
    a.shift();        // -1
	
	a.splice();
</pre>

<h4>七、数组遍历</h4>
for循环

<pre>
    var keys = Object.keys(o);  // 获得o对象属性名组成的数组
    var values = [];
    var i,
	    len = keys.length;
    for (i = 0; i < len; i += 1) {
	  if (!a[i]) {
	    continue;
	  }
      var key = keys[i];
      values[i] = o[key];
    }

</pre>

for/in循环处理稀疏数组

<pre>
var index,
	value;
for (index in sparseArray) {
  if (!a.hasOwnProperty(i) ) {
    continue;
  }
  value = sparseArray[index]
}

for (index in sparseArray) {
  if (String(Math.floor(Math.abs(Number(i) ) ) ) === i ) {
    // coding 
  }
}
</pre>

for/in循环以不同的顺序遍历对象的属性。通常数组元素的遍历实现是升序的，但不能保证一定是这样。如果数组同时拥有对象属性和数组元素，返回的属性名很可能是按照创建的顺序而非数值大小顺序。

<pre>
    var data = [1, 2, 3, 4, 5];
    var sumOfSquares = 0;
    data.forEach(function (x) {
      sumOfSquares += x * x;
    });
    sumOfSquares;
</pre>

<h4>八、多维数组</h4>

<pre>
    var table = new Array(10);
    var i,
    	tableLen = talbe.length,
    	row,
    	rowLen,
    	col;
    for (i = 0; i < table.length; i += 1) {
      table[i] = new Array(10);
    }
    
    for (row = 0; row < tableLen; row += 1) {
      for (col = 0, rowLen = table[row].length; col < rowLen; col += 1) {
        table[row][col] = row * col;
      }
    }
    var product = table[5][7];
</pre>

<h4>九、数组方法</h4>

<pre>
    //Array.join()方法是String.split()方法的逆向操作
    var a = [1, 2, 3];
    a.join();
    a.join(" ");
    a.join("");
    var b = new Array(10);
    b.join('-');
</pre>

<pre>
    //Array.reverse()
    var a = [1, 2, 3];
    a.reverse().join()  // "3,2,1"; a: [3, 2, 1]
</pre>

<pre>
    //Array.sort()
    var a = new Array("banana", "cherry", "apple");
    a.sort();
    var s = a.join(', '); // "apple banana cherry"

	var a = [33, 4, 1111, 222];
	a.sort();
	a.sort(function (a, b) {
	  return a - b;
	});
    a.sort(function (a, b) {
	  return b -a; 
	});
    
    a = ['ant', 'Bug', 'cat', 'Dog'];
	a.sort();
	a.sort(function (s, t) {
	  var a = s.toLowerCase(),
	      b = t.toLowerCase();
	  if (a < b) {
	    return -1;
	  }
	  if (a > b) {
	    return 1;
	  }
	  return 0;
	});
</pre>

<pre>
    //Array.concat
    var a = [1, 2, 3];
    a.concat(4, 5);
    a.concat([4, 5]);
    a.concat([4, 5], [6, 7]);
    a.concat(4, [5, [6, 7] ]);
</pre>

<pre>
    //Array.slice()
    var a = [1, 2, 3, 4, 5];
    a.slice(0, 3);              // 返回[1, 2, 3]
    a.slice(3);                 // 返回[4, 5]
    a.slice(1, -1);             // 返回[2, 3, 4]
    a.slice(-3, -2);            // 返回[3]
</pre>

<pre>
    //Array.splice()：数组插入或删除元素的通用方法。
    var a = [1, 2, 3, 4, 5, 6, 7, 8];
    a.splice(4);                        // 返回[5, 6, 7, 8]；a 是[1, 2, 3, 4]
    a.splice(1, 2);                     // 返回[2, 3]; a 是[1, 4]
    a.splice(1, 1);                     // 返回[4]；a 是[1]
    a.splice(0, 1);                     // 返回[1]; a 是[]
	a.splice(0, 1);                     // 返回[]; a 是[]
</pre>

<pre>
    // Array.push() Array.pop() 
    var stack = [];             // stack：[]
    stack.push(1, 2);           // stack：[1, 2]       返回 2
    stack.pop();                // stack：[1]          返回 2
    stack.push(3);              // stack: [1, 3]       返回 2
    stack.pop();                // stack：[1]          返回 3
    stack.push([4, 5]);         // stack：[1, [4, 5]]  返回 2
    stack.pop();                // stack：[1]          返回 [4, 5]
    stack.pop();                // stack：[]           返回 1
</pre>

<pre>
    // Array.unshift() Array.shift()
    var a = [];                       // a：[]
    a.unshift(1);                     // a：[1]            返回：1
    a.unshift(22);                    // a：[22, 1]        返回：2
    a.shift();                        // a：[1]            返回：22
    a.unshift(3, [4, 5]);             // a：[3, [4, 5], 1] 返回：3
    a.shift();                        // a：[[4, 5], 1]    返回：3
    a.shift();                        // a：[1]            返回：[4, 5]
    a.shift();                        // a：[]             返回：1
</pre>

<pre>
    //Array.toString()和Array.toLocaleString()
    [1, 2, 3].toString();                         // '1,2,3'
    ["a", "b", "c"].toString();                   // 'a,b,c'
    [1, [2, 'c'] ].toString();                    // '1,2,c'
</pre>

<h4>十、ECMAScript 5中的数组方法</h4>

<pre>
    // forEach()
    var data = [1, 2, 3, 4, 5];
    var sum = 0;
    data.forEach(function (value) { sum += value; });
    sum;
    data.forEach(function (v, i, a) { a[i] = v + 1; } );
    data;

	function foreach(a, f, t) {
	  try { 
		a.forEach(f, t); 
	  } catch (e) {
	    if () {
		  return;
		} else {
		  throw e;
		}
	  }
	}
	foreach.break = new Error("StopIteration");
</pre>

<pre>
    // Array.map()
    var a = [1, 2, 3];
    b = a.map(function (x) { 
    		return x * x; 
    	});
</pre>

<pre>
    // Array.filter()
    var a = [5, 4, 3, 2, 1];
    smallvalues = a.filter(function (x) {
    		  return x < 3;
    		});
    everyother = a.filter(function (x, i) {
    		  return i % 2 == 0;
    		});

    var dense = sparse.filter(functiin () {
    		  return true;
    		});
    a = a.filter(function (x) {
    		return x !== undefined && x != null;
    		});
</pre>

<pre>
    // Array.every()
    var a = [1, 2, 3, 4, 5];
    a.every(function (x) {
    		  return x < 10;
    		});
    a.every(function (x) {
    		  return x % 2 === 0;
    		});
</pre>

<pre>
    // Array.some()
    var a = [1, 2, 3, 4, 5];
    a.some(function (x) {
    		  return x % 2 === 0;
    		});
    a.some(isNaN);
</pre>

<pre>
    // Array.reduce()
    var a = [1, 2, 3, 4, 5];
    var sum = a.reduce(function (x, y) { 
    		  return x + y 
    		}, 0);
    var product = a.reduce(function (x, y) {
    		  return x * y;
    		}, 1);
    var max = a.reduce(function (x, y) {
    		  return x > y ? x : y
    		});
</pre>

<pre>
    //Array.reduceRight()
    var a = [2, 3, 4];
	var big = a.reduceRight(function (accumulator, value) {
			  return Math.pow(value, accumulator);
			});
</pre>

<pre>
    // Array.reduceRight()
    function union(o, p) {
      return extend(extend({}, o), p);
    }
    var objects = [{x: 1}, {y: 2}, {z: 3}];
    var merged = objects.reduce(union);            // {x: 1, y: 2, z: 3}
    
    var objects = [{x: 1, a: 1}, {y: 2, a: 2}, {z: 3, a: 3}];
    var leftunion = objects.reduce(union);         // {x: 1, y: 2, z: 3, a: 1}
    var rightunion = objects.reduceRight(union);   // {x: 1, y: 2, z: 3, a: 3}
</pre>

<pre>
    var a = [0, 1, 2, 1, 0];
    a.indexOf(1);                  // 1
    a.lastIndexf(1);               // 3
    a.indexOf(3);                  // -1 没有值为3的元素
</pre>

<pre>
    function findall(a, x) {
      var results = [],
    	  len = a.length,
    	  pos = 0;
      while (pos < len) {
        pos = a.indexOf(x, pos);
    	if (pos === -1) {
    	  break;
    	}
    	results.push(pos);
    	pos = pos + 1;
      }
    }
</pre>

<h4>十一、数组类型</h4>

<pre>
    Array.isArray([]);  // true
    Array.isArray({});  // false
</pre>

<pre>
    [] instanceof Array     // true
    ({}) instanceof Array   // false

	//当多窗口或窗体存在时 instanceof操作符检测无效

	var isArray = Function.isArray || function (o) {
	  return typeof o === "object" && Object.prototype.toString.call(o) === "[object Array]";
	};

</pre>

<h4>十二、类数组对象</h4>
数组的特性。

*   当有新的元素添加到列表中时，自动更新length属性。
*   设置length为一个较小值将截断数组。
*   从Array.prototype中继承一些有用的方法。
*   其类属性为“Array”。

这些特性让Javascript数组和常规对象有明显的区别。但是它们不是定义数组的本质特性。一种常常完全合理的看法把拥有一个数值length属性和对应非负整数属性的对象看作一种类型的数组（类数组）。

<pre>
    var a = {};
    var i = 0;
    while (i < 10) {
      a[i] = i * i;
      i += 1;
    }
    a.length = i;
    var total = 0,
    	j;
    for (j = 0; j < a.length; j += 1) {
      total += a[j];
    }
</pre>

<pre>
    function isArrayLike(o) {
      if (o && typeof o === "object" && isFinite(o.length) && o.length >= 0 && o.length === Math.floor(o.length) && o.length < 4294967296 ) {
        return true;
      } else {
        return false;
      }
    }
</pre>

<pre>
    var a = {"0": "a", "1": "b", "2": "c", length: 3};
    Array.prototype.join.call(a, "+");
    Array.prototype.slice.call(a, 0);
    Array.prototype.map.call(a, function () {
    		  return x.toUpperCase();
    		});
</pre>

<pre>
    Array.join = Array.join || function (a, sep) {
      return Array.prototype.slice.call(a, from, to);
    };
    Array.slice = Array.slice || function (a, from, to) {
      return Array.prototype.slice.call(a, from, to);
    };
    Array.map = Array.map || function (a, f, thisArg) {
      return Array.prototype.map.call(a, f, thisArg);
    };
</pre>

<h4>十三、作为数组的字符串</h4>
<pre>
    var s = "test";
    s.chartAt(0);
    s[1];
</pre>

<pre>
    var s = "JavaScript";
    Array.prototype.join.call(s, " ");
    Array.prototype.filter.call(s, function (x) {
    		  return x.match(/[^aeiou]/);
    		}).join("");
</pre>

字符串是不可变值，故当他们作为数组看待时，它们是只读的。如push(), sort(), reverse(), splice() 等数组方法会修改数组，它们在字符串上是无效的。不仅如此，使用数组方法修改字符串会导致错误：出错的时候没有提示。
