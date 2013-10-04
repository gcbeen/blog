---
layout: post
category: javascript
title: Javascript权威指南-正则表达式的模式匹配
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}
<h4>一、正则表达式</h4>
正则表达式是一个描述字符模式的对象。

<pre>
    var pattern = /s$/;
	var pattern = new RegExp("s$");
</pre>
在正则表达式中，许多标点符号具有特殊含义。

<pre>
    ^ $ . * + ? = ! : | \ / ( ) [ ] { }
</pre>

<h4>二、字符类</h4>

<pre>
    /[abc]/
	/[^abc]/
	/[a-z]/
	/[a-zA-Z0-9]/
</pre>

正则表达式的字符类

<pre>
    [...]  方括号内的任意字符
    [^...] 不再方括号内的任意字符
    .      除换行符和其他Unicode行终止符之外的任意字符
    \w     任何ASCII字符组成的单词，等价于[a-zA-Z0-9]
    \W     任何不是ASCII字符组成的单词，等价于[^a-zA-Z0-9]
    \s     任何Unicode空白符
    \S     任何非Unicode空白符的字符，注意\w和\S不同
    \d     任何ASCII数字，等价于[0-9]
    \D     除了ASCII数字之外的任何字符，等价于[^0-9]
    [\b]   退格直接量（特例）
</pre>

<h4>三、重复的正则语法</h4>

<pre>
    {n,m}  匹配前一个模式至少n次，但不能超过m次
    {n,}   匹配前一个模式n次或者更多次
    {n}    匹配前一个模式n次
    ?      匹配前一个模式0次或者1次，等价于{0,1}
    +      匹配前一个模式1次或者多次，等价于{1,}
    *      匹配前一个模式0次或者多次，等价于{0,}
