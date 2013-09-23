---
layout: post
category: css
title: css那些事笔记
tags: [note, css, beginner, tutorial]
---
{% include JB/setup %}
<h4>一、css的简写</h4>
margin padding的简写

<pre>
    margin: margin-top margin-right margin-bottom margin-left;

    margin: top-bottom margin-right margin-bottom;

    margin: top-bottom left-right;

    margin: all
</pre>
边框的简写

<pre>
    border: border-width || border-style || border-color
    /* 边框的四个方向的属性不能简写 */
    div {
      border-width: 1px; /* 边框4个方向大小都为1px */
	  border-style: solid dashed double; /* 上边框为实线，左右边框为虚线，下边框为双线 */
	  border-color: #FF0000 #000000; /* 上下边框为红线，左右边框为黑线 */
    }
</pre>
背景的简写

<pre>
    body {
	  background-color: #FF0000; /* 背景颜色 */
	  background-image: url(background.gif);  /* 背景图片 */
	  background-repeat: no-repeat; /* 背景图片无平铺 */
	  background-attachment: fixed; /* 将背景图片固定，不随页面滚动而滚动 */
	  background-position: 0 0; /* 定义背景图片的位置，必须先定义背景图片后才有效 */
	}
    
    bacckground: background-color || background-image || background-repeat || background-attachment || background-position

    body {
	  background: #FF0000 url(background.gif) no-repeat fixed 0 0;
	}

    /* 默认值  */
    background-color: transparent;
	background-images: none;
	background-repeat: repeat;
	background-attachment: scroll;
	background-position: 0% 0%;
</pre>
ps:[background-attachment](http://www.w3school.com.cn/tiy/t.asp?f=csse_background-attachment)

<pre>
  background-attachment: fixed;

  background-attachment: scroll;
</pre>


字体的简写

<pre>
    body {
	  font-style: italic; /* 字体 斜体 */
	  font-variant: small-caps; /* 字体小型的大写字母，针对英文 */
	  font-weight: bold; /* 将文字加粗 */
	  font-size: 12px; 
	  line-height: 140%; /* 行高 */
	  font-family: "Lucida Grande", sans-serif; /* 字体名称 */
	}
    
    font: font-style || font-variant || font-weight || font-size || line-height || font-family

	body {
	  font: italic small-caps bold 12px/140% "Lucida Grande", sans-serif;
	}
    
    /* 默认值 */
    font-style: normal;
	font-variant: normal;
	font-weight: normal;
	font-size: medium;
	line-height: normal;
	font-family: "Times New Roman"
</pre>
ps: [line-height](http://www.w3school.com.cn/tiy/t.asp?f=csse_dim_line-height_percent),[font-variant](http://www.w3schools.com/cssref/tryit.asp?filename=trycss_font-variant)

<pre>
    line-height: 140%;

	font-variant: small-caps
</pre>
表的简写

<pre>
    li {
      list-style-type: square;
	  list-style-position: inside;
	  list-style-image: url(image.gif);
    }
  
    list-style: list-style-image || list-style-position || list-style-type

	li {
	  list-style: url(image.gif) inside square;
	}
    
    /* 默认值 */
    list-style-type: disc;
	list-style-position: outside;
	list-style-image: none;
</pre>
ps: [list-style-position](http://www.w3school.com.cn/tiy/t.asp?f=csse_list-style-position)

<pre>
    list-style-position: inside;

	list-style-position: outside;
</pre>
<h4>二、css选择符</h4>
通配符选择符

一般用来对页面所有元素应用样式

<pre>
    * {
	  margin: 0;
	  padding: 0;
	}
</pre>
对后代元素应用样式

<pre>
    p * {
      // stylesheet
    }
</pre>
类选择符

<pre>
    .myContent {
	  // stylesheet
	}
</pre>
包含选择符

<pre>
    p strong {
      // stylesheet
    }
</pre>
子选择符

<pre>
    p > strong {
	  // stylesheet
	}
</pre>
相邻选择符

<pre>
    p + strong {
	  // stylesheet
	}
    &lt;p&gt;p标签&lt;/p&gt;
    &lt;strong&gt;1、p标签相邻&lt;/strong&gt;
    &lt;strong&gt;2、strong2&lt;/strong&gt;
    &lt;strong&gt;3、strong3&lt;/strong&gt;
    &lt;strong&gt;4、strong4&lt;/strong&gt;
</pre>

<pre>
    strong + strong {
      // stylesheet
	}
    &lt;p&gt;p标签&lt;/p&gt;
    &lt;strong&gt;1、p标签相邻&lt;/strong&gt;
    &lt;strong&gt;2、strong2&lt;/strong&gt;
    &lt;strong&gt;3、strong3&lt;/strong&gt;
    &lt;strong&gt;4、strong4&lt;/strong&gt;
</pre>
属性选择符

分为四种：

第一种E[attr]

<pre>
    *[class] {
      color: #F00;
	}
    &lt;p&nbsp;class=&quot;paragraphs&quot;&gt;带class&lt;/p&gt;
    &lt;p&gt;不带class&nbsp;&lt;/p&gt;
</pre>
第二种E[attr="value"]

<pre>
    input[type="text"] {
	  text-align: center;
      border: 1px solid #F00;
      background-color: #E8E8E8;
	}
    &ltinput type="text" value="" /&gt
	&ltinput type="password" value="" /&gt
</pre>
第三种E[attr~="value"] 属性值是用空格隔开的字段，有一个是value

<pre>
    p[title~="css"] {
      color: #F00
	}
    &lt;p&nbsp;title=&quot;css&nbsp;xhtml&quot;&gt;css&nbsp;xhtml&lt;/p&gt;
    &lt;p&nbsp;title=&quot;css+xhtml&quot;&gt;css+xhtml&lt;/p&gt;&nbsp;&nbsp;
    &lt;p&nbsp;title=&quot;层叠式样式表&nbsp;css&quot;&gt;css&nbsp;层叠式样式表&lt;/p&gt;
</pre>
第四种E[attr|=value] 属性值必须是以value值开始及使用连字符（-）分隔的E元素

<pre>
    p[title|="css"] {
      color: #F00;
	}
    &lt;p&nbsp;title=&quot;xhtml-css&quot;&gt;xhtml-css&lt;/p&gt;
    &lt;p&nbsp;title=&quot;css+xhtml&quot;&gt;css+xhtml&lt;/p&gt;
    &lt;p&nbsp;title=&quot;css-Cascading&nbsp;Style&nbsp;Sheets&quot;&gt;css-Cascading&nbsp;Style&nbsp;Sheets&lt;/p&gt;
</pre>
ID选择器

<pre>
    #myContent {
	  //stylesheet
	}
</pre>
选择符组合关系

正对性使用类选择符或ID选择符

<pre>
    .myContent {
	  // stylesheet
	}

    p.myContent {
	  // stylesheet
	}

    #myContent {
	  // stylesheet
	}
</pre>
选择符群组

<pre>
    p,
	.myContent,
    #title {
	  // stylesheet
	}
