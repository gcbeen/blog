---
layout: post
category: ruby
title: ruby编程-ruby平台-上
tags: [note, ruby, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、字符串</h4>
字符串的方法

<pre>
    s = "hello"
    s.concat(" world")             # << 追加字符，返回新的字符串。
    s.insert(5 " there")           # s[5] = " there" 改变原来的字符串, 返回一个新的字符串。
    s.slice(0, 5)                  # Same as s[0, 5]。
    s.slice!(5, 6)                 # Deletion. s[5, 6] = ""。返回删除后的子串。
    s.eql?("hello world")          # True ==
</pre>

获取字符串长度
<pre>
    s.length              # => 5 字符数 在ruby 1.9, 字节数 在ruby 1.8。 
    s.size                # => 5 length同义词
    s.bytesize            # => 5 字节长度 只出现在ruby1.9中
    s.empty?              # => false
    ''.empty?             # => true
</pre>

String的查找和替换文本的方法。

<pre>
    s = "hello"
    s.index('l')           # => 2 第一个字符l的位置
    s.index(?l)            # => 2
    s.index(/l+/)          # => 2 
    s.index('l', 3)        # => 3 从第4个字符开始（包括）第一个l字符位置
    s.index('Ruby')        # => nil 匹配ruby字符串
    s.rindex('l')          # => 3 从右数第一个l字符的位置
    s.rindex('l', 2)       # => 2 从右边第三个数起（包括）第一个l字符的位置
</pre>

<pre>
    s.start_with? 'hell'   # => true
    s.end_with? 'bells'    # => false
</pre>

<pre>
    s.include?("ll")      # => true
    s.include?(?H)        # => false
</pre>

<pre>
    s =~ /[aeiou]{2}/                 # => nil
    s.match(/[aeiou]/) {|m| m.to_s }  # => "m"
</pre>

<pre>
    "this is it".split       # => ["this", "is", "it"]
    "hello".split('l')       # => ["he", "", "o"]
    "1, 2,3".split(\,\s*)    # => ["1", "2", "3"]
</pre>

<pre>
    "banana".partition("an")    # => ["b", "an", "ana"]
    "banana".rpartition("an")   # => ["ban", "an", "a"]
    "a123b".partition(/\d+/)    # => ["a", "123", "b"]
</pre>

<pre>
    s.sub("l", "L")              # => "heLlo"
    s.gsub("l", "L")             # => "heLLo"
    s.sub!(/(.)(.)/, '\2\1')     # => "ehllo"
    s.sub!(/(.)(.)/, "\\2\\l")   # => "hello"  双引号使用双
    
    "hello world".gsub(/\b./){|match| match.upcase }  # => "Hello World"
</pre>

<pre>
    s = "world"       
    s.upcase          # => "WORLD"
    s.upcase!         # => "WORLD" 修改字符串本身
    s.downcase        # => "world"
    s.capitalize      # => "World"
    s.capitalize!     # => "World" 修改字符串本身
    s.swapcase        # => "wORLD" 修改每个字符的大小写
    
    # 
    "world".casecmp("WORLD")   # => 0
    a.casecmp("B")             # => -1
</pre>

<pre>
    s = "hello\r\n"       
    s.chomp!              # => "hello"
    s.chomp               # => "hello"
    s.chomp!              # => nil
    s.chomp("o")          # => "hell"
    $/ = ";"              # 
    "hello;".chomp        # => "hello"
</pre>

<pre>
    s = "hello\n"
    s.chop!               # => "hello"
    s.chop                # => "hell"
    "".chop               # => ""
    "".chop!              # => nil

    s = "\t hello \n"     
    s.strip               # => "hello"
    s.lstrip              # => "hello \n"
    s.rstrip              # => "\t hello"
</pre>

<pre>
    s = "x"
    s.ljust(3)             # => "x  "
    s.rjust(3)             # => "  x"
    s.center(3)            # => " x "
    s.center(5, '-')       # => "--x--"
    s.center(7, '-=')      # => "-=-x-=-"
</pre>

<pre>
    s = "A\nB"
    s.each_byte { |b| print b, " " }                  # prints "65 10 66"
    s.each_line { |l| print l.chomp }                 # prints "AB"
    
    # Works in Ruby 1.9, or in 1.8 with the jcode library : 
    s.each_char { |c| print c, " " }                  # prints "A \n B"

    # 在ruby 1.8中使用多字节字符无效
    0.upto(s.length - 1) { |n| print s[n, 1], " " }   # prints "A \n B"
    
    s.bytes.to_a       # => [65, 10, 66]
    s.lines.to_a       # => ["A\n", "B"]
    s.chars.to_a       # => ["A", "\n", "B"]
</pre>

<pre>
    "10".to_i      # => 10
    "10".to_i(2)   # => 2
    "10x".to_i     # => 10
    " 10".to_i     # => 10
    "ten".to_i     # => 0
    "10".oct       # => 8
    "10".hex       # => 16
    "0xff".hex     # => 255
    " 1.1 dozen".to_f     # => 1.1
    "6.02e23".to_f        # => 6.02e+23
    
    "one".to_sym    # => :one
    "two".intern    # => :two
</pre>

<pre>
    "a".succ        # => "b"
    "aaz".next      # => "aba"
    "a".upto("e") { |c| print c }   # => "abcde"
    "hello".reverse     # => "olleh"
    "hello\n".dump      # => "\"hello\\n\"" 跳脱特殊字符
    "hello\n".inspect   # => "\"hello\\n\"" 跳脱特殊字符
    
    "hello".tr("aeiou", "AEIOU")  # => "hEllo"
    "hello".tr("aeiou", " ")      # => "h ll "
    
    "hello".sum          # => 532
    "hello".sum(8)       # => 20
    "hello".crypt("ab")  # => "ab10JrMf6tlhw"
    
    "hello".count('aeiou')   # => 2
    "hello".delete('aeiou')  # => "hll"
    "hello".squeeze('a-z')   # => "helo"
    
    "hello".count('a-z', '^aeiou')   # => 3
    "hello".delete('a-z', '^aeiou')  # => "eo"
</pre>

<h4>二、格式化文本</h4>

<pre>
    n, animal = 2, "mice"
    "#{n + 1} blind #{animal}"  # => "3 blind mice"
</pre>

<pre>
    printf('%d blind %s', n + 1, animal)     # Prints '3 blind mice' => nil
    sprintf('%d blind %s', n + 1, animal)    # => '3 blind mice'
	'%d blind %s' % [n + 1, animal]          # => '3 blind mice'
</pre>

<pre>
    # 格式化文本
    '%d' % 10 # => '10' # => %d整数
    '%x' % 10 # => 'a'  # => %x 十六进制数
    '%X' % 10 # => 'A'  # => %X 十六进制数
    '%o' % 10 # => '12' # => %o 八进制数
	'%f' % 1234.567   # => '1234.567000'
	'%f' % 1.567      # => '1.567000'
	'%e' % 1234.567   # => '1.234567e+03'
	'%E' % 1234.567   # => '1.234567e+03'
	'%g' % 1234.567   # => '1234.57'
	'%g' % 1.23456E12 # => '1.23456e+12'
    
    # 长度
	'%5s' % '<<<'   # '  <<<'
	'%-5s' % '>>>'  # '>>>  '
	'%5d' % 123     # '  123'
	'%05d' % 123    # '00123'

    # 精确度
	'%.2f' % 123.456   # '123.46'
	'%.2e' % 123.456   # '1.23e+02'
	'%.6e' % 123.456   # '1.234560e+02'
	'%.4g' % 123.456   # '123.5'

    # 长度 精确度
    '%6.4g' % 123.456  # ' 123.5'
	'%3s' % 'ruby'     # 'ruby'
	'%3.3s' % 'ruby'   # 'rub'  force 3
    
    # 格式化多个参数
	args = ['Syntax Error', 'test.rb', 20]
	"%s: in '%s' line %d" % args           # "Syntax Error: in 'test.rb' line 20"
	"%2$s:%3$d: %1$s" % args               # "test.rb:20: Syntax Error"
</pre>

<h4>三、打包和解包二进制字符串</h4>
Ruby字符串不仅可以存放文本数据，也可以存放二进制数据。

<pre>

    a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    b = a.pack('i10')
    c = b.unpack('i*')
    c == a
    
    m = 'hello world'
    data = [m.size, m]
    template = 'Sa*'
    b = data.pack(template)
    b.unpack(template)

</pre>

<h4>四、字符串和编码</h4>

*   ruby 1.8中，字符串是一个字节序列，字符串里的单个元素不是字符，而是数字，即实际的字节值或字符编码值
*   ruby 1.9中，字符串是实实在在的字符序列，而且那些字符不必局限于ASCII字符集。字符串的单个元素是字符（表示长度为1的字符串）。每个字符串都有一种编码方式，它指定了字符串的字节和那些字节所代表的字符之间的关系。比如utf-8编码方式，采用可变数目的字节来表示每个字符。

<pre>
    # -*- coding: utf-8 -*- # 设置utf-8编码
    # 多字节字符x
    s = "2×2=4"
    
    s.length     # => 5: '2'   'x'   '2'    '='    '4' （char）
    s.bytesize   # => 6:  32  c3 97   32    3d     34  （hex）
</pre>

在进行字符串处理时，应尽量使用顺序算法（each_char迭代器）。想自己对子符访问保持高效可以使用ascii_only?实例方法来测定一个字符串是否完全由7位的ASCII字符构成。

Ruby 1.9的String类定义了 encoding 返回字符串的编码方式

<pre>
    # -*- coding: utf-8 -*-
    s = "2×2=4"
    s.encoding    # => <Encoding: UTF-8>
    t = "2+2=4"
    t.encoding    # => <Encoding: ASCII-8BIT>
</pre>

force_encoding

<pre>
    text = stream.readline.force_encoding('utf-8')
    bytes = text.dup.force_encoding(nil)            # nil encoding就是binary
    
    s = "\xa4".force_encoding('utf-8')    
    s.valid_encoding?                     # => false
</pre>

encode（encode!）

<pre>
    # -*- coding: utf-8 -*-
    euro1 = "\u20AC"
    puts euro1         # prints " "
    euro1.encoding     # => <Encoding: UTF-8>
    euro1.bytesize     # => 3
    
    euro2 = euro1.encode("iso-8859-15")
    puts euro2.inspect   # prints "\xA4"
    euro2.encoding       # => <Encoding:iso-8859-15>
    euro2.bytesize       # => 1

	euro3 = euro2.encode('utf-8')
	euro1 == euro3                 # => true
</pre>

<pre>
    byte = "\xA4"
    char = byte.encode("utf-8", "iso-8859-15")
    
    # 或者
    text = bytes.encode(to, from)
    text = bytes.dup.force_encoding(from).encode(to)


	"\u20AC".encode("iso-8859-1")
</pre>

<h4>五、正则表达式</h4>

*   Regexp字面量

<pre>
    /Ruby?/
    /ruby?/i
    /./mu
</pre>

<pre>
    修饰符    描述
      i       匹配文本是忽略大小写
      m       跨行进行模式匹配。换行符被当作普通字符对待：。可以匹配换行符
      x       扩展句法：允许在正则表达式中放入空白符和注释
      o       对#{}这样的插入仅仅在对正则表达式字面量第一次求值时被执行一次
      u,e,s,n 把正则表达式解释为utf-8,euc,sjis或ascii。
	          如果没有这样的修饰符，则对正则表达式使用源文件的编码方式
</pre>

正如字符串字面量可以用%Q分隔一样，Ruby的正则表达式也可以用%r后跟分割符来定义。当正则表达式中有许多斜杠符时，如果不想使用转义符，这会很有用处：

<pre>
    %r |/|
    %r [</(.*)>]i
</pre>

<pre>
    money = /[$\u20AC\u{a3}\u{a5}]/

	prefix = ","
	/#{prefix}\t/
</pre>
插入表达式在每次对正则表达式字面量求值前被执行一次。

如果使用了o修饰符，这个插入表达式仅仅在第一次解析时被执行。

<pre>
    [1, 2].map { |x| /#{x}/ }    # => [/1/, /2/]
    [1, 2].map { |x| /#{x}/o }   # => [/1/, /1/]
</pre>

<h4>六、Regexp的工厂方法</h4>

使用Regexp.new（或Regexp.compile）来创建一个正则表达式：

<pre>
    
    Regexp.new("Ruby?")
    Regexp.new("ruby?",Regexp::IGNORECASE)
    Regexp.compile(".", Regexp::MULTILINE, "u")
    
</pre>

把一个字符串传给Regexp的构造方法前，你可以用Regexp.escape对字符串中的特殊字符进行转义：

<pre>
    pattern = "[a-z]+"                
    suffix = Regexp.escape(" () ")    
    r = Regexp.new(pattern + suffix)  # /[a-z]+\(\)/
</pre>

在Ruby 1.9中，工厂方法Regexp.union可以创建一个“联合”了任意多字符串或Regexp对象的模式。

<pre>
    
    pattern = Regexp.union("Ruby", "Perl", "Python", /Java(Script)?/ )
    pattern = Regexp.union([ "Ruby", "Perl", "Python", /Java(Script)?/ ])
    
    Regexp.union("()", "[]", "{}")  # => /\(\)|\[\]|\{\}/
    
</pre>

<h4>七、正则表达式句法</h4>

<pre>
    /ruby/
    /￥/
</pre>

<pre>
    /[Rr]uby/
    /rub[ye]/
</pre>

<pre>
    /<.*>/
    /<.*?>/
</pre>

<pre>
    /([Rr])uby&\1ails/
    /(['"])[^\1]*\1/ #'
</pre>

<pre>
    /(?&ltfirst&gt\w)(?&ltsecond&gt\w)\k&ltsecond&gt\k&ltfirst&gt/
    /(?'first'\w)(?'second'\w)\k'second'\k'first'/
</pre>

<pre>
    /\ARuby\Z/
    /\bRuby\b/
    /\brub\B/
</pre>

<pre>
    /Ruby(?=!)/   # 匹配Ruby或Ruby!
    /Ruby(?!!)/   # 匹配Ruby或者Ruby非!字符
</pre>

<pre>
    /R(?#comment)/  # 匹配R或者R#comment
    /R(?i)uby/      # 匹配Ruby或者ruby（不区分大小写）
    /R(?i:uby)/     # 匹配Ruby或者ruby
    /rub(?:y|le)/   # 匹配ruby或者ruble
</pre>

<pre>
    # option x 忽略注释和空白
    / #  the ignore comments
      #  the ignore comments
      R       # 匹配R
      (uby)+  # 加上一个或多个uby
      \       # 加上空白
      /x      #
</pre>

句法规则：

<pre>
    .       匹配出换行符外所有单个字符。如果使用m选项，也将匹配换行符。
    [...]   匹配方括号内的所有单个字符。
    [^...]  匹配所有不在方括号中的单个字符。
    \w      匹配可用做单词的字符。
    \W      匹配不可用作单词的字符。
    \s      匹配空白字符，等价于[ \t\n\r\f]。
    \S      匹配非空白字符。
    \d      匹配数字字符。等价于[0-9]。
    \D      匹配非数字字符。
</pre>

序列、替代、分组和引用

<pre>
    ab
    a|b
    (re) 
    (?:re)          # 用()进行分组，但是不捕获匹配文本
    (?<name> re)    # 用()进行分组并捕获匹配文本，把子表达式标记为name（Ruby 1.9）
    (?'name' re)    # 同(?<name> re)
    \1...\9         # 匹配与第n组子表达式所匹配文本相同的文本。
    \10...          # 如果先前有足够多的子表达式，匹配与第n组子表达式所匹配文本相同的文本；
	                # 否则，用给定的八进制编码匹配字符
    \k<name>        # 引用分组名
    \g<n>           # 引用分组号
</pre>

<pre>
    # 锚
    \Z          # 匹配字符串尾部，如果字符串以换行符结尾，匹配前一个字符。
    \z          # 匹配字符串尾部。
    \G          # 匹配上次完成时的节点。
    (?= re)     # 匹配字符 re
    (?! re)     # 
    (?<= re)
    (?&lt! re)
</pre>

<pre>
    # 杂项
    (? onflags - offflags)
    (? onflags - offflags : x)
    (?#...)
    (?> re)
</pre>

