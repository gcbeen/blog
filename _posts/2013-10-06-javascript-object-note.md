---
layout: post
category: javascript
title: javascript权威指南-对象
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}
对象是Javascript的基本类型。除了字符串、数字、true、false、null和undefined之外，Javascript中的值都是对象。尽管字符串、数字和布尔值不是对象，但它们的行为和不可变对象非常相似。

对象最常见的用法：创建(create)、设置(set)、查找(query)、删除(delete)、检测(test)和枚举(enumerate)它的属性。

属性的特性。

*   可写（writable attribute）,表明是否可以设置该属性的值。
*   可枚举（enumerable attribute），表明是否可以通过for/in循环返回该属性。
*   可配置（configurable attribute），表明是否可以删除或修改该属性。

对象的三个相关联的特性。

*   对象的原型（prototype）指向另外一个对象，本对象的属性继承自它的原型对象。
*   对象的类（class）是一个标识对象类型的字符串。
*   对象的扩展标记（extensible flag）指明了是否可以向该对象添加属性。

三类对象。

*   内置对象，由ECMAScript规范定义的对象或类。例如，数组、函数、日期、和正则表达式都是内置对象。
*   宿主对象，由Javascript解释器所嵌入的宿主环境（比如Web浏览器）定义的。客户端Javascript中表示网页结构的HTMLElement对象均是宿主对象。既然宿主环境定义的方法可以当成普通的Javascript函数对象，那么宿主对象也可以当成内置对象。
*   自定义对象，运行中由Javascript代码所创建的对象。

两类属性

*   自有属性，直接在对象中定义的属性。
*   继承自属性，在对象的原型对象中定义的属性。

<h4>一、创建对象</h4>
创建对象的方式。

*   对象直接量

<pre>
    var empty = {};
    var point = { x: 0, y: 0};
    var point2 = { x: point.x, y: point.y + 1};
    var book = {
    	"main title": "javascript",
    	"sub-title": "The Definitive Guide",
    	"for": "all audiences",
    	author: {
            firstname: "David",
    		surname: "Flanagan"
    	}
    };
</pre>
如果在一个重复调用的函数中的循环体内使用了对象直接量，它将创建很多新对象。

*   关键字new

<pre>
    var o = new Object(); //创建一个空对象，和{}一样。
    var a = new Array();
    var d = new Date();
    var r = new RegExp("js");
</pre>

*   object.create()函数 

第一个参数是这个对象的原型。第二个参数是对属性进行描述，可选参数。

<pre>
    var o1 = Object.create({x:1, y:2}); // o1继承了属性x和y
	var o2 = Object.create(null);  // o2不继承任何属性和方法 不包括基础方法。比如toString(), “+”运算符
	var o3 = Object.create(Object.prototype); //o3和{}和new Object()一样
</pre>

可以通过任意的原型创建对象（换句话说，可以使任意对象可继承）。

原型

每个Javascript对象（null除外）都和另一个对象（原型）相关联。每一个对象都从原型继承属性。

*   所有通过对象直接量创建的对象都具有同一个原型对象，并可以通过Javascript代码Object.prototype获得对原型对象的引用。
*   通过关键字new和构造函数调用创建的对象的原型就是构造函数的prototype属性的值。

<pre>
    {}, new Object() 原型 Object.prototype
    new Array()      原型 Array.prototype
    new Date()       原型 Date.prototype
</pre>
没有原型的对象不多，Object.prototype就是其中之一。它继承任何属性。所有内置构造函数（以及大部分自定义构造函数）都具有一个继承自Object.prototype的原型。

模拟原型继承

<pre>
    function inherit(p) {
      if (p == null) {
        throw TypeError();
      }
      if (Object.create) {
        return Object.create(p);
      }
      var t = typeof p;
      if (t !== "object" && t !== "function") {
        throw TypeError();
      }
      function F() {
      }
      F.prototype = p;
      return new F();
    }
</pre>
inherit()函数的其中一个用途就是防止库函数无意间修改那些不受你控制的对象。不是将对象直接作为参数传入函数，而是将它的继承对象传入函数。库函数只会操作继承对象的属性，不会影响原始对象。

<pre>
    var o = { x: "don't change this value"};
    library_function(inherit(o)); //防止对o的意外修改
</pre>

<h4>二、属性的查询和设置</h4>

<pre>
    var author = book.author;
    var name = author.surname;
    var title = book["main title"];
    
    book.edition = 6;
    book["main title"] = "ECMScript";