</pre>
选择符组合

<pre>
    p span {
	  // stylesheet
	}
</pre>
<h4>三、伪类、伪对象</h4>
伪类：以a标签为例

<pre>
    a:link {
	  color: red;
	}/* 默认状态 */
    a:visited {
	  color: blue;
	}/* 访问过后状态 */
    a:hover {
	  color: green;
	}/* 经过时的状态 */
    a:active {
	  color: back;
    }/* 点击时的状态 */
</pre>
<pre>
    input:hover {
	  background-color: #F00;
	}
    &ltinput type="text" value="" /&gt
</pre>
伪对象 如在p前后添加文字。

<pre>
    p:before {
	  content: "4月1日，"
	}
    p:after {
	  content: ", 大家小心不要被骗啊！"
	}
    &ltp&gt愚人节快到了&lt/p&gt
</pre>
<h4>四、选择符的关系</h4>

大雪过后为何大地一片银白，因为白雪覆盖在大地之上，所以看到大地一片银白，css样式也是如此，后面的样式覆盖前面的（选择符的覆盖）。选择符的继承（子承父业）就是后代标签会继承父级元素的css属性，其中也有例外，例如标题标签h1~h6这6个标签将会使用浏览器默认的样式设置的文字大小，也就是使用选择符的覆盖特性。

选择符的权重值优先级别

css样式采用的有限顺序

1.   标有！important关键字声明的属性。
2.   HTML中的css样式属性。(不推荐)
3.   作者编辑的css文件样式属性。（推荐）
4.   用户设置的样式。
5.   浏览器默认的样式。

css样式的优先级积分

*   标签选择符、伪类及伪对象：优先级积分为1。
*   类选择符、属性选择符：优先级积分为10。
*   ID选择符：优先级积分为100。
*   style属性：优先级积分为1000。
*   其他选择符，如通配符选择符等：优先级积分为0。
!important关键字优先级最高。

把css引入html页面。

*   直接写在标签元素的属性style中，行间样式。
*   将样式写在<style></style>标签之内，内嵌样式表。
*   通过<link/>方式外链css样式文件，外联样式表。
*   通过@import关键字导入外部css样式文件，导入样式表。

样式表的规划与维护

一般将css分为两部分：

*   页面的全局定义及处理页面基本布局的css样式文件
*   处理细节方面的css样式文件

ps：[html转义](http://wyz.67ge.com/html-encode-tool/)
