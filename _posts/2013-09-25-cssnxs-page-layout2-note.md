---
layout: post
category: css
title: css那些事-页面布局2
tags: [note, css, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、三列的结构</h4>
两个两列布局结构组合成三列。

<pre>
    &lt;div&nbsp;class=&quot;header&quot;&gt;头部信息&lt;/div&gt;
    &lt;div&nbsp;class=&quot;container&quot;&gt;
    &nbsp;&nbsp;&lt;div&nbsp;class=&quot;wrap&quot;&gt;
    &nbsp;&nbsp;&nbsp;&nbsp;&lt;div&nbsp;class=&quot;mainBox&quot;&gt;主要内容区域&lt;/div&gt;
    &nbsp;&nbsp;&nbsp;&nbsp;&lt;div&nbsp;class=&quot;subMainBox&quot;&gt;次要内容区域&lt;/div&gt;
    &nbsp;&nbsp;&lt;/div&gt;
    &nbsp;&nbsp;&lt;div&nbsp;class=&quot;sideBox&quot;&gt;侧边栏&lt;/div&gt;
    &lt;/div&gt;
    &lt;div&nbsp;class=&quot;footer&quot;&gt;底部信息&lt;/div&gt;
</pre>
单独的三列布局结构。

<pre>
    &lt;div&nbsp;class=&quot;header&quot;&gt;头部信息&lt;/div&gt;
    &lt;div&nbsp;class=&quot;container&quot;&gt;
    &nbsp;&nbsp;&lt;div&nbsp;class=&quot;mainBox&quot;&gt;主要内容区域&lt;/div&gt;
    &nbsp;&nbsp;&lt;div&nbsp;class=&quot;subMainBox&quot;&gt;次要内容区域&lt;/div&gt;
    &nbsp;&nbsp;&lt;div&nbsp;class=&quot;sideBox&quot;&gt;侧边栏&lt;/div&gt;
    &lt;/div&gt;
    &lt;div&nbsp;class=&quot;footer&quot;&gt;底部信息&lt;/div&gt;
</pre>
<h4>二、单独三列两列定宽，中间自适应结构。</h4>

宽度auto与浮动的关系。

*   宽度auto占据浏览器框口一行中所有位置。
*   浮动 + 宽度auto 根据容器内容确定占据浏览器窗口的大小。

<pre>
    * {
	  margin: 0;
	  padding: 0;
	}
    .header,
	footer {
	  height: 30px;
	  line-height: 30px;
	  text-align: center;
      color: #FFF;
	  background-color: #AAA;
	}
    .container {
	  text-align: center;
      color: #FFF;
	}
    .mainBox {
	  float: left;
	  width: 100%; //站满一行
	  background-color: #FFF;
	}
    .mainBox .content {
      margin: 0 210px 0 310px;
	  background-color: #000;
	}
    .subMainBox {
	  float: left;
	  width: 300px;
	  margin-left: -100%; 
	  background-color: #666;
	}
    .sideBox {
	  float: left;
	  width: 200px;
	  margin-left: -200px;
	  background-color: #666;
	}
    .footer {
	  clear: both;
	}
</pre>

ps:[float](http://www.w3school.com.cn/tiy/t.asp?f=csse_float)

<h4>三、左侧定宽右侧及中间自适应结构</h4>

右侧定宽左侧及中间自适应

<pre>
    .mainBox .content {
      <strong>margin: 0 210px 0 41%;</strong>
      background-color: #000;
    }
    .subMainBox {
      float: left;
      <strong>width: 40%;</strong>
      margin-left: -100%;
      background-color: #666;
    }
</pre>

交换左右侧

<pre>
    .mainBox .content {
	  <strong>margin: 0 41% 0 210px;</strong>
	  background-color: #000;
	}
    .subMainBox {
	  float: left;
	  width: 40%;
	  <strong>margin-left: -40%;</strong>
	  background-color: #666;
	}
    .sideBox {
	  float: left;
	  width: 200px;
	  <strong>margin-left: -100%</strong>
	  background-color: #666;
	}
</pre>

<h4>四、三列宽度自适应结构</h4>

<pre>
    .mainBox .content {
	  <strong>margin: 0 21% 0 41%;</strong>
	  background-color: #000;
	}
    .subMainBox {
	  float: left;
	  <strong>width: 40%;
	  margin-left: -100%;</strong>
	  background-color: #666;
	}
    .sideBox {
	  float: left;
	  <strong>width: 20%;
	  margin-left: -20%;</strong>
	  background-color: #666;
	}
</pre>

<h4>四、三列等高</h4>

背景模拟

<pre>
    .container {
	  float: left;
      width: 960px;
	  text-align: center;
      color: #FFF;
	  background: url(images/bg.png) repeat-y 0 0;
	}
</pre>

负边距实现

<pre>
    .container {
	  float: left;
	  width: 960px;
	  text-align: center;
      overflow: hidden;
	  color: #FFF;
	}
    
    margin-bottom: -9999px;
	padding-bottom: 9999px;
</pre>

边框模拟

<pre>
    .container {
      position: relative;
      width: 960px;
      color: #FFF;
    }

    .mainBox .content {
      border-left: 310px solid #999;
      border-right: 210px solid #333;
      background-color: #000;
    }
    .subMainBox {
      position: absolute;
      top: 0;
      left: 0;
      width: 300px;
    }
    sideBox {
      position: absolute;
      top: 0;
      right: 0;
      width: 200px;
    }
</pre>
其他 javascript 实现

<pre>
    &ltbody onload="equalColumns('subMainBox', 'm_content', 'sideBox');"&gt
	  ...
	&lt/body&gt
</pre>
