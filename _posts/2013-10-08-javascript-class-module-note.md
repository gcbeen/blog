---
layout: post
category: javascript
title: javascript权威指南-类和模块
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、类和原型</h4>
原型对象是类的核心。如果定义了一个原型对象，然后通过inherit()函数创建一个继承自它的对象，这样就定义了一个Javascript类。通常，类的实例还需要进一步的初始化，通常是通过定义一个函数来创建并初始化这个新对象。例如：

<pre>
    function range(from, to) {
      var r = inherit(range.methods);
      r.from = from;
      r.to = to;
      return r;
    }
    
    range.methods = {
      includes: function (x) {
        return this.from <= x && x <= this.to;
      },
      
      foreach: function (f) {
    	var x;
        for (x = Math.ceil(this.from); x <= this.to; x += 1 ) {
          f(x);
    	}
      },
      toString: function () {
        return "(" + this.from + "..." + this.to + ")";
      }
    };
    
    var r = range(1, 3);
    r.includes(2);
    r.foreach(console.log);
    console.log(r);

</pre>

<h4>二、类和构造函数</h4>
构造函数是用来初始化新创建的对象的。使用关键字new来调用构造函数，自动创建一个新对象。调用构造函数的一个重要特征是，构造函数的prototype属性被用作新对象的原型。同一个构造函数创建的新对象继承自一个相同的对象。

<pre>
    function range(from, to) {
      return new Range(from, to);
    }
    function Range(from, to) {
      this.from = from;
      this.to = to;
    }
    Range.prototype = {
      includes: function (x) {
        return this.from <= x && x <= this.to;
      },
      foreach: function (f) {
        var x;
    	for (x = Math.ceil(this.from); x <= this.to; x += 1 ) {
    	  f(x);
    	}
      },
      toString: function () {
        return "(" + this.from + "..." + this.to + ")";
      }
    };
    var r = range(1, 3);
    r.includes(2);
    r.foreach(console.log);
    console.log(r);
</pre>

构造函数

*   this：在调用构造函数之前就已经创建了新对象，通过this关键字可以获取这个新对象。
*   初始化：Range()构造函数只不过是初始化this而已。
*   无返回值：构造函数甚至不必返回这个新创建的对象。构造函数会自动创建对象，然后将构造函数作为这个对象的方法来调用一次，最后返回这个新对象。
*   调用构造函数：new 构造函数。
*   原型：Range.prototype。对Range()构造函数的调用会自动使用Range.prototype作为新Range对象的原型。

<pre>
    function MyObject(x) {
      this.x = x;
    }
    var o = MyObject(5);
    
    console.log(o); // undefined
    console.log(x); // 5
</pre>

类标识

*   原型对象是类的唯一标识：当且仅当两个对象继承自同一个原型对象时，它们才是属于同一个类的实例。
*   初始化对象的状态的构造函数不能作为类的标识。两构造函数的prototype属性可能指向同一个原型对象。那么这两个构造函数创建的实例属于同一个类。
*   构造函数是类的“外在表现”。构造函数的名字通常用作类名。
*   用instanceof运算符检测对象是否属于某个类时，会用到构造函数。

<pre>
    r instanceof Range // true (r是否继承自Range.prototype)
</pre>

constructor属性

任何Javascript函数都可以用做构造函数，并且调用构造函数需要用到一个prototype属性的。因此，每个Javascript函数都自动拥有一个prototype属性。这个属性的值是一个对象，这个对象包含唯一一个不可枚举属性constructor。constructor属性的值是一个函数对象：

<pre>
    var F = function () {};
    var p = F.prototype;
    var c = p.constructor; 
    c === F;// true： F.prototype.constructor === F
</pre>

构造函数的原型中预存定义好的constructor属性，对象继承的constructor指代它们的构造函数。

<pre>
    var o = new F();
    o.constructor === F; // true, constructor属性指代构造函数。
</pre>

重写原型对象设置constructor属性。

<pre>
    Range.prototype = {
      constructor: Range, // 显示设置构造函数的反向引用
      includes: function (x) { 
    	return this.from <= x && x <= this.to; 
      },
      forEach: function (f) {
        var x;
    	for (x = Math.ceil(this.from); x <= this.to; x += 1 ) {
    	  f(x);
    	}
      },
      toString: function () {
        return "(" + this.from + "..." + this.to + ")";
      }
    };
</pre>

使用预定义的原型对象。

<pre>
    Range.prototype.includes = function (x) {
      return this.from <= x && x <= this.to;
    };
    Range.prototype.forEach = function (f) {
      var x;
      for (x = Math.ceil(this.from); x <= this.to; x += 1 ) {
        f(x);
      }
    };
    Range.prototype.toString = function () {
      return "(" + this.from + "..." + this.to + ")";
    };
</pre>

<h4>Javascript中Java式的类继承</h4>

强类型面向对象语言。
实例字段，实例方法，类字段，类方法。

*   构造函数的属性都是类属性，类方法。

<pre>
    function Range() {}

    Range.classAttribute = 'aa';
    Range.classMethod = function () {
        console.log('classMethod');
    };

</pre>

*   原型对象的属性都是实例方法。

<pre>
    Range.prototype.method = function () {
      console.log('method');
    };
</pre>

*   实例对象的属性都是实例属性

<pre>
    var range = new Range();
    range.kk = 'rr';
</pre>