</pre>

<pre>
    object.property
    object["property"]
</pre>
表达式的值相同。

<pre>
    var addr = "",
    	index = 0;
    while (index < 4) {
      addr += customer["address" + (index)] + '\n';
      index += 1;
    } 
</pre>
数组写法和用字符串表达式来访问对象属性更灵活。

给portfolio添加新的股票

<pre>
    function addstock(protfolio, stockname, shares) {
      portfolio[stockname] = shares;
    }
</pre>

数组写法在for-in中的运用

<pre>
    function getvalue(portfolio) {
      var total = 0.0;
      for (stock in portfolio) {
        var shares = portfolio[stock];
    	var price = getquote(stock);
    	total += shares * price;
      }
      return total;
    }
</pre>

<h4>三、继承</h4>
Javascript对象具有“自由属性”，也有从原型对象继承而来的属性。

假设要查询对象o的属性x，如果o中不存在x，那么将会继续在o的原型对象中查询属性x，如果原型对象中也没有x，但这个原型对象也有原型，那么继续在这个原型对象的原型上执行查询，直到找到x或者找到一个原型是null的对象为止。

<pre>
    var o = {};
    o.x = 1;
    var p = inherit(o);
    p.y = 2;
    var q = inherit(p);
    q.z = 3;
    var s = q.toString();
    q.x + q.y //3
</pre>
假设给对象o的属性赋值，如果o中已经有属性x，那么这个赋值操作只改变这个已有属性x的值。如果o中不存在属性x，那么赋值操作给o添加一个新属性x。如果之前o继承自属性x，那么这个继承的属性就会被新创建的同名属性覆盖了。

属性的赋值操作首先检查原型链，以此判断是否允许赋值操作。例如如果o继承自一个只读属性x，那么赋值操作是不允许的。如果允许属性赋值操作，他也总是在原始对象上创建属性或已有的属性赋值，而不会去修改原型链。

在Javascript中，只有在查询属性时才会体会到继承的存在，而设置属性则和继承无关。

<pre>
    var unitcircle = { r:1 };
    var c = inherit(unitcircle);
    c.x = 1;
    c.y = 1;
    c.r = 2;
    unitcircle.r;
</pre>

属性赋值要么失败，要么创建一个属性，要么在原始对象中设置属性，但如果o继承自属性x，而这个属性是一个具有setter方法和accessor属性。那么这是o将调用setter方法而不是给o创建一个属性x，并且这个操作指针对o本身，并不会修改原型链。

属性访问错误

如果在对象o自身的属性或继承的属性中均未找到属性x，属性访问表达式o.x返回undefined。

<pre>
    book.subtitle; // undefined
</pre>

<pre>
    var len = book.subtitle.length; //抛出异常。
	var len = book && book.subtitle && book.subtitle.length;
</pre>

给null和undefined设置属性也会报类型错误。

给其他值设置属性也不总是成功，有一些属性是只读的，不能重新赋值，有一些对象不允许新增属性，但不会报错。

<pre>
   // 内置构造函数的原型是只读的
   Object.prototype = o;
</pre>

设置对象o的属性p会失败。

*   o中的属性p是只读的：不能给只读属性重新赋值（defineProperty()方法中有一个例外，可以对可配置的只读属性重新赋值）。
*   o中的属性p是继承属性，且它是只读的：不能通过同名自由属性覆盖只读的继承属性。
*   o中不存在自有属性p：o没有使用setter方法继承属性p，并且o的可扩展性为false。

<h4>四、删除属性</h4>
delete运算符可以删除对象的属性（自有属性）。只是断开属性和宿主对象的联系（引用）。

<pre>
    o = {x: 1};
    delete o.x; // true
    delete o.x; // 什么也没做（x已删除），返回true
    delete o.toString; // 什么也没做（toString是继承来的）返回true
    delete 1; // 无意义， 返回  true。
</pre>

不可删除的属性

<pre>
    delete Object.prototype;  // 不能删除，属性是不可配置的
    var x = 1;                
    delete this.x;            // 不能删除全局变量（属性）
    function f() {}
    delete this.f             // 不能删除全局函数
	this.x = 1;
	delete this.x;            // 可配置的全局属性（没有用var） 可以删除。
</pre>

<h4>五、属性检测</h4>

*   in运算符（自有属性和继承自属性）

