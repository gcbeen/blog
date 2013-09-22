---
layout: post
category : javascript
title: Javascript编程风格
tags : [note, javascript, beginner, tutorial]
---
{% include JB/setup %}
<p>Douglas Crockford是Javascript权威，他分享了一些好的Javascript编程风格：</p>
<p><strong>所谓“编程风格”（programming style），指的是编写代码的样式规则。</strong>不同的程序员，往往有不同的编程风格。</p>
<p>有人说，编译器的规范叫做“语法规则”（grammar），这是程序员必须遵守的；而编译器忽略的部分，就叫“编程风格”（programming style）这是程序员可以自由选择的。这种说法不完全正确，<strong>程序员固然可以自由选择编程，但是好的编程风格有助于写出质量高、错误少、更易于维护的程序。</strong></p>
<p>所以，有一点应该明确，<strong>“编程风格”的选择不应该基于个人爱好、熟悉程度、打字工作量等因素，而要考虑如何尽量使代码清晰易读、减少出错。你的选择，不是你喜欢的风格，而是一种能够清晰表达你的意图的风格。</strong>这一点，对于Javascript这种语法自由度很高、设计不完全成熟的语言尤其重要。</p>
<h4>一、大括号的位置</h4>
<p>绝大多数的编程语言，都用大括号（{}）表示区块（block）。起首的大括号的位置，有许多不同的写法。</p>
<p>最流行的两种写法。一种是起首的大括号另起一行：</p>
<pre>
    block
    {
	  ...
    }
</pre>
另一种是起首的大括号跟在关键字的后面：

<pre>
    block {
      ...
    }
</pre>
一般来说，这两种写法都可以接受。但是Javascript要使用后一种，因为Javascript会自动添加句末的分号，导致一些难以察觉的错误。

<pre>
    return
    {
	  key:value;
    };
</pre>
上面的代码的原意，是要返回一个对象，但实际上返回的是undefined，因为Javascript会自动在return语句后面添加分号。为了避免这一类错误，需要写成这样：

<pre>
    return {
	  key : value;
    };
</pre>
因此，

<pre>
    <strong>规则一：表示区块起首的大括号，不要另起一行。</strong>
</pre>
<h4>二、园括号</h4>
园括号（parentheses）在Javascript中有两种作用，一种表示调用函数，另一种表示的值的组合（grouuping）。我们可以用空格，区分这两种不同的括号。

<pre>
    <strong>规则2：调用函数的时候，函数名与左括号之间没有空格。</strong>
</pre>
<pre>
    <strong>规则3：函数名与参数序列之间，没有空格。</strong>
</pre>
<pre>
    <strong>规则4：所有的语法元素与做括号之间，都有一个空格。</strong>
</pre>
按照以上规则，下面的写法都是不规范的：

<pre>

    foo (bar)

    return(a+b);

    if(a === 0) {...}

    function foo (b) {...}

    function(x) {....}

</pre>
<h4>三、分号</h4>
分号表示语句的结束。大多数情况下，如果你省略了句的分号，Javascript会自动添加。

<pre>
    var a = 1
</pre>
等同于

<pre>
    var a = 1;