<pre>
    function defineClass(constructor, methods, statics) {
      if (methods) {
        extend(constructor.prototype, methods);
      }
      if (statics) {
        extend(constructor, statics);
      }
      return constructor;
    }

    var SimpleRange = defineClass(function (f, t) {
    							      this.f = f;
    								  this.t = t;
    								},
    								{
    								  includes: function(x) {
    								    return this.f <= x && x <= this.t;
    								  },
    								  toString: function () {
    								    return this.f + "..." + this.t;
    								  }
    								},
    								{
    								  upto: function (t) {
    								    return new SimpleRange(o, t);
    								  }
    								}
    		);

</pre>

“手动”实现java式类。

<pre>
    function Complex(real, imaginary) {
      if (isNaN(real) || isNaN(imaginary) ) {
        throw new TypeError();
      }
      this.r = real;
      this.i = imaginary;
    }
    
    Complex.prototype.add = function (that) {
      return new Complex(this.r + that.r, this.i + that.i);
    };
    
    Complex.prototype.mul = function (that) {
      return new Complex(this.r * that.r - this.i * that.i, this.r * that.i + this.i * that.r);
    };
    
    Complex.prototype.mag = function () {
      return Math.sqrt(this.r * this.r + this.i + this.i);
    };
    
    Complex.prototype.toString = function () {
      return this.r + "," + this.i;
    };
    
    Complex.prototype.equals = function (that) {
      return that != null && that.constructor === Complex && this.r === that.r && this.i === that.i;
    };
    
    Complex.ZERO = new Complex(0, 0);
    Complex.ONE = new Complex(1, 0);
    Complex.I = new Complex(0, 1);
    
    Complex.parse = function (s) {
      try {
        var m = Complex._format.exec(s);
    	return new Complex(parseFloat(m[1]), parseFloat(m[2]) );
      } catch(x) {
        throw new TypeError("Cant parse '" + s + "' as a complex number." );
      }
    };
    
    Complex._format = /^{([^,]+), ([^}]+)\}$/;

</pre>

<pre>
    var c = new Complex(2, 3);
    var d = new Complex(c.i, c.r);
    c.add(d).toString();
    Complex.parse(c.toString()).add(c.neg()).equals(Complex.ZERO);
</pre>

尽管Javascript可以模拟出Java式的类成员，但Java中有很多重要的特性是无法在Javascript类中模拟的。

约定暗示：

*   不能修改的成员：以大写字母开始。
*   类外部不可见成员：下划线前缀（_kkk）。
*   私有属性：闭包的局部变量模拟。

<h4>三、类的扩充</h4>Javascript中基于原型的继承机制是动态的：对象从其原型继承属性，如果创建对象之后原型的属性发生改变，也会影响到继承这个原型的所有实例对象。我们可以通过给原型对象添加新方法来扩充Javascript类。

<pre>
    Complex.prototype.conj = function () {
      return new Complex(this.r, -this.i);
    };
</pre>

Javascript内置类的原型对象也是一样如此“开放”。可以给数字、字符串、数组、函数等数据类型添加方法。

<pre>
    if (!Function.prototype.bind) {
      Function.prototype.bind = function (o /* ,args */) {
        // coding
      };
    }
</pre>