<pre>
    var o = { x: 1};
    "x" in o;          // true   
    "y" in o;          // false
    "toString" in o;   // true 继承属性
</pre>

*   hasOwnPreperty() （自有属性）

<pre>
    var o = { x: 1};                
    o.hasOwnProperty("x");          // true
    o.hasOwnProperty("y");          // false
    o.haOwnProperty("toString");    // false 继承属性。
</pre>

*   propertyIsEnumerable() 检测自有可枚举的属性

<pre>
    var o = inherit({ y: 2});
    o.x = 1;
    o.propertyIsEnumerable("x");                        // true   
    o.propertyIsEnumerable("y");                        // false 继承来的
    Object.prototype.propertyIsEnumerable("toString");  // false 不可枚举
</pre>

*   属性查询

<pre>
    var o = { x: undefined };    
    o.x !== undefined;             // false
    o.y !== undefined;             // false
    
    "x" in o;                      // true
    "y" in o;                      // false
    delete o.x;                    
    "x" in o;                      //false
</pre>

“!==”运算符可以区分undefined和null。

<h4>六、枚举属性</h4>
for/in循环枚举对象的属性

对象继承的内置方法不可枚举，但在代码中给对象添加的属性都是可枚举的。

<pre>
   var o = { x: 1, y: 2, z: 3};
   o.propertyIsEnumerable("toString");
   for (p in o) {
     console.log(p);
   }
</pre>

<pre>
   for (p in o) {
     if (!o.hasOwnProperty(p)) {
       continue;
     }
   }
   
   for (p in o) {
     if (typeof o[p] === "function") {
       continue;
     }
   }
</pre>

<pre>
   function extend(o, p) {
     for (prop in p) {
       o[prop] = p[prop];;
     }
     return o;
   }
</pre>

<pre>
/* merge 属性 */
function merge(o, p) {
  for (prop in p) {
	if (o.hasOwnProperty[prop]) {
	  continue
	}
	o[prop] = p[prop];
  }
}
/* 删除o中属性，在p中没有的同名属性。交集。 */
function restrict(o, p) {
  for (prop in o) {
    if (! (prop in p)) {
	  delete o[prop];
	}
  }
  return o;
}
/* 取差集 */
function subtract(o, p) {
  for ( prop in p) {
    delete o[prop];
  }
  return o;
}
/* 取并集 */
function union(o, p) {
  return extend(extend({},o),p);
}
/* 取交集 */
function intersection(o, p) {
  return restrict(extend({},o),p);
}

function keys(o) {
  var result = [],
	  prop;
  if (typeof o !== "object") {
	  throw TypeError();
  }
  for (prop in o) {
	if (o.hasOwnProperty(prop)) {
	  result.push(prop);
	}
  }
  return result;
}
</pre>

ECMAScript 5定义了两个用以枚举属性名称的函数。

*   Object.keys()  返回一个数组，由对象中可枚举的自有属性的名称组成。
*   Object.getOwnPropertyNames()  返回对象中所有自有属性的名称。

<h4>七、属性getter和setter</h4>
存取器属性。当程序查询存取器属性的值时，Javascript调用getter方法（无参数）。这个方法的返回值就是属性存取表达式的值。当程序设置一个存取器属性的值时，Javascript调用setter方法，将赋值表达式右侧的值当作参数传入setter。从某种意义上讲，这个方法负责“设置”属性值。可以忽略setter方法的返回值。

数据属性不同，存取器属性不具有可写性。

<pre>
    var o = {
      data_prop: value,
      get accessor_prop() {},
      set accessor_prop() {}
    };
</pre>

<pre>
    var p = {
      x: 1.0,
      y: 1.0,
    
      get r() { return Math.sqrt(this.x * this.x + this.y * this.y); },
      set r(newValue) {
    	var oldValue = this.r;
    	var ratio = newValue/oldValue;
    	this.x *= ratio;
    	this.y *= ratio;
      },
      get theta() { return Math.atan2(this.y, this.x); }
    };
    var q = inherit(p);
    q.x = 1; q.y =1;
    console.log(q.r);
    console.log(q.theta);
</pre>

<pre>
    var serialnum = {
        
    	$n: 0,
    
        get next() {
    	  $n += 1;
    	  return $n;
    	},
    	
    	set next(n) {
    	  if (n >= this.$n) {
    		this.$n = n;
    	  } else {
    	    throw "序列号的值不能比当前值小";
    	  }
    	}
    }
</pre>

