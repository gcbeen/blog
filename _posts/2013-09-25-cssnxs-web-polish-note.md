---
layout: post
category: css
title: css那些事-网页润色
tags: [note, css, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、字体设置</h4>
字体设置的建议：

*   中文页面尽可能首先定义为“宋体”，其次为其他字体，如：

<pre>
    font-family: "宋体"、Verdana、Lucida,、Arial、Helvetica、sans-serif;
    font: normal 14px/1.5em simsun, Verdana,Lucida, Arial, Helvetica, sans-serif;
</pre>

*   英文页面就选择英文字体，可以考虑Arial、Verdana、Tahoma等字体。
*   中英结合的页面可以考虑首先定义英文字体，相对来说修改浏览器默认字体的用户少之又少，但不带表没有。
*   特殊字体一律使用图片。

<h4>二、字形改变</h4>

字体大小（font-size）的属性值

*   xx-small： 最小
*   x-small： 较小
*   small： 小
*   medium：默认值 正常
*   large：大
*   x-large：较大
*   xx-large：最大
*   larger：相对字体尺寸，相对于父对象中字体尺寸较大，使用成比例em单位计算。
*   smaller：相对字体尺寸，相对于父对象中字体尺寸较小，使用成比例em单位计算。
*   length：百分数，px，其百分比取值基于父对象中字体的尺寸。

字体加粗（font-weight）常用属性值。

*   normal：默认值
*   bold：粗体

font-style属性值

*   normal：默认值
*   italic：斜体

em强调标签所包含的文字默认就是斜体，如果不需要倾斜 将其设置为正常的字体（normal）。

<h4>三、文字颜色（color）</h4>

<h4>四、段落样式</h4>

首行缩进

text-indent属性可以设置文本对象的缩进

<pre>
    p {
	  text-indent: 2em;/* em相对于当前对象内文本字体大小的单位。 */
	}
</pre>

在一个固定宽度的容器内，利用缩进的特性将文字“推到”容器之外，在设置overflow: hidden;将超出该容器的内容隐藏。

<pre>
    p {
	  width: 200px;
	  height: 25px;
	  overflow: hidden;
	  background-color: #EEE;
	}
    p.ti-2em {
	  text-indent: 2em;
	}
    p.ti-none {
	  text-indent: -9999px;
	}
</pre>
行高调整（line-height）

<pre>
    p {
	  line-height: 28px;
	  background-color: #EEE;
	}
</pre>

<pre>
    p span {
	  font-size: 30px;
	}
</pre>

line-height属性居有一个特性，不设置单位的时候会以段落中某个最大的文字字体为基准做行高处理。

<pre>
    p {
	  line-height: 1.5;
	  background-color: #EEE;
	}
</pre>

<h4>五、特殊效果</h4>
首字母下沉

<pre>
    p:first-letter {
	  float: left;
	  font-weight: bold;
	  font-size: 2em;
	}
    p {
	  clear: both;
	}
</pre>
首行文字样式

<pre>
    p:first-line {
	  font-weight: bold;
      color: #F00;
	}
</pre>
文字隐藏截取

<pre>
    p {
	  width: 300px;
	  height: 54px;
	  overflow: hidden;
	  background-color: #EEE;
	}
</pre>

文字隐藏，以图片代替。

<pre>
    &lh1&gtcss那些事&lth1&gt

	h1 {
	  width: 250px;
	  height: 80px;
	  overflow: hidden;
	  <strong>text-indent: -9999px;</strong>
	  background: url(images/logo.jpg) no-repeat 0 0;
	}
</pre>

<pre>
    h1 {
	  width: 250px;
	  height: 80px;
	  overflow: hidden;
	  line-height: 9999px;
      background: url(images/logo.jpg) no-repeat 0 0;
	}
</pre>
使用具备隐藏特性的属性

*   visibility: hidden;
*   display: none;

<pre>
    &lth1&gt&ltspan&gtCSS那些事&lt/span&gt&lt/h1&gt

	h1 {
	  width: 250px;
	  height: 80px;
      background: url(images/logo.jpg) no-repeat 0 0;
	}
    h1 span {
      visibility: hidden;
	}
    //或者
    h1 span {
	  display: none;
	}
</pre>

<h4>六、文字链接</h4>

链接样式的几个伪类

*   :link 
*   :visited
*   :hover
*   :active

四个伪类的顺序（LoVe HAte）link visited hover active

<pre>
    .myLink {
	  padding-left: 20px;
      background: url(images/music_1.gif) no-repeat left center;
	}
    .myLink:hover {
	  background: url(images/music_2.gif) no-repeat left center;
	}
</pre>
利用多张图片代替文字链接在不同状态下的样式。

<pre>
    .moreA {
	  display: block;
	  width: 41px;
	  height: 11px;
	  overflow: hidden;
	  text-indent: -9999px;
      background: url(images/more_1.gif) no-repeat 0 0;
	}
    .moreA:hover {
	  width: 43px;
	  height: 13px;
	  background: url(images/more_2.gif) no-repeat 0 0;
	}
</pre>