</pre>
因此，有人提倡省略句尾的分号。但麻烦的是，如果下一行的第一个字元（token）是下面五个字符之一，Javascript将不对上一行句尾添加分号：“(”、“\[”、“/”、“+”和“-”。

<pre>
    x = y
    (function (){
     ...
    })();
</pre>
上面代码等同于

<pre>
    x = y(function (){...})();
</pre>
因此，

<pre>
    <strong>规则5：不要省略句末的分号。</strong>
</pre>
<h4>四、with语句</h4>
with可以减少代码的书写，但是会造成混淆。

<pre>
    with (o) {
      foo = bar;
    }
</pre>

上面的代码，可以有四种运行结果：

<pre>
    o.foo = bar;
    o.foo = o.bar;
    foo = bar;
    foo = o.bar;
</pre>
这四种结果都可能发生，取决于不同的变量是否有定义。因此，

<pre>
    <strong>规则6：不要使用with语句。</strong>
</pre>
<h4>五、相等和严格相等</h4>
Javascript有两个表示“相等”的运算符：“相等”（==）和“严格相等”（===）。

因为“相等”运算符会自动转换变量类型，造成很多意想不到的情况：

<pre>
    0 == '' // true
    1 == true //true
    2 == true // false
    0 == '0' // true
    " \t\r\n" == 0 // true
</pre>
因此，

<pre>
    <strong>规则7：不要使用“相等”（==）运算符，只使用“严格相等”（===）运算符。</strong>
</pre>

<h4>六、语句的合并</h4>
有些程序员追求简洁，喜欢合并目的不同的语句。比如，原来的语句是

<pre>
    a = b;
	if (a) { ... }
</pre>
他喜欢写成下面这样：

<pre>
    if (a = b) { ... }
</pre>
虽然语句少了一行，但是可读性大打折扣，而且会造成误读，让别人误以为这行代码的意思是：

<pre>
    if (a === b) { ... }
</pre>
另外一种情况是， 有些程序员喜欢在同一行中符多个变量：

<pre>
    var a = b = 0;
</pre>
他以为，这行代码等同于

<pre>
    var a = 0, b = 0;
</pre>
实际上不是，他真正效果是下面这样：

<pre>
    b = 0;
	var a = b;
</pre>
因此，

<pre>
    <strong>规则8：不要将不同目的的语句，合并成一行。</strong>
</pre>
<h4>七、变量声明</h4>
Javascript会自动将变量声明“提升”（hoist）到代码块（block）的头部。

<pre>
    if (!o) {
	  var o = {};
	}
</pre>
等同于：

<pre>
    var o;
	if (!o) {
	  o = {};
	}
</pre>
为了避免可能出现的问题，不如把变量声明都放在代码块的头部。

<pre>
    for (var i ...) {...}
</pre>
最好写成：

<pre>
    var i;
	for (i ...) {...,}
</pre>
因此，

<pre>
    <strong>规则9：所有变量声明都放在函数的头部。</strong>
</pre>
<pre>
    <strong>规则10：所有函数都在使用之前定义。</strong>
</pre>
<h4>八、全局变量</h4>
Javascript最大的语法缺点，可能就是全局变量对于任何一个代码块，都是可读可写。这对代码的模块化和重复使用，非常不利。

<pre>
    <strong>规则11：避免使用全局变量；如果不得不使用，用大写字母表示变量名，比如 UPPER_CASE.</strong>
</pre>
<h4>九、new命令</h4>
Javascript使用new命令，从构建函数生成一个对象。

<pre>
    var o = new myObject();
</pre>
这种做法的问题是，一旦你忘了加上new,myObject()内部的this关键字就会指向全局变量，导致所有绑定在this上面的变量，都变成全局变量。

<pre>
    var userB = {
      init: function (name) {
        this.name = name;
      },
      sayHello: function() {
        console.log('Hello '+ this.name);
      }
  };
  var bob = Object.create(userB);
  bob.init("bog");
  bob.sayHello();
</pre>
<pre>
    <strong>规则12：不要使用new命令，改用Object.create()命令。</strong>
</pre>
如果不得不使用new，为了防止出错，最好在视觉上把构建函数与其他函数区分开来。

<pre>
    <strong>规则13：构建函数的函数名，采用首字母大写（InitialCap）；其他函数名，一律首字母小写。</strong>
</pre>
<h4>十、自增和自减运算符</h4>
自增（++）和自减（--）运算符，放在变量的前面或后面，返回的值不一样，很容易发生错误。

事实上，所有的++运算符多可以用“+=1”代替。

<pre>
    ++x
</pre>
等同于

<pre>
    x += 1;
</pre>
代码变得更清晰了。有一个很可笑的例子，某个Javascript函数库的源代码中出现了下面的片段：

<pre>
    ++x;

	++x;
</pre>
这个程序员忘了，还有跟简单、更合理的写法：

<pre>
    x += 2;
</pre>
因此，

<pre>
    <strong>规则14：不要使用自增（++）和自减（--）运算符，用+=和-=代替。</strong>
</pre>
<h4>十一、区块</h4>
如果循环和判断的代码只有一行，Javascript允许该区块（block）省略大括号。

下面的代码

<pre>
    if (a) b(); c();
</pre>
原意可能是

<pre>
    if (a) { b(); c(); }
</pre>
但是，实际效果是

<pre>
    if (a) { b(); } c();
</pre>
因此，

<pre>
    <strong>规则15：总是使用大括号表示区块。</strong>
</pre>

