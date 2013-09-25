---
layout: post
category: css
title: css那些事-页面布局
tags: [note, css, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、html4文档类型</h4>

<pre>
    &lt;!DOCTYPE&nbsp;html&nbsp;PUBLIC&nbsp;&quot;-//W3C//DTD&nbsp;XHTML&nbsp;1.0&nbsp;Transitional//EN&quot;&nbsp;&nbsp;&quot;http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd&quot;&gt;
</pre>
<h4>二、怪异模式（Quirks）</h4>

IE浏览器存在两种渲染方式：怪异模式（Quirks）和标准模式（Standard）。在标准模式下，浏览器根据规范表现页面；在怪异模式下页面以一种比较宽松的向后兼容的方式显示。怪异模式通常模拟老的浏览器（比如IE4）。

怪异模式下的盒模型计算方式

<pre>
    盒模型的高度 = margin-top + width + margin-bottom
	盒模型的宽度 = margin-left + width + margin-right
	
	正常模式下
	盒模型的高度 = margin-top + padding-top + width + padding-bottom + margin-bottom
    盒模型的宽度 = margin-left + padding-left + width + padding-right + margin-right

</pre>

<h4>三、容器居中</h4>
左右方向的margin设为auto即可将有宽度设置的容器居中显示。

<pre>
  .poetry-box {
    width: 250px;
	margin-left: auto;
	margin-right: auto;
  }
</pre>
<h4>四、容器居右</h4>
利用浮动方式将容器居右显示。

<pre>
    .poetry-box {
	  float: right;
	  width: 250px;
	  background: #E8E8E8;
	}
</pre>
利用定位方式将容器居右显示。

<pre>
    .poetry-box {
      position: absolute;
	  right: 0px;
	  width: 250px;
	  background: #E8E8E8;
	}
</pre>
<h4>五、两列定宽结构</h4>

<pre>
    * {
	  margin: 0;
	  padding: 0;
	}
    #header,
    #footer {
	  width: 960px;
	  height: 30px;
	  background-color: #E8E8E8;
	}
    #container {
	  width: 960px;
	  height: 250px;
	  margin: 10px 0;
	}
    .mainBox {
	  float: left;
	  width: 680px;
	  height: 250px;
	  color: #FFF;
	  background-color: #333;
	}
    .sideBox {
	  float: right;
	  width: 270px;
	  height: 250px;
	  color: #FFF;
	  background-color: #999;
	}
</pre>

对于定高容器,当内容超过范围后，

可以利用css样式中overflow属性将其余的部分隐藏或者设置出现滚动条。

我们需要的是当内容超过容器的高度值时，要将容器的高度撑开，即自适应高度。

<pre>
    #container {
	  width: 960px;
      margin: 10px 0;
	}
    .mainBox {
	  float: left;
	  width: 680px;
	  color: #FFF;
	  background-color: #333;
	}
    .sideBox {
	  float: right;
	  width: 270px;
	  color: #FFF;
	  background-color: #999;
	}
    #footer {
	  clear: both;
	}
</pre>

这种方式在FF浏览器中失去了底部的margin值。

采用另一种清除浮动的方式

<pre>
    #container:after {
      display: block;
      visibility: hidden;
	  font-size: 0;
	  line-height: 0;
      clear: both;
	  content: "";
    }
</pre>

将mainBox区域的宽度增大或者减小后。

将mainBox容器的宽度增大到780px的时候浏览器导致错位

两列定宽的布局方式 两列的盒模型宽度相加不能大于父级元素的宽度，否则将会导致页面的错位现象。

利用margin的负值减小容器与容器之间的间距。

<pre>
    .sideBox {
	  float: right;
	  width: 270px;
	  margin-left: -100px;
	}
</pre>
<h4>六、两列自适应结构</h4>

<pre>
    * {
	  margin: 0;
	  padding: 0;
	}
    #header,
    #footer {
	  height: 30px;
	  background-color: #E8E8E8;
	}
    #container {
	  margin: 10px 0;
	}
    .mainBox {
	  float: left;
	  width: 70%;
	  color: #F00;
	  background-color: #333;
	}
    .sideBox {
	  float: right;
	  width: 30%;
	  color: #FFF;
	  background-color: #999;
	}
    #container:after {
	  display: block;
	  visibility: hidden;
	  font-size: 0;
	  line-height: 0;
      clear: both;
	  content: "";
	}
</pre>

这种设置，在IE浏览器中，底部的#footer容器跑到了上面。

主要原因：IE浏览器对CSS样式解析的问题。

*   未设置#footer底部信息的宽度，默认为auto值，即根据页面中所留的空白显示容器的宽度。
*   .mainBox的浮动将#footer“拉”到上面来。
设置宽度

<pre>
    #footer {
	  width: 100%;
	}
</pre>
页面内容区域和底部信息之间的空白消失了。

另一种解决方法。在#footer中添加对上级元素浮动的清除。

<pre>
    #footer {
	  clear: both;
	}
