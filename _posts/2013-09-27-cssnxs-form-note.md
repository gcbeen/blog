---
layout: post
category: css
title: css那些事-表单
tags: [note, css, beginner, tutorial]
---
{% include JB/setup %}

<h4>表单元素特性说明</h4>

在网页中出现的表单利用CSS样式修改样式，大部分没办法实现，只能模拟。但未经CSS样式修饰的表单会随着系统主题的改变而改变。

编写css样式代码时，需要从大容器开始写样式，然后逐步地调用细节部分。这样的处理方式能更好的把握细节部分及整体调整。

目前，输入框类型的input标签是可以修改边框及背景等样式的，而复选框的input标签在个别浏览器中不能修改。

<pre>auto: true</pre>
