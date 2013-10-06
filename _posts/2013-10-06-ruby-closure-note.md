---
layout: post
category: ruby
title: ruby编程语言-ruby闭包
tags: [note, ruby, beginner, tutorial]
---
{% include JB/setup %}
<h4>一、什么是闭包</h4>
<strong>闭包是能够读取其他函数内部变量的函数（或者语法）。</strong>

<pre>
    def multiply(data, n)
      data.collect {|x| x*n}
    end
    puts multiply([1,2,3], 2)
</pre>

代码块变成一个proc或者lambda

<pre>
    def multiply(n)
      lambda {|data| data.collect {|x| x*n} }
    end
    doubler = multiplier(2)
    puts doubler.call([1,2,3])
</pre>

<h4>二、函数的内部变量始终保存在内存中</h4>

<pre>

    def accessor_pair(initialValue = nil)
    	value = initialValue
    	getter = lambda { value}
    	setter = lambda {|x| value = x }
    	return getter, setter
    end
    getX, setX = accessor_pair(0)
    puts getX[]
    setX[10]
    puts getX[]

</pre>

在ruby1.8中，代码块参数并不总是限定在代码块内访问。

<pre>
   def multiply(*args)
     x = nil
     args.map {|x| lambda {|y| x*y } } #在ruby1.9中代码块的参数仅在代码块可见。
   end
   double, triple = multipliers(2, 3)
   puts double.call(2) # prints 6 in Ruby 1.8
</pre>

如果在循环中创建lambda，并在lambda中使用一个循环变量（比如数组索引），这个循环变量还是会在代码块外访问的。

<pre>
    def multipliers(*args)
      x = nil
      for x in args
        lambda {|y| x*y }
      end
    end
    double, triple = multipliers(2, 3)
    puts double.call(2) # prints 6 in ruby 1.8, 1.9
</pre>


<h4>三、绑定</h4>
<pre>
    def multiplier(n)
      lambda {|data| data.collect {|x| x*n } }
    end
    doubler = multiplier(2)
    puts doubler.call([1, 2, 3])
    eval("n=3", doubler.binding) #或者 doubler.binding.eval("n=3")
    puts doubler.call([1, 2, 3])
</pre>