<pre>
    var random = {
        get octet() {
    	  return Math.floor(Math.random() * 256);
    	},
    	get uint16() {
    	  return Math.floor(Math.random() * 65536);
    	},
    	get int16() {
    	  return Math.floor(Math.random() * 65536) - 32768;
    	}
    };
</pre>

<h4>八、属性的特性</h4>
属性包含一些标识它们的特性（可写、可枚举、可配置）。

数据属性的四个特性（值、可写性、可枚举性、可配置性）。
存取器属性的四个特性（读取、写入、可枚举性、可配置性）。

属性特性的查询和设置操作，属性描述符对象代表那4个特性。

数据属性的描述符对象的属性有（value,writable,enumerable,configurable）。
存取器属性的描述符对象的属性（get，set，enumerable，configurable）。

调用Object.getOwnPropertyDescriptor()可以获得某个对象特定属性的属性描述符。

<pre>
   Object.getOwnPropertyDescriptor({x:1}, "x");
   Object.getOwnPropertyDescriptor(random, 'octet');
   Object.getOwnPropertyDescriptor({}, "x");           // undefined 没有这个属性
   Object.getOwnPropertyDescriptor({}, "toString");    // undefined 继承属性
</pre>

Object.getOwnPropertyDescriptor()只能得到自有属性的描述符。要想获得继承的特性，需要遍历原型链（getPrototypeOf()）。

要想设置属性的特性，或者想让新建属性具有某些特性，则需要调用Object.definePeoperty()，传入要修改的对象、要创建的对象或修改的属性的名称以及属性描述符对象。

<pre>
    var o = {};
    object.defineProperty(o, "x", { value: 1, writable: true, enumerable: false, configurable: true});
    
    o.x;
    Object.keys(o); // []
    
    Object.defineProperty(o, "x", {writable: false});
    o.x = 2;
    o.x; // 1
    
    Object.defineProperty(o, "x", { value: 2 });
    o.x; // 2
    
    Object.defineProperty(o, "x", { get: function() { return 0; } });
    o.x; // 0
</pre>

Object.defineProperty()的使用。

*   传入Object.defineProperty()的属性描述符对象不必包含所有4个特性。
*   对于新创建的属性来说，默认的特性值是false或undefined。对于修改的已有属性来说，默认的特性值没有做任何修改。
*   修改已有属性或者新建自有属性，但不能修改继承属性。

修改或创建多个属性。

<pre>
    var p = Object.defineProperties({},{
    	x: { value: 1, writable: true, enumerable: true, configurable: true},
    	y: { value: 1, writable: true, enumerable: true, configurable: true},
    	r: {
    	  get: function () { return Math.sqrt( this.x * this.x + this.y * this.y ) }, // 只读存取器属性。
    	  enumerable: true,
    	  configurable: true
    	}
    });
</pre>

对于那些不允许创建或者修改的属性来说，如果用Object.defineProperty() 和 Object.defineProperties()对其操作就会抛出类型错误异常。

Object.defineProperty()或Object.defineProperties()的使用规则。

*   如果对象是不可扩展的，则可以编辑已有的自有属性，但是不能给它添加属性。
*   如果属性是不可配置的，则不能修改它的可配置性和可枚举性。
*   如果存取器属性是不可配置的，则不能修改其getter和setter方法，也不能将它转化为数据属性。
*   如果数据属性是不可配置的，则不能将它转化为存取器属性。
*   如果数据属性是不可配置的，则不能将它的可写性从false修改为true，但可以从true修改为false。
*   如果数据属性是不可配置的，则不能修改它的值。然而可配置但不可写属性的值是可以修改的（实际上是先将它标记为可写，然后修改它的值，最后转换为不可写的）。

改进的extend()函数。

<pre>
    // 定义 Object.prototype.extend
    Object.defineProperty(Object.prototype, "extend", {
            writable: true,
    		enumerable: false,
    		configurable: true,
    		value: function(o) {
    		  var names = Object.getOwnPropertyNames(o),
    		      index,
    			  desc;
    		  for (index = 0; i < names.length; i += 1) {
    		    if (names[i] in this) {
    			  continue;
    			}
    			desc = Object.getOwnPropertyDescriptor(o, names[i]);
    			Object.defineProperty(this, names[i], desc);
    		  }
    		}
    	});
</pre>

