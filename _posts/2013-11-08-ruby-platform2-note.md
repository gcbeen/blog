---
layout: post
category: ruby
title: ruby编程-ruby平台-中
tags: [note, ruby, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、用正则表达式进行模式匹配</h4>

*   =~操作符，一个操作数必须是正则表达式，另一个必须是字符串。

<pre>
    
    pattern = /Ruby?/i
    pattern =~ "backrub"    # => 4
    "rub ruby" =~ pattern   # => 0
    pattern =~ "r"          # => nil
</pre>

<pre>
    # 模式匹配的其他一些信息
    "hello" =~ /e\w{2}/
    $~.string           # => "hello"
    $~.to_s             # => "ell"
    $~.pre_match        # => "h"
    $~.post_match       # => "o"
</pre>

$~是一个特殊的线程局部和方法局部变量，它在两个并发运行的线程中的值是不同的。并且，使用了=~操作符的方法不会修改调用者方法中的$~变量值。可以使用面向对象方式引用（Regexp.last_match）。

*   MatchData对象

<pre>
    pattern = /(Ruby|Perl)(\s+)(rocks|sucks)!/
    text = "Ruby\trocks!"
    pattern =~ text             # => 0
    data = Regexp.last_match    # => 匹配详细
    data.size    # => 4
    data[0]      # => "Ruby\trocks"
    data[1]      # => "Ruby"
    data[2]      # => "\t"
    data[3]      # => "rocks"
    data[1, 2]   # => ["Ruby", "\t"]
    data[1..3]   # => ["Ruby", "\t", "rocks"]
    data.values_at(1, 3)    # => ["Ruby", "rocks"]
    data.captures           # => ["Ruby", "\t", "rocks"]
    
    Regexp.last_match(3)    # => "rocks"
    
    # 开始和结束的匹配
    
    data.begin(0)     # => 0
    data.begin(2)     # => 4
    data.end(2)       # => 5
    data.offset(3)    # => [5, 10]
</pre>

<pre>
    # 在Ruby 1.9中的有名捕获。
    pattern = /(?&ltlang&gtRuby|Perl) (?&ltver&gt\d(\.\d)+) (?&ltreview&gtrocks|sucks)!/
    if (pattern =~ "Ruby 1.9.1 rocks!")
      $~[:lang]         # => "Ruby"
      $~[:ver]          # => "1.9.1"
      $~["review"]      # => "rocks"
      $~.offset(:ver)   # => [5, 10]
    end
    
    pattern.names           # => ["lang", "ver", "review"]
    pattern.named_captures  # => { "lang" => [1], "ver" => [2], "review" => [3] }
</pre>

有名匹配和局部变量

在Ruby 1.9中，如果一个正则表达式字面量包含有名捕获并且出现在=~操作符的左侧，那么捕获分组名将成为局部变量，匹配的文本放入这些变量中。如果匹配失败这些变量将被赋值为nil。

<pre>
    if /(?<lang>\w+) (?<ver>\d+\.(\d+)+) (?<review>\w+)/ =~ "Ruby 1.9 rules!"
      lang      # => "Ruby"
      ver       # => "1.9"
      review    # => "rules"
    end
</pre>

这值针对正则表达式字面量有效。如果一个匹配模式被存储在变量或常量中，或是方法的返回值，或出现在操作符的右侧，那么=~操作符不会进行这种操作。

match方法：返回MatchData对象

<pre>
    if data = pattern.match(text)
      handle_match(data)
    end

    # 在Ruby1.9中，对match的调用关联一个代码块。
    pattern.match(text) { |data| handle_match(data) }

</pre>

用于匹配数据的全局变量

<pre>
    # 特殊的正则表达式变量
    $~      # 等价于 Regexp.last_match
    $&      # 等价于 Regexp.last_match[0]
    $`      # 等价于 Regexp.last_match.pre_match
    $'      #'等价于 Regexp.last_match.post_match
    $1      # 等价于 Regexp.last_match[1]
    $2, etc # 等价于 Regexp.last_match[2], etc
    $+      # 等价于 Regexp.last_match[-1]
</pre>

$~和这些从它继承而来的变量都是线程局部且方法局部的，两个Ruby线程可以同时进行模式匹配，无需担心相互干扰。

*   用字符串进行模式匹配

<pre>
    "ruby123"[/\d+/]                 # "123"
    "ruby123"[/([a-z]+) (\d+)/, 1]   # "ruby"
    "ruby123"[/([a-z]+) (\d+)/, 2]   # "123"
</pre>
slice方法是字符串索引操作符[]的同义词方法。slice!方法返回值与slice方法相同，并且将从字符串中删除返回的匹配字符串。

<pre>
    r = "ruby123"
    r.slice!(/\d+/)  # => "123", r："ruby"
</pre>

<pre>
    s = "one, two, three"
    s.split               # ["one,", "two,", "three,"]
	s.split(", ")         # ["one", "two", "three"]
	s.split(/\s*,\s*/)    # ["one", "two", "three"]
</pre>

index方法可以搜索一个字符、子字符串或模式，并返回起始位置。如果给定一个Regexp对象作参数，则该方法的行为与=~操作符相似，不过它还可以用第二个参数指定搜索的起始位置，这样就可以在搜索完第一个之后，继续搜索下一个匹配值。

<pre>
    text = "hello world"
    pattern = /l/
    first = text.index(pattern)
    n = Regexp.last_match.end(0)
    second = text.index(pattern, n)
    last = text.rindex(pattern)
</pre>

搜索并替换（sub，gsub，sub!，gsub!）
最重要的使用正则表达式的String方法是sub（用于替换）和gsub（用于全局替换），以及它们的变体sub!和gsub!方法,如果发生替换，返回修改的字符，否则返回nil。

<pre>
    phone = gets
    phone.sub!(/#.*$/, "")
    phone.gsub!(/\D/, "")
    
    text.gsub!("rails", "Rails")
    
    text.gsub!(/\brails\b/, "Rails")
    
    # 使用匹配文本（\0），使用匹配的第一个字串（\1）
    text.gsub(/\bruby\b/i, '&ltb&gt\0&lt/b&gt')
    
    # 插入将在字符串字面量中被执行，这时字符串没有被传递给gsub方法，插入这个动作发生在模式匹配发生之前。$&变量要么没定义，要么是前次匹配所遗留的值。
    text.gsub(/\bruby\b/i, "&ltb&gt#{$&}&lt/b&gt")
    
    re = /(?&ltquote&gt['"]) (?&ltbody&gt[^"']*)\k&ltquote&gt/
    puts "These are 'quotes'".gsub(re, '\k&ltbody&gt')
</pre>

替换字符串也可以引用不在捕获分组中的文本，可以用\&、\`、\'和\+来替换值为$&、$`、$'和$+的文本。

动态计算替换字符串

<pre>
    text = "RUBY Java perl PyThOn"
    lang = /ruby|java|perl|python/i
    text.gsub!(lang) {|l| l.capitalize}
</pre>

<pre>
    pattern = /(['"])([^\1]*)\1/  #'
    text.gsub!(pattern) do
      if ($1 == '"')
        "'#{$2}'"
      else
        "\"#{$2}\""
      end
    end
</pre>

正则表达式编码

在Ruby 1.9中，Regexp对象有一个与字符串类似的encoding方法。可以用修饰符显示指定编码：

<pre>
    u表示UTF-8编码
    s表示SJIS编码
    e表示EUC-JP编码
    n表示无编码
</pre>
还可以在正则表达式中用\u转义字符显示指定UTF-8编码。如果不显示指定编码，则使用源程序编码，如果正则表达式中所有字符都是ASCII编码，那么即使源程序编码是ASCII编码的超集，也使用ASCII编码。

如果试图匹配的模式与文本编码不兼容，在Ruby 1.9中会抛出一个异常。如果Regexp对象的编码不是ASCII编码，fixed_encoding?方法会返回true。如果fixed_encoding?返回false，那么可以安全地使用这个模式来匹配编码为ASCII或ASCII超集的文本。

<h4>二、数字和数学运算</h4>

*   数字相关的方法

<pre>
    # 通用方法
    0.zero?      # => true
    1.0.zero?    # => false
    0.0.nonzero? # => nil（像false一样使用）
    1.nonzero?   # => 1（像true一样工作）
    1.integer?   # => true
    1.0.integer? # => false
    1.scalar?    # => false：不是复数
    1.0.scalar?  # => false：不是复数
    Complex(1, 2).scalar?  # => true：复数
</pre>

<pre>
    # 整数方法
    0.even?  # => true
    0.odd?   # => false
</pre>

<pre>
    # 浮点数方法
    ZERO, INF, NAN = 1.0/0.0, 0.0/0.0
    
    # 有限的数字
    ZERO.finite?  # => true
    INF.finite?   # => false
    NAN.finite?   # => false
    
    # 无限的数字
    ZERO.infinite?  # => nil
    INF.infinite?   # => 1
    -INF.infinite?  # => -1
    NAN.infinite?   # => nil
    
    # 非数字
    ZERO.nan? # => false
    INF.nan?  # => false
    NAN.nan?  # => true
</pre>

<pre>
    # 取整数方法
    1.1.ceil   # => 2
    -1.1.ceil  # => -1
    
    1.9.floor  # => 1
    -1.9.floor # => -2
    
    1.1.round   # => 1
    0.5round    # => 1
    -0.5.round  # => -1
    
    1.1.truncate # => 1
    -1.1.to_i    # => -1 truncate同义词
</pre>

<pre>
    # 浮点数的方法
    
    -2.0.abs      # => 2.0
    -2.0 <=> 0.0  # => -1
    
    # 常量
    Float::MAX     # => 1.79769313486232e+308
    Float::MIN     # => 2.2250738585072e-308
    Float::EPSILON # => 2.22044604925031e-16
</pre>

<h4>三、Math模块</h4>

<pre>
    # 常量
    Math::PI
    Math::E
    
    # 方根
    Math.sqrt(25.0)    # => 5.0
    27.0 ** (1.0/3.0)  # => 3.0
    
    # 对数
    Math.log10(100.0)        # => 2.0
    Math.log(Math::E ** 3)   # => 3.0
    Math.log2(8)     # => 3.0
    Math.log(16, 4)  # => 2.0
    Math.exp(2)      # => 7.38905609893065  和Math::E ** 2一样
</pre>

<pre>
    # 三角函数
    include Math
	sin(PI/2)     # => 1.0
	cos(0)        # => 1.0
	tan(PI/4)     # => 1.0
	asin(1.0)/PI  # => 0.5
	sinh(0)       # => 0.0
	asinh(1.0)    # => 0.0
	theta = atan2(y, x)   # 
	r = hypot(x, y)       # 

	f, e = frexp(1024.0)  # => [0.5, 11]
	x = ldexp(f, e)       # => 1024： x = f * 2 ** e

	erf(0.0)   # => 0.0
	erfc(0.0)  # => 1.0
</pre>

<h4>四、数字运算</h4>

BigDecimal替代Float。BigDecimal对象的有效数字可以任意长，并且大小不受限制。在进行计算时，它们可以提供比舍入方式更好的精度控制。

<pre>
    require "bigdecimal"
    dime = BigDecimal("0.1")
    4 * dime - 3 * dime == dime  # => true
    4 * 0.1 - 3 * 0.1 == 0.1     # => false
    
    BigDecimal.mode(BigDecimal::ROUND_MODE, BigDecimal::ROUND_HALF_EVEN)
    BigDecimal.limit(20)
    principal = BigDecimal("200000")
    apr = BigDecimal("6.5")
    years = 30
    payments = years * 12
    interest = apr/100/12
    x = (interest + 1) ** payments
    monthly = (principal * interest * x) / (x -1)
    monthly = monthly.round(2)
    monthly = monthly.to_s("f")
</pre>

<h4>五、复数</h4>

<pre>
    require 'complex'
    c = Complex(0.5, -0.2)     # => 0.5 - 0.2i
    z = Complex.new(0.0, 0.0) 
    10.times { z = z * z + c}
    magnitude = z.abs
    x = Math.sin(z)
    Math.sqrt(-1.0).to_s           # => "1.0i"
    Math.sqrt(-1.0) == Complex::I  # => true
</pre>

<h4>六、实数</h4>
标准库中的Rational类表示实数（两个整数之商），mathn库重定义了整数除法，用于创建实数。mathn还做了许多工作用于统一Ruby的算术运算，并使Integer、Rational和Complex类协同工作。

<pre>
    require 'rational'
    penny = Rational(1, 100)  # => "1/100"
    require 'mathn'
    nickel = 5 / 100
    dime = 10 / 100
    quarter = 1 / 4
    change = 2 * quarter + 3 * pnny  # Rational result：53/100
    (1/2 * 1/3).to_s                 # "1/6"
</pre>

<h4>七、向量和矩阵</h4>
matrix库定义了Matrix和Vector类，分别代表数字矩阵和向量，以及对它们进行算术运算的操作符。

<pre>
    require 'matrix'
    
    unit = Vector[1, 1]
    
    identity = Matrix.identity(2)  # 2x2 matrix
    identity * unit == unit        # true
    
    sx, sy = 2.0, 3.0
    scale = Matrix[[sx, 0], [0, sy]]
    scale * unit                     # [2.0, 3.0]
    
    theta = Math::PI/2
    rotate = Matrix[[Math.cos(theta), -Math.sin(theta)], [Math.sin(theta), Math.cos(theta)]]
    
    rotate * unit        # [-1.0, 1.0]
    scale * (rotate * unit)  # [-2.0, 3.0]
</pre>

<h4>八、随机数</h4>
在Ruby中，用全局函数Kernel.rand产生随机数。如果不带参数，它返回一个大于等于0.0而小于1.0的伪随机浮点数。

<pre>
    rand      # => 0.4682060936325654
    rand      # => 0.6254414462555852
    rand(100) # => 81
    rand(100) # => 32
</pre>

可重复的伪随机数序列

<pre>
    srand(0)
    [rand(100), rand(100)]  # => [44, 47]
    srand(0)
    [rand(100), rand(100)]  # => [44, 47]
</pre>

<h4>九、日期和时间</h4>
Time类用于表示日期和时间。它是操作系统提供的日期和时间的一个简单包装，因此，在某些系统上，这个类不能用于表示1790年之前或2038年之后的日期。在date库中的Date和DateTime类则没有这个限制。

<pre>
    Time.now  # => 返回现在的时间
    Time.new  # => Time.now同义词
    Time.local(2007, 7, 8)         # July 8, 2007
    Time.local(2007, 7, 8, 9, 10)  # July 8, 2007, 09:10am, local time
    Time.utc(2007, 7, 8, 9, 10)    # July 8, 2007, 09:10 UTC
    Time.gm(2007, 7, 8, 9, 10, 11) # July 8, 2007, 09:10:11 GMT (same as UTC)
</pre>

<pre>
    t = Time.utc(2000, 12, 31, 23, 59, 59, 999999)
    t.day   # => 31
    t.wday  # => 0（0代表星期天）
    t.yday  # => 366
    t.usec  # => 999999 微妙
    t.zone  # => "UTC"
    
    values = t.to_a  # => [59, 59, 23, 31, 12, 2000, 0, 366, false, "UTC"]
    
    values[5] += 1   
    Time.utc(*values) # => Mon Dec 31 23:59:59 UTC 2001
    
    # 时区
    t.zone        # => "UTC"
    t.utc?        # => true
    t.utc_offset  # => 0
    t.localtime   # 转换为本地时间
    t.zone        # "PST"
    t.utc?        # false
    t.utc_offset  # -28800
    t.gmtime      # 转换为utc
    t.getlocal    # 返回一个新的本地时间
    t.getutc      # 返回一个新的utc时间
    t.isdst       # => false  utc没有夏时制
    t.getlocal.isdst  # => false 没有夏时制
    
    # 星期函数
    t.sunday?  # => true
    t.monday?  # => false
    t.tuesday? # => false
</pre>

<pre>
    
    t.to_s   # => "Sun Dec 31 23:59:59 UTC 2000" Ruby 1.8
    t.to_s   # => "2000-12-31 23:59:59 UTC"      Ruby 1.9
    t.ctime  # => "Sun Dec 31 23:59:59 2000"
    
    # 格式化时间
    t.strftime("%Y-%m-%d %H:%M:%S") # => "2000-12-31 23:59:59"
    t.strftime("%H:%M")             # => "23:59"
    t.strftime("%I:%M %P")          # => "11:59 PM" 12小时制
    
    t.strftime("%A, %B %d")    # => "Sunday, December 31"
    t.strftime("%a, %b %d %y") # => "Sun, Dec 31 00"
    t.strftime("%x")   # => "12/31/00"
    t.strftime("%X")   # => "23:59:59"
    t.strftime("%c")   # "Sun Dec 31 23:59:59 2000"
</pre>

<pre>
    require 'parsedate'
    include ParseDate
    
    datestring = "2001-01-01"
    values = parsedate(datestring)  # => [2001, 1, 1, nil, nil, nil, nil, nil]
    t = Time.local(*values)         # => Mon Jan 01 00:00:00 -0800 2001
    s = t.ctime                     # => "Mon Jan 1 00:00:00 2001"
    Time.local(*parsedate(s)) == t  # => true
    
    s = "2001-01-01 00:00:00-0500"  
    v = parsedate(s)       # => [2001, 1, 1, 0, 0, 0, "-500", nil]
    t = Time.local(*v)     # 
    
    now = Time.now
    past = now - 10     # 10秒之前
	future = now + 10   # 10秒之后
    future - now        # => 10
    
    past <=> future
    past < future
    now >= future
    now == now
</pre>

时间工具方法
<pre>
    class Numeric
      def milliseconds; self/1000.0; end
      def seconds; self; end
      def minutes; self*60; end
      def hours; self*60*60; end
      def days; self*60*60*24; end
      def weeks; self*60*60*24*7; end
    
      def to_milliseconds; self*1000; end
      def to_seconds; self; end
      def to_minutes; self/60.0; end
      def to_hours; self/(60*60.0); end
      def to_days; self/(60*60*24.0); end
      def to_weeks; self/(60*60*24*7.0); end
    end
    
    expires = now + 10.days   # => 10小时后
    expires - now             # => 864000.0 秒
    (expires - now).to_hours  # => 240.0 小时
    
    t = Time.now.to_i  # => 1384160886
    Time.at(t)         # => 2013-11-11 17:08:06 +0800
    t = Time.now.to_f  # => 1384160954.451236
    Time.at(0)         # => 1970-01-01 08:00:00 +0800
</pre>

<h4>十、集合</h4>
Enumerable模块是一种混入模块，它在each迭代器的基础上实现了一组有用的方法。Array、Hash和Set类都包含了Enumerable模块。

一些可枚举的类有自然的枚举顺序，它们的each方法遵循这个顺序。数组按照索引的升序枚举各个元素，Range对象按照升序枚举元素，IO对象按照对应的文件和socket读入的文本顺序枚举每一行，在Ruby 1.9中，Hash和Set按照元素插入的顺序进行枚举，在Ruby 1.9前，这些类是随意的顺序进行枚举的。

许多Enumerable的方法返回一个处理后的可枚举集合或一个子集。通常情况下，如果一个Enumerable方法返回一个集合，这个集合是一个Array对象。Hash类覆盖了reject方法，返回一个Hash对象。

对集合进行迭代和转换

<pre>
    (5..7).each { |x| print x }
    (5..7).each_with_index{ |x, i| print x, i }
</pre>

在Ruby 1.9中，Enumerable定义了cycle迭代器，它重复迭代集合中的每个元素，无限循环一直到给定的代码块用break、return或者抛出异常来明确中止这个迭代。在对Enumerable对象进行第一次迭代时，cycle把所有元素存储在一个数组中，以后的迭代将在数组中进行。

each_slice和each_cons迭代器

<pre>
    (1..10).each_slice(4) { |x| print x }  # => prints "[1, 2, 3, 4][5, 6, 7, 8][9, 10]"
    (1..5).each_cons(3) {|x| print x}      # => prints "[1, 2, 3][2, 3, 4][3, 4, 5]"
</pre>

<pre>
    data = [1, 2, 3, 4]
    roots = data.collect {|x| Math.sqrt(x) }
    words = %w[hello world]
    upper = words.map {|x| x.upcase}  # collect同义词
</pre>

<pre>
    (1..3).zip([4, 5, 6]) { |x| print x.inspect }  # => "[1, 4][2, 5][3, 6]"
    (1..3).zip([4, 5, 6], [7, 8]) { |x| print x }  # => "14725836"
    (1..3).zip('a'..'c') { |x, y| print x, y}      # => "1a2b3c"
</pre>

<pre>
    # 将可枚举集合转换为一个数组
    (1..3).to_a     # => [1, 2, 3]
    (1..3).entries  # => [1, 2, 3] to_a同义词
    
    # 
	require 'set'
	(1..3).to_set
</pre>

枚举器和外部迭代器

在Ruby 1.9中枚举器是用Enumerable::Enumerator类来实现。在Ruby 1.8中可以通过包含enumerator库来得到。

<pre>
    # 通过to_enum来创建一个Enumerator对象
    e = [1..10].to_enum
    
    # 通过enum_for来创建一个Enumerator对象
    e = "test".enum_for(:each_byte)
    
    # 直接用不带代码块的方式调用iterator方法
    e = "test".each_byte
</pre>

枚举器作为外部迭代器被使用

<pre>
    "Ruby".each_char.max     # => "y"
    iter = "Ruby".each_char  # => 
    loop do print iter.next; end 
    print iter.next   # prints "R"
    iter.rewind       # 
    print iter.next   # prints "R"
</pre>

<pre>
    "Ruby".each_char.with_index.each do |c, i| puts "#{i}: #{c}"; end
</pre>