</pre>
<pre>
    /\d{2,4}/
	/\w{3}\d?/
	/\s+java\s+/
	/[^(]*/
</pre>

一般匹配重复字符是尽可能多地匹配，而且允许后续的正则表达式继续匹配。称之为“贪婪的”匹配。
非贪婪的匹配，只须在代匹配的字符后跟一个问好即可：“??”、“+?”、“*?”或“{1,5}?”。

<pre>
    "aaab"  /a+b/
	/a+?b/
</pre>

<h4>四、选择、分组和引用</h4>

*   |：选择，匹配的是该字符左边的子表达式或右边的子表达式。

<pre>
    /ab|cd|ef/ 
	/\d{3}|[a-z]{4}/ 
</pre>
选择项是从左到右直到发现匹配项。如果左边的选择项匹配，就忽略右边的匹配项。

*   (...)：组合，把几个单独的项组合成一个单元。
<pre>
    /java(script)?/
	/(ab|cd)+|ef/
</pre>
这个单元可以通过“|”、“*”、“+”或者“?”等符号加以修饰，而且可以记住和这个组合相匹配的字符串以供此后引用使用。

*   (?:...)： 只组合，把项组合到一个单元，但不记忆与该组相匹配的字符。

<pre>
    /([Jj]ava(?:[Ss]cript)?)\sis\s(fun\w*)/
</pre>
*   \n：      和第n个分组第一次匹配的字符相匹配，组是圆括号中的子表达式，组索引是从左到右的左括号数，“(?:”形式的分组不编码。

<pre>
    /['"][^'"]*['"]/
	引用
	/(['"])[^"']\1/
	
	/([Jj]ava([Ss]cript)?)\sis\s(fun\w*)/
	\2 指代([Ss]cript) # 左括号的位置
</pre>
正则表达式不允许双引号括起的内容中有单引号，也不允许单引号括起的内容有双引号。

<h4>五、指定匹配位置</h4>
指定匹配发生的合法位置的字符，我们称之为正则表达式的锚。

*   ^      匹配字符串的开头，在多行检索中，匹配一行的开头

<pre>
    /^JavaScript$/
</pre>
*   $      匹配字符串的结尾，在多行检索中，匹配一行的结尾
*   \b     匹配一个单词的边界，简言之，就是位于字符\w和\W之间的位置，或位于字符\w和字符串的开头或者结尾之间的位置

<pre>
    /\bJava\b/
</pre>
*   \B     匹配非单词边界的位置

<pre>
    /\B[Ss]cript/ # 匹配 "JavaScript","postscript" 不匹配 "script","Scripting" 
</pre>
*   (?=p)  零宽正向先行断言，要求接下来的字符都与p匹配，但不能包括匹配p的那些字符

<pre>
    /[Jj]ava([Ss]cript)?(?=\:)/ # 匹配 "JavaScript: The Definitive Guide" 不匹配 "Java in a Nutshell"
</pre>
*   (?!p)  零宽负项先行断言，要求接下来的字符不与p匹配

<pre>
    /Java(?! Script)([A-Z]\w*)/ #匹配 "JavaBeans", "JavaScript" 不匹配 "Javanese", "JavaScripter"
</pre>

<h4>六、修饰符</h4>

<pre>
    i    执行不去分大小写的匹配
	g    执行一个全局匹配，简言之，即找到所有的匹配，而不是在找到第一个之后就停止
	m    多行匹配模式，^匹配一行的开头和字符串的开头，$匹配行的结束和字符串的结束
</pre>

<pre>
    "Java\nis fun,\n java".replace(/java$/gim, 'ruby');
    
	结果：
	ruby
    is fun,
    ruby
</pre>
<h4>七、用于模式匹配的String方法</h4>
String支持4种使用正则表达式的方法。

*   search() 参数正则表达式，如果不是，通过RegExp构造函数将它转换成正则表达式，忽略正则表达式参数中的修饰符g。

<pre>
    "Javascript".search(/script/i); # 4 找不到匹配字串 返回-1
</pre>
*   replace()方法用以执行检索与替换操作。第一个参数是一个正则表达式，第二个参数是要进行替换的字符串。
如果正则表达式设置了修饰符g，那么源字符串中所有与模式匹配的子串都将替换成第二个参数指定的字符串；如果正则表达式参数不带修饰符g，只替换所匹配的第一个字符串。如果replace()的第一个参数是字符串，replace直接搜索这个字符串，而不转换成正则表达式。

<pre>
    text.replace(/javascript/gi, "JavaScript");
	// 一段引用文本起始于引号，结束于引号
	// 中间的内容区域不能包含引号
	var quote = /"([^''])"/g;
	text.replace(quote, ' “$1” ');
</pre>
replace()方法的第二个参数可以是函数，该函数能动态地计算替换字符串。

*   match方法是最常用的正则表达式方法。他的唯一参数就是正则表达式，或通过RegExp()构造函数将其转换为正则表达式。返回一个由结果匹配的数组。

<pre>
    a = "1 plus 2 equals 3".match(/\d+/g); 返回 ['1', '2', '3']
	b = "1 plus 2 equals 3".match(/(\d)+*/); 返回 ['1']
	b[0] = "1 plus 2 equals 3"
	b[1] = '1'
</pre>
如果正则表达式没有设置修饰符g，match()就不会进行全局检索，他只检索第一个匹配。但还是返回一个数组。数组的第一个元素是匹配的字符串，余下的元素则是与正则表达式中用圆括号括起来的子表达式相匹配的子串。为了和方法replace()保持一致，a[n]存放的事$n的内容。

<pre>
    var url = /(\w+):\/\/([\w.]+)\/(\S*)/;
	var text = "Visit my blog at http://www.example.com/~david";
	var result = text.match(url);
	if (result != null) {
	    var fullurl = result[0];
		var protocol = result[1];
		var host = result[2];
		var path = result[3];
	}
</pre>

*   split()方法的参数是一个正则表达式。

<pre>
    "1, 2, 3, 4, 5".split(/\s*,\s*/);
</pre>

<h4>八、RegExp对象</h4>

*   RegExp()构造函数。

<pre>
    var zipcode = new RegExp("\\d{5}", "g");
</pre>
在需要动态创建正则表达式的时候，RegExp()构造函数显得非常有用。

*   RegExp的属性

<pre>
    source        只读     字符串   包含正则表达式的文本
	global        只读     布尔值   是否带修饰符g
	ignoreCase    只读     布尔值   是否带修饰符i
	multiline     只读     布尔值   是否带修饰符m
	lastIndex     读写     整数     如果匹配模式带g修饰符，这个属性存储在整个字符串中下一次检索开始的位置
</pre>

*   RegExp的方法 exec() 与match()相似，参数是一个字符串。

没有任何匹配返回null,找到一个匹配返回一个数组,就像match()方法为非全局检索返回的数组一样，数组第一个元素是这个正则表达式相匹配的字符串，余下的元素是与园括号内的子表达式相匹配的子串。属性index包含了发生匹配的字符位置，属性input引用的是正在检索的字符串。

如果调用exec()的正则表达式对象具有修饰符g时，他将把当前正则表达式对象的lastIndex属性设置为紧挨着匹配字串的字符位置。当同一个正则表达式第二次调用exec()时，他将从lastIndex属性所指的字符出开始检索。如果exec()没有发现任何匹配结果，他会将lastIndex重置为0（在任何时候都可以将lastIndex属性设置为0，每当在字符串中找到最后一个匹配向后，在使用RegExp对象开始新的字符串查找之前，都应当将lastIndex设置为0）。这种特殊行为是我们可以反复调用exec()

<pre>
    var pattern = /Java/g,
	    text = "JavaScript is more fun than Java!",
	    result = pattern.exec(text);
	while (result != null) {
	    alert("Matched '" + result[0] + "'" +
				" at position " + result.index +
				"; next search begins at " + pattern.lastIndex);
		result = pattern.exec(text);
	}
    pattern.lastIndex = 0;
</pre>

*   RegExp的方法 test() 参数是一个字符串。如果包含正则表达式的匹配结果，则返回true。

<pre>
    var pattern = /java/i;
	pattern.test("JavaScript"); # true
</pre>
当exec()的返回结果不是null时，test()返回true。当一个全局正则表达式调用test()时，它的行为和exec()相同。
因为它从lastIndex指定的位置处开始检索某个字符串，如果找到了一个匹配结果，那么它就立即设置lastIndex为当前匹配字串的结束位置。

如果让一个带修饰符g的正则表达式对多个字符串执行exec()或test()，要么在每个字符串中找出所有匹配以便将lastIndex自动重置为0，要么显示将lastIndex手动设置为0（当最后一次检索失败时需要手动设置lastIndex）。如果忘了手动设置lastIndex的值，那么下次对新字符串进行检索时，执行检索的起始位置可能是任意位置。

在ECMAScript 5中，正则表达式直接量的每次计算都会创建一个新的RegExp对象，每个新的RegExp对象具有各自的lastIndex属性。这势必大大减少“残留”lastIndex对程序造成的意外影响。