<h4>九、getter和setter的老式API</h4>
对象直接量语法给对象定义存取器属性，但不能查询属性的getter和setter方法或给已有的对象添加新的存储器属性。在ECMAScript 5中，可以通过Object.getOwnPropertyDescriptor()和Object.defineProperty()来完成这些工作。

之前的非标准方法。

*   __lookupGetter__()和__lookupSetter__()用以返回一个命名属性的getter和setter方法。
*   __defineGetter__()和__defineSetter__()用以定义getter和setter，这两个函数的第一个参数是属性名字，第二个参数是getter和setter方法。

<h4>十、对象的三个属性</h4>

*   原型属性

对象的原型属性是用来继承属性的，简称对象的原型。原型属性实在实例创建之初就设置好的，通过直接量创建的对象使用Object.prototype作为它们的原型。通过new创建的对象使用构造函数的prototype属性作为它们的原型。通过Object.create()创建的对象使用第一个参数（也可以是null）作为它们的原型。

在ECMAScript 5中，将对象作为参数传入Object.getPrototypeOf()可以查询它的原型。在ECMAScript 3中，则没有与之相等的函数，经常使用表达式o.constructor.prototype来检测一个对象的原型。

1.   通过new关键字和构造函数创建的对象，通常继承一个constructor属性，这个属性指代创建一个对象的构造函数。constructor.prototype是对象的原型。
2.   通过对象直接量创建的对象包含一个名为constructor属性，这个属性指代Object()构造函数。constructor.prototype（Object.prototype）是对象直接量的原型。
3.   通过Object.create(对象原型)创建的对象也包含一个constructor属性，指代Object()构造函数。constructor.prototype不是这个对象的原型。

检测一个对象是否是另一个对象的原型（isPrototypeOf()方法）。

<pre>
    var p = {x: 1};
    var o = Object.create(p);
    p.isPrototype(o);
    Object.prototype.isProtypeOf(o);
</pre>

Mozilla实现的Javascript对外暴露了一个专门命名为__proto__的属性，用以直接查询/设置对象的原型。但不推荐使用。

*   类属性

对象的类属性是一个字符串，用以表示对象的类型信息。只有通过一种间接的方法可以查询它。默认的toString()方法（继承自Object.prototype）返回了一个如下格式的字符串。

<pre>
    [object class]
</pre>
因此，要获得对象的类，可以调用对象的toString()方法，然后提取已返回字符串的低8个到倒数第二个位置之间的字符。不过让人棘手的是，很多对象继承的toString()方法重写了，为了能调用正确的toString()版本，必须间接地调用Function.call()方法。

<pre>
    function classOf() {
      if (o === null) {
        return "Null";
      }
      if (o === undefined) {
    	return "Undefined";
      }
      return Object.prototype.toString.call(o).slice(8, -1);
    }
</pre>

1.   数字，字符串和布尔值可以直接调用toString()方法，就和对象调用toString()方法一样。
2.   通过内置构造函数创建的对象包含"类属性",它与构造函数名称相匹配。
3.   宿主对象也包含有意义的“类属性”，但这和具体的Javascript实现有关。
4.   通过直接量和Object.create创建的对象的类属性是“Object”。
5.   那些自定义构造函数创建的对象也是一样，类属性也是“Object”。自定义的类没有办法通过类属性来去分对象的类。

<pre>
    classOf(null);              // "Null"
    classOf(1);                 // "Number"
    classOf("");                // "String"
    classOf(false);             // "Boolean"
    classOf({});                // "Object"
    classOf([]);                // "Array"
    classOf(/./);               // "Regexp"
    classOf(new Date());        // "Date"
    classOf(window);            // "Window"
    function F() {};
    classOf(new F());           // "Object"
</pre>

*   可扩展性

对象的可扩展性用以表示是否可以给对象添加新属性。内置对象和自定义对象都是显示可扩展的，宿主对象的可扩展性由Javascript引擎定义的。

查询和设置对象可扩展性的

*   函数Object.esExtensible()。将对象传入Object.esExtensible()来判断该对象是否是可扩展的。
*   使用Object.preventExtensions()将对象设置为不可扩展，将待转换的对象作为参数传进去，一旦将对象转换为不可扩展，就无法再将其转换回可扩展，且只影响对象本身的可扩展性。如果给一个不可扩展的对象的原型添加属性，这个不可扩展的对象同样会继承这些属性。

可扩展性的目的是将对象“锁定”，以免外界干扰。对象的可扩展性通常和属性的可配置性与可写性配合使用。