<pre>

    Number.prototype.times = function (f, context) {
      var n = Number(this),
    	  i;
      
      for (i = 0; i < n; i += 1) {
        f.call(context, i);
      }
    };
    
    String.prototype.trim = String.prototype.trim || function () {
      if (!this) {
        return this;
      }
      return this.replace(/^\s+|\s+$/g, "");
    };
    
    Function.prototype.getName = function () {
      return this.name || this.toString().match(/function\s*([^()*]\(/)[1];
    };

</pre>

可以给Object.prototype添加方法，从而使所有的对象都可以调用这些方法。当这种做法并不推荐，如果给Object.prototype添加属性，这些属性是可以被for/in循环遍历到的。可以使用Object.defineProperty()方法可以安全地扩充Object.prototype。然而并不是所有的宿主环境都可以使用Object.defineProperty()。

<h4>四、类和类型</h4>

*   Javascript定义了少量的数据类型：null、undefined、布尔值、数字、字符串、函数和对象。typeof运算符可以得出值的类型。
*   将类作为类型来对待，这样就可以根据对象所属的类来区分它们。Javascript核心的内置对象（包括客户端Javascript的宿主对象）可以根据它们的class属性来区分。

三种检测任意对象的类的技术

*   instanceof运算符
*   constructor属性
*   构造函数的名字。

instanceof运算符：使用构造函数作为中介检测对象的继承关系，可以是非直接继承。

检测对象的原型链上是否存在某个特定的原型对象。

<pre>
    range.methods.isPrototypeOf(r);  // range.method 是r的原型对象
</pre>

instanceof运算符和isPrototypeOf()方法的缺点是，无法通过对象来获取类名，只能检测对象是否属于指定的类名。

在客户端Javascript中还有一个比较严重的不足，就是在多框架和多窗口的子页面的Web应用中兼容性不佳。每个窗口和框架的子页面都具有单独的执行上下文，每个上下文都包含独有的全局变量和一组构造函数。在两个不同框架页面中创建的两个数组继承自两个相同但相互独立的原型对象，其中一个框架页面中的数组不是另一个框架页面的Array()构造函数的实例，instanceof运算结果是false。

constructor属性：构造函数是类的公共标识。

<pre>

    function typeAndValue(x) {
      if (x === null) {
        return "";
      }
    
      // return typeof x + x; 返回字符串
      // return classof(x) + x; 返回字符串
      // x.constructor 返回函数
      switch (x.constructor) {
      case Number: 
    	  return "Number:" + x;
      case String: 
    	  return "String:'" + x + "'";
      case Date: 
    	  return "Date:" + x;
      case RegExp:
    	  return "RegExp:" + x;
      case Complex:
    	  return "Complex:" + x;
      }
    }

</pre>

使用constructor属性检测对象属于某个类的技术不足之处：在多个执行上下文的场景中它无法正常工作，并不是所有对象都包含constructor属性。

构造函数的名称

使用instanceof运算符和constructor属性来检测对象所属的类有一个主要的问题，在多个执行上下文中存在构造函数的多个副本的时候，这两种方法的检测结果会出错。多个执行上下文中的函数看起来是一模一样，但是它们是相互独立的对象，因此彼此也不相等。

一种解决方案是使用构造函数的名字，一个窗口里的Array构造函数和另一个窗口的Array构造函数是不相等的，但是它们的名字是一样的。

<pre>
    function type(o) {
      var t, c, n;
    
      if (o === null) {
        return "null";
      }
    
      if (o !== o) {
        return "NAN"
      }
    
      t = typeof o;
    
      if (t !== "object") {
        return t;
      }
    
      c = classof(o);
    
      if (c !== "Object") {
        return c;
      }
    
      if (o.constructor && typeof o.constructor === "function") {
        n = o.constructor.getName();
    	if (n) {
    	  return n;
    	}
      }
    }
    
    function classof(o) {
      return Object.prototype.toString.call(o).slice(8, -1);
    }
    
    Function.prototype.getName = function () {
      if ("name" in this) {
        return this.name;
      }
      return this.name = this.toString().match(/function\s*([^(]*)\(/)[1];
    };
</pre>

并不是所有的对象都具有constructor属性。并不是所有函数都有名字。如果使用不带名字的函数定义表达式定义一个构造函数，getName()方法则会返回空字符串：

<pre>
    var Complex = function (x, y) {
      this.r = x;
      this.i = y;
    }
    var Range = function Range(f, t) {
      this.from = f;
      this.to = t;
    }
</pre>

<h4>五、鸭式辩型</h4>
使用instanceof运算符、constructor属性和构造函数的名字等检测对象的类多少都会有些问题，至少在客户端Javascript中是如此。

解决办法就是规避掉这些问题：不关注“对象的类是什么”，而是关注“对象能做什么”。这种思考问题的方式称为“鸭式辩型”。

当我们提到鸭式辩型时，往往是说检测对象是否试想了一个或多个方法。

<pre>
    function isImplements(o /*, ...*/) {
      var i,
    	  arg,
    	  m;
      for (i = 1; i < arguments.length; i += 1) {
        arg = arguments[i];
    	switch (typeof arg) {
    	case 'string':
    	  if (typeof o[arg] !=== "function") {
    	    return false;
    	  }
    	  continue;
    	case 'function':
    	  arg = arg.prototype; //参数是一个方法，假设是构造函数。
    	case 'object':
    	  for (m in arg) {
    	    if (typeof arg[m] !== 'function') { // 跳过不是方法的属性。
    		  continue;
    		}
    		if (typeof o[m] !== 'function') {
    		  return false;
    		}
    	  }
    	}
      }
      return true;
    }
</pre>

isImplements()函数注意的地方：

*   只是通过特定的名称来检测对象是否含有一个或多个值为函数的属性。无法得知这些已经存在的属性的细节信息，比如函数是干什么用的。需要多少参数，参数类型是什么。
*   内置方法都是不可枚举的，for/in循环无法遍历到它们。不能通过isImplements(o, Array)来检测o是否实现了Array中的所有同名方法。

<h4>六、Javascript中的面向对象</h4>

集合类（set）。

<pre>

    function Set() {
      this.values = {};
      this.n = 0;
      this.add.apply(this, arguments);
    }
    
    Set.prototype.add = function () {
      var i,
    	  val,
    	  str;
      for (i = 0; i < arguments.length; i += 1) {
        val = arguments[i];
    	str = Set._v2s(val);
    	if(!this.values.hasOwnProperty(str) ) {
    	  this.values[str] = val;
    	  this.n += 1;
    	}
      }
      return this;
    };
    
    Set.prototype.remove = function () {
      var i,
    	  str;
      for (i = 0; i < arguments.length; i += 1) {
        str = Set._v2s(arguments[i]);
    	if (this.values.hasOwnProperty(str) ) {
    	  delete this.values[str];
    	  this.n -= 1;
    	}
      }
      return this;
    };
    
    Set.prototype.contains = function (value) {
      return this.values.hasOwnProperty(Set._v2s(value) );
    };
    
    Set.prototype.size = function () {
      return this.n;
    };
    
    Set.prototype.foreach = function (f, context) {
      var s;
      for (s in this.values) {
        if (this.values.hasOwnProperty(s) ) {
    	  f.call(context, this.values[s]);
    	}
      }
    };
    
    Set._v2s = function (val) {
      switch (val) {
      case undefined: 
    	  return 'u';
      case null:
    	  return 'n';
      case true:
    	  return 't';
      case false:
    	  return 'f';
      default: switch (typeof val) {
    		   case 'number':
    			   return '#' + val;
    		   case 'string':
    			   return '"' + val;
    		   default:
    			   return '@' + objectId(val);
    		   }
      }
    
      function objectId(o) {
        var prop = "|**objectid**|";
    	if (!o.hasOwnProperty(prop) ) {
          Set._v2s.next += 1;
    	  o[prop] = Set._v2s.next;
    	}
    	return o[prop];
      }
    
    };
    
    Set._v2s.next = 100;

</pre>

枚举类型

<pre>
    var Coin = enumeration({Penny: 1, Nickel: 5, Dime: 10, Quarter: 25});
    var c = Coin.Dime;
    c instanceof Coin;
    c.constructor == Coin;
    Coin.Quarter + 3 * Coin.Nickel;
    Coin.Dime = 10;
    Coin.Dime > Coin.Nickel;
    String(Coin.Dime) + ":" + Coin.Dime;
</pre>

<pre>

    function enumeration(namesToValues) {
      var name,
    	  e,
    	  i;
    
      var enumeration = function () {
        throw "Can't Instantiate Enumerations";
      };
    
      var proto = enumeration.prototype = {
        constructor: enumeration,
    	toString: function () {
    	  return this.name;
    	},
        valueOf: function () {
    	  return this.value;
    	},
    	toJSON: function () {
    	  return this.name;
    	}
      };
    
      enumeration.values = [];
    
      for (name in namesToValues) {
        e = inherit(proto);
    	e.name = name;
    	e.value = namesToValues[name];
    	enumeration[name] = e;
    	enumeration.values.push(e);
      }
    
      enumeration.foreach = function (f, c) {
        for (i = 0; i < this.values.length; i += 1) {
    	  f.call(c, this.vaues[i]);
    	}
      };
      return enumeration;
    }

</pre>

枚举类型表示一副扑克牌
<pre>

    function Card(suit, rank) {
      this.suit = suit;
      this.rank = rank;
    }
    
    Card.Suit = enumeration({Clubs: 1, Diamonds: 2, Hearts: 3, Spades: 4  });
    Card.Rank = enumeration({Two: 2, Three: 3, Four: 4, Five: 5, Six: 6,
    		                 Seven: 7, Eight: 8, Nine: 9, Ten: 10,
    						 Jack: 11, Queen: 12, King: 13, Ace: 14});
    Card.prototype.toString = function () {
      return this.rank.toString() + "of" + this.suit.toString();
    };
    
    Card.prototype.compareTo = function (that) {
      if (this.rank < that.rank) {
        return -1;
      }
      if (this.rank > that.rank) {
        return 1;
      }
      return 0;
    };
    
    Card.orderByRank = function (a, b) {
      return (a.suit - b.suit) || a.rank - b.rank;
    };
    
    function Deck() {
      var cards = this.cards = [];
      Card.Suit.foreach(function (s) {
                          Card.Rank.foreach(function (r) {
    						                  cards.push(new Card(s, r));
    						                } );
    		            });
    }
    
    //洗牌的方法
    Deck.prototype.shuffle = function () {
      var deck = this.cards,
    	  i = deck.length - 1,
    	  r,
    	  temp;
      while (i) {
        r = Math.floor(Math.random() * (i + 1) );
    	temp = deck[i], deck[i] = deck[r], deck[r] = temp;
      }
      return this;
    }
    
    //发牌方法
    Deck.prototype.deal = function (n) {
      if (this.cards.length < n) {
    	  throw "Out of cards";
      }
      return this.cards.splice(this.cards.length -n, n);
    }
    
    var deck = (new Deck()).shuffle();
    var hand = deck.deal(13).sort(Card.orderBySuit)

</pre>

<h4>七、标准转换方法</h4>
有一些方法是在需要做类型转换时由Javascript解释器自动调用的。

*   toString()：返回一个可以表示这个对象的字符串。在希望使用字符串的地方用到对象的话（比如将对象用作属性名或使用“+”运算符来进行字符串连接运算），Javascript会自动调用这个方法。如果没有实现这个方法，类会默认从Object.prototype中继承toString()方法，这个方法的运算结果是“[object Object]”，这个字符串用处不大。应当返回一个可读的字符串。

*   toLocaleString()：以本地敏感性方式来将对象转换为字符串。默认情况下，对象所继承的toLocaleString()方法只是简单地调用toString()方法。有一些内置类型包含有用的toLocaleString()方法用以实际上返回本地化相关的字符串。

*   valueOf()：它用来将对象转换为原始值。比如，当数学运算符（除了“+”运算符）和关系运算符作用于数字文本表示的对象时，会自动调用valueOf()方法。大多数对象都没有合适的原始值来表示它们，也没有定义这个方法。

*   toJSON()：这个方法是由JSON.stringify()自动调用的。JSON格式用于序列化良好的数据结构，而且可以处理Javascript原始值、数组和纯对象。它和类无关，当对一个对象执行序列化操作时，它会忽略对象的原型和构造函数。

<h4>八、比较方法</h4>

Javascript的相等运算符比较对象时，比较的是引用而不是值。

<pre>
    Range.prototype.constructor = Range;
    Range.prototype.equals = function (that) {
      if (that == null) {
        return false;
      }
      if (that.constructor !== Range) {
        return false;
      }
      return this.from == that.from && this.to == that.to;
    }
</pre>

<pre>
    Set.prototype.equals = function (that) {
      if (this === that) {
        return true;
      }
      if (!(that instanceof Set) ) {
        return false
      }
      if (this.size() != that.size() ) {
        return false;
      }
    
      try {
    	this.foreach(function (v) { 
    			       if (!that.contains(v) ) {
    				     throw false
    				   });
    	return true;
      } catch (x) {
        if (x === false) {
    	  return false;
    	}
    	throw x;
      }
    };
</pre>

如果将对象用于Javascript的关系比较运算符，比如“<”和“<=”，Javascript会首先调用对象的valueOf()方法。

为了按照显示定义的规则来比较这些类型的对象，可以定义一个方法如 compareTo()。

<pre>
    Range.prototype.compareTo = function (that) {
      return this.from - that.from;
    }
</pre>

<pre>
    Range.prototype.compareTo = function (that) {
      if (!(that instanceof Range) ) {
        throw new Error("Can't compare a Range with " + that);
      }
      return this.from - that.from || this.to - that.to;
    }
    
    ranges.sort(function (a, b) {
    		      return a.compareTo(b);
    		      });
    
    Range.byLowerBound = function (a, b) {
      return a.compareTo(b);
    }
    
    ranges.sort(Range.byLowerBound);
</pre>

<h4>九、方法借用</h4>
多个类中的方法可以共用一个单独的函数。以经典的面向对象语言的视角来看Javascript，把一个类的方法用到其他的类中的做法称作“多重继承”。在Javascript中叫做“方法借用”。

<pre>
    Range.prototype.equals = generic.equals;
    
    var generic = {
      toString: function () {
        var s = '[',
            n = 0,
            name,
            value;
        
        if (this.constructor && this.constructor.name) {
          s += this.constructor.name + ": ";
        }
    
        for (name in this) {
          if (!this.hasOwnProperty(name) ) {
            continue;
          }
          value = this[name];
          if (typeof value === "function") {
            continue;
          }
          if (n) {
            s += ", ";
    	  }
          s += name + '=' + value;
		  n += 1;
        }
        return s + ']';
      },
      
      equals: function (that) {
        var name;
    	if (that == null) {
    	  return false;
    	}
    	if (this.constructor !== that.constructor) {
    	  return false;
    	}
    	for (name in this) {
    	  if (name === "|**objectid**|") {
    	    continue;
    	  }
    	  if (!this.hasOwnProperty(name) ) {
    	    continue;
    	  }
    	  if (this[name] !== that[name]) {
    	    return false;
    	  }
    	}
    	return true;
      }
      
    };
</pre>

<h4>十、私有状态</h4>
通过将变量（或参数）闭包在一个构造函数内来模拟实现私有实例字段。

<pre>
    function Range(from, to) { // 私有字段from, to
      this.from = function () {
        return from;
      },
      this.to = function () {
        return to;
      }
    }
    Range.prototype = {
      constructor: Range,
      includes: function (x) {
        return this.from() <= x && x <= this.to();
      },
      foreach: function (f) {
        var x = math.ceil(this.from() ),
    	    max = this.to();
    	while (x <= max) {
    	  f(x);
    	  x += 1;
    	}
      },
      toString: function () {
        return "(" + this.from() + "..." + this.to() + ")";
      }
    };
</pre>

<pre>
    var r = new Range(1, 5);
    r.from = function() {
      return 0;
    };
</pre>

<h4>十一、构造函数的重载和工厂方法</h4>
有时候，我们希望对象的初始化有多种方式。通过重载这个构造函数让它根据传入的参数不同来执行不同操作。

<pre>
    function Set() {
      this.values = {};
      this.n = 0;
      
	  // 如果传入一个数组的对象，将这个元素添加至集合中
	  // 否则，将所有的参数都添加至集合中。
      if (arguments.length == 1 && isArrayLike(arguments[0]) ) {
        this.add.apply(this, arguments[0]);
      } else if (arguments.length > 0) {
        this.add.apply(this, arguments);
      }
    }
</pre>

<pre>
    Complex.polar = function (r, theta) { // 极坐标初始化的Complex对象。
      return new Complex(r * Math.cos(theta), r * Math.sin(theta) );
    };
    
    // 通过数组初始化Set对象。
    Set.fromArray = function (a) {
      s = new Set();
      s.add.apply(s, a);
      return s;
    };
</pre>

<pre>
    function SetFromArray(a) {
      Set.apply(this, a);
    }
    SetFromArray.prototype = Set.prototype;
    
    var s = new SetFromArray([1, 2, 3]);
    s instanceof Set
</pre>

<h4>十二、子类</h4>
Javascript的对象可以从类的原型对象中继承属性，B的原型对象继承自A的原型对象。

<pre>
    B.prototype = inherit(A.prototype);
    B.prototype.constructor = B;
</pre>

原型对象仅仅是一个普通对象，它只继承自Object.prototype，这个类和所有类一样是Object的子类。

<pre>
    function defineSubclass(superclass, constructor, methods, statics) {
      constructor.prototype = inherit(superclass.prototype);
      constructor.prototype.constructor = constructor;
      if (methods) {
        extend(constructor.prototype, methods);
      }
      if (statics) {
        extend(constructor, statics);
      }
      return constructor;
    }
    
    Function.prototype.extend = function (constructor, methods, statics) {
      return defineSubclass(this, constructor, methods, statics);
    };

</pre>

<pre>
    //SingletonSet：一个简单的子类
    
    function SingletonSet(member) {
      this.member = member;
    }
    
    SingletonSet.prototype = inherit(Set.prototype);
    
    extend(SingletonSet.prototype, {
                constructor: SingletonSet,
    			add: function () { 
    			       throw "read-only set";
    				 },
    			remove: function () {
    			  throw "read-only set";
    			},
                size: function () {
    			  return 1;
    			},
                foreach: function (f, context) {
    			  f.call(context, this.member);
    			},
                contains: function (x) {
    			  return x === this.member;
    			}
    		  });

</pre>

<pre>
    SingletonSet.prototype.equals = function (that) {
      return that instanceof Set && that.size() == 1 && that.contains(this.member);
    }
</pre>

SingletonSet不是将Set中的方法列表静态地借用过来，而是动态地从Set类继承方法。如果给Set.prototype添加新的方法，Set和SingletonSet的所有实例就会立即拥有这个方法。

<h4>十三、构造函数和方法链</h4>
构造函数和子类的方法需要调用或链接到父类构造函数和父类方法。

<pre>

    function NonNullSet() {
      Set.apply(this, arguments);
    }
    
    NonNullSet.prototype = inherit(Set.prototype);
    NonNullSet.prototype.constructor = NonNullSet;
    
    NonNullSet.prototype.add = function () {
      var i = 0;
      while (i < arguments.length) {
        if (arguments[i] == null) {
    	  throw new Error("Can't add null or undefined to a NonNullSet");
    	}
    	i += 1;
      }
      return Set.prototype.add.apply(this, arguments);
    };

</pre>

将这个非null集合的概念推而广之，称为“过滤后的集合”，这个集合中的成员必须首先传入一个过滤函数再执行添加操作。

<pre>
    var StringSet = filteredSetSubclass(Set, function (x) {
    		                                   return typeof x === "string"; 
    										 } );
    var MySet = filteredSetSubclass(NonNullSet, function (x) {
    		                                      return typeof x !== "function"; 
    											});
</pre>

<pre>
    //类工厂和方法链
    function filteredSetSubclass(superclass, filter) {
      var i,
    	  v;
      var constructor = function () {
        superclass.apply(this, arguments);
      };
      var proto = constructor.prototype = inherit(superclass.prototype);
      proto.constructor = constructor;
      proto.add = function () {
        for (i = 0; i < arguments.length; i += 1) {
    	  v = arguments[i];
    	  if (!filter(v) ) {
    	    throw ("value " + v + " rejected by filter" );
    	  }
    	}
    	superclass.prototype.add.apply(this, arguments);
      };
      return constructor;
    }
</pre>

<pre>
    var NonNullSet = (function () {
		  var superclass = Set;
    	  return superclass.extend(function () {
    		superclass.apply(this, arguments);
    	  },
    	  { add: function () {
    	      var i;
    		  for (i = 0; i < arguments.length; i += 1) {
    		    if (arguments[i] == null) {
    			  throw new Error("Can't add null or undefined");
				}
    		  }
    		  return superclass.prototype.add.apply(this, arguments);
    		}
    	  });
		  } ());
    
</pre>

类似这种创建类工厂的能力是Javascript语言动态特性的一个体现。

<h4>十四、组合vs子类</h4>

创建的子类使用了特定的过滤函数来对集合中的成员做限制。父类和过滤函数的每个组合都需要创建一个新的类。还有一种更好的方法完成对集合中的成员做限制这种需求。使用组合，“组合优于继承”。

<pre>
//组合
var FilteredSet = Set.extend(function FilteredSet(set, filter) {
        this.set = set;
		this.filter = filter;
	  },

	  { 
        add: function () {
		  var i,
		      v;
		  if (this.filter) {
		    for (i = 0; i < arguments.length; i += 1) {
			  v = arguments[i];
			  if (!this.filter(v) ) {
			    throw new Error("FilteredSet: value " + v + " rejected by filter");
			  }
			}
		  }
		  this.set.add.apply(this.set, arguments);
		  return this;
		},

		remove: function () {
		  this.set.remove.apply(this.set, arguments);
		  return this;
		},
	
		contains: function (v) {
		  return this.set.contains(v);
		},

		size: function () {
		  return this.set.size();
		},

		foreach: function (f, c) {
		  this.set.foreach(f, c);
		}
	  }
	);

var s = new FilteredSet(new Set(), function (x) { 
		                             return x !== null; 
								   } );
var t = new FilteredSet(s, { function (x) { 
		                       return !(x instanceof Set ) 
							 } } );

</pre>

<h4>十五、ECMAScript 5中的类</h4>

*   让属性不可枚举（Object.defineProperty() ）

<pre>
(function () {
   var idprop = "|**objectId**|";
   var nextid = 1;
   /*
	* 定义一个不可枚举的属性objectId,
	* 没有定义setter, 只读
	* 不可配置， 不能删除。
	*/
   Object.defineProperty(Object.prototype, "objectId", {
	   get: idGetter,
	   enumerable: false,
	   configurable: false
	 });
   function idGetter() {
     if (!(idprop in this) ) {
	   if (!Object.isExtensible(this) ) {
	     throw Error("Can't define id for nonextensible objects");
	   }
	   Object.defineProperty(this, idprop, {
           value: nextid,
	       writable: false,
	       enumerable: false,
	       configurable: false
	     } );
	   nextid += 1;
	 }
	 return this[idprop];
   }
 } ());
</pre>

*   定义不可变的类：实例方法设置为“只读”和“不可删除”。

<pre>

    // 给Range.prototype对象添加属性
    // 设置属性的特性
    // 无法识别出它们的可枚举性、可写性和可配置性，默认为false
    function Range(from, to) {
      var props = {
        from: {value: from, enumerable: true, writable: false, configurable: false},
    	to: {value: to, enumerable: true, writable: false, configurable: false}
      };
      if (this instanceof Range) {
        Object.defineProperties(this, props);
      } else {
        return Object.create(Range.prototype, props);
      }
    }
    
    Object.defineProperties(Range.prototype, {
      
      includes: {
        value: function (x) {
    	  return this.from <= x && x <= this.to;
    	}
      },
      
      foreach: {
        value: function (f) {
    	  var x = Math.ceil(this.from);
    	  while (x <= this.to) {
    	    f(x);
    		x += 1;
    	  }
    	}
      },
    
      toString: {
        value: function () {
    	  return "(" + this.from + "..." + this.to + ")";
    	}
      }
      
    });

</pre>

<pre>
    //属性描述符工具函数
    function freezeProps(o) {
      var props = arguments.length == 1 ? Object.getOwnPropertyNames(o) : Array.prototype.splice.call(arguments, 1);
      props.forEach(function (n) {
    	if (!Object.getOwnPropertyDescriptor(o, n).configurable) {
    	  return;
    	}
    	Object.defineProperty(o, n, {writable: false, configurable: false});
      });
      return o;
    }
    
    function hideProps(o) {
      var props = arguments.length == 1 ? Object.getOwnPropertyNames(0) : Array.prototype.splice.call(arguments, 1);
      props.forEach(function (n) {
        if (!Object.getOwnPropertyDescriptor(o, n).configurable) {
    	  return;
    	}
    	Object.defineProperty(o, n, {enumerable: false});
      } );
      return o;
    }
</pre>

<pre>
    function Range(from, to) {
      this.from = from;
      this.to = to;
      freezeProps(this);
    }
    Range.prototype = hideProps({
      constructor: Range,
      
      includes: function (x) {
        return this.from <= x && x <= this.to;
      },
    
      foreach: function (f) {
        var x = Math.ceil(this.from);
    	while (x < this.to) {
    	  f(x);
    	  x += 1;
    	}
      },
    
      toString: function () {
        return "(" + this.from + "..." + this.to + ")";
      }
    
    });
</pre>

*   封装对象状态

<pre>
    function Range(from, to) {
      if (from > to) {
        throw new Error("Range: from must be <= to");
      }
    
      function getFrom () {
        return from;
      }
    
      function getTo() {
        return to;
      }
    
      function setFrom(f) {
        if (f <= to) {
    	  from = f;
    	} else {
    	  throw new Error("Range: from must be <= to");
    	}
      }
    
      function setTo(t) {
        if (t >= from){
    	  to = t;
    	} else {
    	  throw new Error("Range: to must be >= from");
    	}
      }
    
      Object.defineProperties(this, {
        from: {get: getFrom, set: setFrom, enumerable: true, configurable: false},
    	to: {get: getTo, set: setTo, enumerable: true, configurable: false}
      });
    
    }
    Range.prototype = hideProps({
      constructor: Range,
      includes: function (x) {
        return this.from <= x && x <= this.to;
      },
      foreach: function (f) {
        var x = Math.ceil(this.from);
    	while (x < this.to) {
    	  f(x);
    	  x += 1;
    	}
      },
      
      toString: function () {
        return "(" + this.from + "..." + this.to + ")";
      }
    });
</pre>

*   防止类的扩展

Object.preventExtensions()将对象设置为不可扩展。

Object.seal()阻止用户给对象添加新属性，还能将当前已有的属性设置为不可配置的，这样就不能删除这些属性。

<pre>
    //阻止对Object.prototype
    Object.seal(Object.prototype);
</pre>

<pre>
    //对象的方法可以随时替换
    var original_sort_method = Array.prototype.sort;
    Array.prototype.sort = function () {
      var start = new Date(),
    	  end;
      original_sort_method.apply(this, arguments);
      end = new Date();
      console.log("Array sort took " + (end - start) + " milliseconds." );
    };
</pre>

防止类扩展的方法。

1.   将实例方法设置为只读。
2.   使用freezeProps()工具函数。
3.   Object.freeze()：把属性设置为只读和不可配置。

只读属性：如果对象o继承了只读属性p，那么给o.p的赋值操作将会失败，就不会给o创建新属性。如果想重写一个继承来的只读属性，就必须使用Object.defineProperty()、Object.defineProperties()或Object.create()来创建这个新属性。重写它的子类的方法的难度会更大。

<pre>
    Object.freeze(enumeration.values);
    Object.freeze(enumeration);
</pre>
	
<h4>十六、子类和ECMAScript 5</h4>

<pre>

    function StringSet() {
      this.set = Object.create(null);
      this.n = 0;
      this.add.apply(this, arguments);
    }
    StringSet.prototype = Object.create(AbstracWritableSet.prototype, {
      constructor: { 
        value: StringSet
      },
    
      contains: {
        value: function (x) {
          return x in this.set;
        } 
      },
    
      size: { 
        value: function (x) {
          return this.n;
        } 
      },
    
      foreach: { 
        value: function (f, c) {
    	  Object.keys(this.set).forEach(f, c);
        }
      },
      
      add: {
        value: function () {
    	  var i = 0;
    	  while (i < arguments.length) {
    	    if (!(arguments[i] in this.set) ) {
    		  this.set[arguments[i]] = true;
    		  this.n += 1;
    		}
    	  }
    	  return this;
    	}
      },
      
      remove: {
        value: function () {
    	  var i = 0;
    	  while (i < arguments.length) {
    	    delete this.set[arguments[i]];
    		this.n -= 1;
    	  }
    	  return this;
    	}
      }
    });
</pre>


<h4>十七、属性描述符</h4>

<pre>
    // 给Object.prototype定义properties()方法
    // 返回调用它的对象的属性名列表的对象。
    // 属性名列表对象的4个有用方法：toString()、descriptors()、hide()和 show()
    //
    
    (function namespace() {
       function properties() {
         var names;
    	 if (arguments.length == 0) {
    	   names = Object.getOwnPropertyNames(this);
    	 } else if (arguments.length == 1 && Array.isArray(arguments[0])) {
    	   names = arguments[0];
    	 } else {
    	   names = Array.prototype.splice.call(arguments, 0);
    	 }
    	 return new Properties(this, names);
       }
    
       Object.defineProperty(Object.prototype, "properties", {
    	 value: properties,
    	 enumerable: false, writable: true, configurable: true
       });

	   function Properties(o, names) {
	     this.o = o;
		 this.names = names;
	   }
    
       Properties.prototype.hide = function () {
         var o = this.o,
    	     hidden = { enumerable: false };
    
    	 this.names.forEach(function (n) {
    	   if (o.hasOwnProperty(n) ) {
    	     Object.defineProperty(o, n, hidden);
    	   }
    	 });
    	 return this;
       },
    
      Properties.prototype.descriptors = function () {
    	var o = this.o,
    	    desc = {};
    
    	this.names.forEach(function (n) {
    	  if (!o.hasOwnProperty(n) ) {
    	    return;
    	  }
    	  desc[n] = Object.getOwnPropertyDescriptor(o, n);
    	});
    	return desc;
      };
    
      Properties.prototype.toString = function () {
        var o = this.o,
    		lines = this.names.map(nameToString);
    	return "{\n " + lines.join(",\n ") + "\n}";
    
    	function nameToString(n) {
    	  var s = "",
    		  desc = Object.getOwnPropertyDescriptor(o, n);
    	  if (!desc) {
    	    return "nonexistent " + n + ": undefined";
    	  }
    	  if (!desc.configuable) {
    	    s += "permanent";
    	  }
    	  if ((desc.get && !desc.set) || !desc.writable) {
    	    s += "readonly";
    	  }
    	  if (!desc.enumerable) {
    	    s += "hidden";
    	  }
    	  if (desc.get || desc.set) {
    	    s += "accessor" + n;
    	  } else {
    	    s += n + "" + ((typeof desc.value === "function") ? "function" : desc.value );
    	  }
    
    	  return s;
    	}
      };
    
	Properties.prototype.properties().hide();
    
    } () );
</pre>

<h4>十八、模块</h4>

*   用作命名空间的对象

在模块的创建过程中避免污染全局变量的一种方法：使用一个对象作为命名空间。将函数和值作为命名空间对象属性存储起来。

<pre>
  var sets = {};
  
  sets.SingletonSet = sets.AbstracEnumerableSet.extend(...);

  var s = new sets.SingletonSet(1);

  // or
  var Set = sets.Set;

  var s = new Set(1, 2, 3);

  //更大的模块集合

  var collections;
  if (!collections) {
    collections = {};
  }
  collections.sets = {};

  collections.sets.AbstractSet = function () { ... }

  //域名反转
  var sets = com.davidflanagan.collections.sets;

</pre>

按照约定，模块的文件应当和命名空间匹配。sets模块应当保存在文件sets.js中。collections.sets模块应当保存在目录collections/sets.js中。

*   作为私有命名空间的函数

将函数作用域用作模块的私有命名空间（“模块函数”）。

<pre>

var Set = (function invocation() {
  function Set() {
    this.values = {};
	this.n = 0;
	this.add.apply(this, arguments);
  }
  Set.prototype.contains = function (value) {
    return this.values.hasOwnProperty(_v2s(value));
  };
  Set.prototype.size = function () {
    return this.n;
  };
  Set.prototype.add = function () { /* ... */ };
  Set.prototype.remove = function () { /* ... */ };
  Set.prototype.foreach = function (f, context) { /* ... */ };
  function _v2s(val) { /* ... */ }
  function objectId(o) { /* ... */ }
  var nextId = 1;
  return Set;
} ());

</pre>

模块api

<pre>
    var collections;
    if (!collections) {
      collections = {};
    }
    
    collections.sets = (function namespace() {
      // coding
      return {
        AbstractSet: AbstractSet,
    	NotSet: NotSet,
    	AbstracEnumerableSet: AbstracEnumerableSet,
    	SingletonSet: SingletonSet,
    	AbstracWritableSet: AbstracWritableSet,
    	ArraySet: ArraySet
      };
    } ());
</pre>

模块api：通过new调用导出。

<pre>
    var collections;
    if (!collections) {
      collections = {};
    }
    collections.sets = (new function namespace() {
      //
      //api导出至this对象
      this.AbstractSet = AbstractSet;
      this.NotSet = NotSet;
    }());

</pre>

另一种替代方案。

<pre>
    var collections;
    if (!collections) {
      collections = {};
    }
    collections.sets = {};
    (function namespace() {
      collections.sets.AbstractSet = AbstractSet;
      collections.sets.NotSet = NotSet;
    } ());
</pre>