</pre>
<h4>七、单列定宽单列自适应结构</h4>
试想一下，定宽的布局结构采用的宽度单位是px，而自适应的布局结构所采用的单位是%或者默认的auto值，如何将这两种不同单位结合在一起，最终完美实现单列定宽单列自适应的页面结构。

例如，将自适应布局结构中的css样式稍作修改，保持mainBox的宽度属性值为70%，修改sideBox的宽度属性值为200px。

<pre>
    .mainBox {
	  float: left;
	  width: 70%;
	  color: #F00;
	  background-color: #333;
	}
    .sideBox {
	  float: right;
	  width: 200px;
	  color: #FFF;
	  background-color: #999;
	}
</pre>
在某些情况下正常显示。如果将浏览器的窗口缩小后，sideBox侧边栏掉了下来。与mainBox错位了。

利用“负边距”来处理。

<pre>
    .sideBox {
	  float: right;
	  width: 200px;
	  margin-left: -200px;
      color: #FFF;
	  background-color: #999;
	}
</pre>
问题又来了，sideBox侧边栏因为使用负边距的方法后，与mainBox主要内容区域产生重叠。

分析

*   重叠与错位都是因为两列的宽度值问题引起的
*   mainBox主要内容区域目前是采用70%的宽度值，既然是自适应的宽度值，是否可以考虑用auto默认宽度值。

<pre>
    .mainBox {
	  float: left;
	  width: auto;
	  color: #F00;
	  background-color: #333;
	}
</pre>

随着mainBox的内容增多，mainBox宽度也逐渐增多，最终还是将sideBox侧边栏挤下去了。有错位了。

不采用浮动的方式布局页面，采用定位的方式布局页面。

<pre>
    #container {
	  position: relative; /*字元素的绝对定位属性的参照 */
      margin: 10px 0;
	}
    .mainBox {
	  float: left;
	  width: auto;
	  margin-right: 200px; /* 为sideBox，留有200px的空白 */
      color: #F00;
	  background-color: #333;
	}
    .sideBox {
	  position: absolute;
	  top: 0px; /* 相对父元素的顶部0px绝对定位 */
	  right: 0px; /* 相对父元素的右边0px绝对定位 */
	  float: right;
	  width: 200px;
	  margin-left: -200px;
      color: #FFF;
	  background-color: #999;
	}
</pre>

ps: [absolute](http://www.w3school.com.cn/tiy/t.asp?f=csse_position_absolute)

使用绝对定位是不得已的方式。

弊端：

*   浮动和清除浮动都无效
*   无法撑开父级元素

<h4>两列等高</h4>

背景模拟

利用背景图片的平铺，在视觉上产生等高的感觉。

<pre>
    background-repeat: repeat-y;
</pre>

<pre>
    #container {
	  width: 960px;
      background: url(images/bg.png) repeat-y 0 0;
	}
</pre>

ps: [background-repeat](http://www.w3schools.com/cssref/tryit.asp?filename=trycss_background-repeaty)

负边距实现等高

<pre>
    #container {
	  width: 960px;
      overflow: hidden;
	}
    .mainBox {
	  float: left;
	  width: 650px;
	  background-color: #333;
	}
    .sideBox {
	  float: right;
	  width: 280px;
	  background-color: #AAA;
	}
    .mainBox,
	.sideBox {
	  padding: 0 5px;
	  color: #F00;
	  margin-bottom: -9999px;
	  padding-bottom: 9999px;// padding 可以显示容器的背景图片。
	}
</pre>

ps: [overflow](http://www.w3schools.com/cssref/tryit.asp?filename=trycss_overflow)

负边距导致的问题很多。

两个比较典型的问题：

*   页面使用a锚点元素做页面跳转时，将会隐藏部分文字信息。
*   背景图片如果以background-position: left bottom;方式定位，图片将会消失。

ps: [锚点](http://www.magicalboy.com/html-named-anchors.html)

<h4>边框模拟</h4>

<pre>
    #container {
	  position: relative;
      width: 960px;
	  overflow: hidden;
	}
    .mainBox {
	  float: left;
	  width: 680px;
	  color: #FFF;
	  border-right: 280px solid #AAA;
	  background-color: #333;
	}
    .sideBox {
	  position: absolute;
	  top: 0;
	  right: 0;
	  width: 280px;
	  color: #FFF;
	}
</pre>
缺点：

*   只能将侧边栏（sideBox）或者主要内容区域（mainBox）的背景色设置为单色，无法使用背景图片。
*   边框色需要设置在侧边栏（sideBox）或者主要内容区域（mainBox）中。
*   采用绝对定位，绝对定位容器（sideBox）高度不能大于另外一个非绝对定位容器（mainBox）的高度。

其他方式（javascript）设置height属性。

<pre>
    mh = document.getElementById('mainBox');
	sh = document.getElementById('sideBox');
	if (mh.clientHeight < sh.clientHeight) {
	  mh.style.height = sh.clientHeight + "px";
	} else {
	  sh.style.height = mh.clientHeight + "px";
	}
</pre>