Object.seal()和Object.preventExtensions()类似，除了能够将对象设置为不可扩展的，还可以将对象的所有自有属性都设置为不可配置的。就是不能添加新属性，它已有的属性不能删除或配置，不过它已有的可写属性依然可以设置。对于那些已经封闭（sealed）起来的对象是不能解封的。可以使用Object.isSealed()来检测对象是否封闭。

Object.freeze()将严格的锁定对象--“冻结”（frozen）。除了将对象设置为不可扩展的和将其属性设置为不可配置的之外，还可以将它自有的所有属性设置为只读（如果对象的存取器属性具有setter方法，存取器属性将不受影响，仍然可以通过给属性赋值调用它们）。使用Object.isFrozen()来检测对象是否冻结。

Object.preventExtensions(),Object.seal()和Object.freeze()都返回传入的对象。

<pre>
    var o = Object.seal(Object.create(Object.freeze({x:1}), {y: {value: 2, writable: true} }));
</pre>

<h4>十一、序列化对象</h4>

对象序列化（serialization）是指将对象的状态转换为字符串，也可将字符串还原为对象。

使用JSON作为数据交换的格式，JSON的全称是“Javascript Object Notatin”---Javascript对象表示法。语法和Javascript对象与数组直接量语法非常相近。

*   JSON.stringify()    序列化对象。
*   JSON.parse()        还原Javascript对象。

<pre>
    o = { x: 1, y: { z: [false, null,""] } };  
    s = JSON.stringify(o);      // s是'{ "x":1, "y":{"z":[false, null, ""] } }'
    p = JSON.parse(s);          // p是o的深拷贝。
</pre>

JSON的语法是 Javascript语法的子集，它并不能表示Javascript里的所有值。支持对象、数组、字符串、无穷大数字、true、false和null。并且它们可以序列化和还原。

1.   NaN、Infinity和-Infinity序列化的结果是null。
2.   日期对象序列化的结果是ISO格式的日期字符串（参照Date.toJSON()函数），但JSON.parse()依然保留它们的字符串形态，而不会将它们还原为原始日期对象。
3.   函数、RegExp、Error对象和undefined值不能序列化和还原。
4.   JSON.stringify()只能序列化对象可枚举的自有属性。对于一个不能序列化的属性来说，在序列化后的输出字符串中会将这个属性省略掉。
5.   JSON.stringify()和JSON.parse()都可以接受第二个可选参数，通过传入需要序列化或还原的属性列表来定制自定义的序列化或还原操作。

<pre>
    var obj = {
      title: '对象的标题',
      type: 'primitive',
      releaseDate: new Date(2012, 03, 01)
    };
    var o = JSON.stringify(obj);
    console.log(o);
    JSON.parse(o);
</pre>

<h4>十二、对象方法</h4>
*   toString()方法。返回一个表示调用这个方法的对象的字符串。在需要将对象转化为字符串的时候，Javascript都会调用这个方法。比如，当使用“+”运算符连接一个字符串和一个对象时或者在希望使用字符串的方法中使用了对象时都会调用toString()。

<pre>
    var s = { x: 1, y: 1 }.toString();  // [object Object]
	Array.toString();       // 数组元素列表，元素转换成字符串。
	Date.toString();        // 日期字符串。
	Function.toString();    // 函数源代码。
</pre>

*   toLocaleString()方法。这个方法返回一个表示这个对象的本地化字符串。

Object中默认的toLocaleString()方法并不做任何本地化自身的操作。仅调用toString()方法并返回对应值。Date和Number类对toLocaleString()方法做了定制，可以用它对数字、日期和时间做本地化的转换。Array类的toLocaleString()方法和toString()方法很像，唯一的不同是每个元素回调用toLocalString()方法转换为字符串，而不是调用各自的toString()方法。

*   toJSON()方法。Object.prototype实际上没有定义toJSON()方法，但对于需要执行序列化的对象来说，JSON.stringify()方法会调用toJSON()方法。如果在序列化对象中存在这个方法，则调用它，返回值（序列化结果），而不是原始的对象。

*   valueOf()方法。和toString()方法非常类似。但往往当Javascript需要将对象转换为某种原始值而非字符串的时候才会调用它，尤其是转换为数字的时候。

如果在需要使用原始值的上下文中使用了对象，Javascript就会自动调用valueOf()方法。有些内置类自定义了valueOf()方法（比如Date.valueOf()）。
