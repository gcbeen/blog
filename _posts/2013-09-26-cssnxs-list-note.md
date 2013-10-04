---
layout: post
category: css
title: css那些事-列表
tags: [note, css, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、列表种类</h4>

*   无须列表（ul）
*   有序列表（ol）
*   自定义列表（dl）

xhtml中无序列表的错误嵌套方法：

*   错误一：ul标签与li标签之间插入其他标签。

<pre>
    &lt;ul&gt;
    &nbsp;&nbsp;&lt;li&gt;无须列表中的一条内容&lt;/li&gt;
    &nbsp;&nbsp;&lt;li&gt;无须列表中的一条内容&lt;/li&gt;
    &nbsp;&nbsp;&lt;div&gt;错误的无序列表嵌套结构&lt;/div&gt;
    &lt;/ul&gt;
</pre>

*   错误二：多层ul标签嵌套时的错误

<pre>
    &lt;ul&gt;
    &nbsp;&nbsp;&lt;li&gt;错误的无序列表嵌套结构&lt;/li&gt;
    &nbsp;&nbsp;&lt;ul&gt;
    &nbsp;&nbsp;&nbsp;&nbsp;&lt;li&gt;错误的无序列表嵌套结构&lt;/li&gt;
    &nbsp;&nbsp;&lt;/ul&gt;
    &lt;/ul&gt;
</pre>

*   错误三：li标签未关闭。

<pre>
    &lt;ul&gt;
    &nbsp;&nbsp;&lt;li&gt;错误的无序列表嵌套结构
    &nbsp;&nbsp;&lt;ul&gt;
    &nbsp;&nbsp;&nbsp;&nbsp;&lt;li&gt;错误的无序列表嵌套结构&lt;/li&gt;
    &nbsp;&nbsp;&lt;/ul&gt;
    &nbsp;&nbsp;&lt;li&gt;错误的无序列表嵌套结构&lt;/li&gt;
</pre>
浏览器对无序列表的默认解析也是有规律的。无序列表可以分为一级无序列表和多级无序列表，一级无序列表在浏览器中解析后，会在列表li标签前面添加一个小黑点修饰符，而多级无序列表则会根据级数改变列表前面的修饰符。

凡是总有一个尽头，浏览器在解析无序列表时，会不断地对不同级别的无须列表缩进，但是修饰符却只有3种。

如果三层嵌套都无法满足网页中的列表的需求，可以思考一下这个页面中的列表嵌套是否过多。

如果网页中实在是需要更多层嵌套，并且是不同的修饰符，我们还是可以利用CSS样式来修改其修饰符的。

有序列表

有序列表在多级的情况下，理论上应该是随着层级的增加而出现1.1或者1.1.1之类的数字，但浏览器却无法在网页中直接解析出这样的效果。如果需要使用1.1或者1.1.1之类的数字表达方式，那么就只能利用背景图片或者手工输入。

list-style-image属性 设置列表前修饰用的图像。

当list-style-image属性值为none或者属性值中图像的url地址错误时，list-style-type属性将会替代list-style-image属性

ps：在页面实现效果中，list-style-image中某些情况下对位置的控制不如background-image灵活，因此大家采用background-image的方式多于list-style-image的方式。

list-style-position属性 设置列表修饰的位置
*   outside: 默认值，列表项目标记放置在文本以外，且环绕文本不根据标记对齐。
*   inside: 列表项目标记放置在文本以内，且环绕文本根据标记对齐。

ps：[inside outside](http://www.w3schools.com/cssref/tryit.asp?filename=trycss_list-style-position)

list-style-type

*   disc: 默认值，实心圆。
*   circle: 空心圆。
*   square: 实心方块。
*   decimal：阿拉伯数字。
*   lower-roman：小写罗马数字。
*   upper-roman：大写罗马数字。
*   lower-alpha：下写英文字母。
*   upper-alpha：大写英文字母。
*   none：不使用项目符号。

不调用任何一种修饰符而使用背景图片作为列表的修饰符。前提：必须将list-style-type属性的属性值设置为none,而且list-style-image属性值也为none。

list-style-type属性在页面中显示的效果与padding-left和margin-left有着密切的联系：

<pre>
    ul {
	  list-style-type: lower-roman;
	  padding-left: 0;
	}
</pre>
padding-left设为0在FF下看不到列表的修饰符。

<pre>
    ul {
	  list-style-type: lower-roman;
	  margin-left: 0;
	}
</pre>
margin-left设为0在IE浏览器下无法正常显示修饰符。

这些并不等同于 list-style-type: none;的效果，而是不同浏览器在解析列表的 padding 与 margin时产生的一种错误的解析方式。

利用背景属性添加背景图片作为列表的修饰符：

<pre>
    ul {
	  list-style: none;
	}
    li {
	  padding-left: 20px;
      background: url(images/music.gif) no-repeat left center;
	}
</pre>
<h4>二、自定义列表</h4>
<pre>
    &lt;dl&gt;
    &nbsp;&nbsp;&lt;dt&gt;&lt;/dt&gt;
    &nbsp;&nbsp;&lt;dd&gt;&lt;/dd&gt;
    &lt;/dl&gt;
</pre>
注意点

*   dl标签必须与dt标签相邻，dd标签需要对应于一个dt标签。
*   dl、dt、dd 3个标签之间不允许出现第四者。
*   标签必须成对出现，嵌套要合理。

dl标签 自定义列表集合，dt标签 自定义列表标题， dd标签 自定义列表内容。

因此自定义列表dl标签一般都是出现在名词性解释的情况下。

<h4>三、列表模式的导航</h4>

ps：[text-decoration](http://www.w3school.com.cn/tiy/t.asp?f=csse_text-decoration)
