---
layout: post
category: css
title: javascript权威指南-web浏览器中的javascript
tags: [note, javascript, beginner, tutorial]
---
{% include JB/setup %}

<h4>一、客户端的Javascript</h4>
Window对象是所有客户端Javascript特性和API的主要接入点。

Window对象的一些属性

*   location属性 指代Location对象。
*   window属性 指代自身Window对象。
*   document属性 指代Document对象。

<pre>
    window.location = "http://www.baidu.com";
</pre>

Window对象的一些方法

*   alert()
*   setTimeout()

<pre>
    setTimeout(function () { 
	  alert("hello world!"); 
	}, 2000);
</pre>

Window的属性和方法实际上是全局变量和全局函数。 引用自身的属性：window。

Document对象的一些方法

*   getElementById()
*   createTextNode()

Window、Document和Element对象上另一个重要的属性集合是<strong>事件处理程序相关的属性</strong>。

<pre>
    timestamp.onclick = function () { 
	  this.innerHTML = new Date().toString(); 
	};
</pre>
Window对象的onload处理程序是最重要的事件处理程序之一。当显示在窗口中的文档内容稳定并可以操作时会触发它。Javascript代码通常封装在onload事件处理程序里。

客户端Javascript的应用：

*   Web文档里的Javascript：Javascript程序可以通过Document对象和它包含的Element对象遍历和管理文档内容。
*   Web应用里的Javascript：Web浏览器是简单操作系统，Web应用定义：<strong>用Javascript访问更多浏览器提供的高级服务（比如网络、图像和数据存储）的web页面</strong>。

Javascript在Web应用里会比在Web文档里显得更加重要。Web应用本质上就是Javascript程序，后者使用由Web浏览器提供的操作系统类型的服务，并且不用期望它们在禁用浏览器脚本后还能正常工作（利用HTML表单提交的方式和服务器CGI脚本进行通信的交互式web页面是原始的“Web应用”，可以不用Javascript来实现）。

在HTML文档里嵌入客户端Javascript代码有4种方法：

*   内联，放置在&ltscript&gt和&lt/script&gt标签对之间。
*   放置在由&ltscript&gt标签的src属性制定的外部文件中。
*   放置在HTML事件处理程序中，该事件处理程序由onclick或onmouseover这样的HTML属性值指定。
*   放在一个URL里，这个URL使用特殊的"javascript:"协议。

<h4>二、内联Javascript</h4>

<pre>
    &ltscript&gt
	// coding
	&lt/script&gt
	//或者
	&ltscript&gt&lt![CDATA[
	// coding
	]]&gt&lt/script&gt
</pre>

<pre>
    function displayTime() {
	  var elt = document.getElementById('clock');
	  var now = new Date();
	  elt.innerHTML = now.toLocaleTimeString();
	  setTimeout(displayTime, 1000);
	}
</pre>

<h4>三、外部文件中的Javascript。</h4>

<pre>
    &ltscript src="../../scripts/util.js"&gt&lt/script&gt
</pre>
引用外部文件的script的属性。

*   src：脚本的url。
*   type：脚本的类型（默认 text/javascript）。
*   language：脚本使用的语言。

脚本的类型

如果使用不标准的脚本语言，如Microsoft的VBScript（只用IE支持），就必须用type属性指定脚本的MIME类型：

<pre>
    &ltscript type="text/vbscript"&gt
	// coding
	&lt/script&gt
</pre>
老的浏览器在script标记上用language属性代替type属性。

<pre>
    &ltscript language="javascript"&gt
	// coding
	&lt/script&gt
</pre>

不识别的type属性（解析这个元素但不会尝试显示或执行它的内容。）

<pre>
    使用script元素来嵌入任意文本数据到文档里。只要type属性为 一个不可执行的类型。
    用表示script元素的HTMLElement对象的text属性。 
    只对内联脚本生效，如果同时指定src属性和一个未知的类型，这样的脚本会被忽略。
</pre>

src属性方式的一些优点：

*   可以把大块Javascript代码从HTML文件中删除，这有助于保持内容和行为的分离，从而简化HTML文件。
*   如果多个Web页面共用相同的Javascript代码，用src属性可以让你只管理一份代码，而不用在代码改变时编辑HTML文件。
*   如果一个Javascript代码文件由多个页面共享，就只需要下载它一次，通过使用它的第一个页面----随后的页面可以从浏览器缓存检索它。
*   由于src属性的值可以是任意的URL，因此来自一个Web服务器的Javascript程序或Web页面可以使用由另一个Web服务器输出的代码。很多互联网广告依赖与此。
*   从其他网站载入脚本的能力，可以让我们更好的利用缓存。Google正在为通用的客户端类库推广标准且好记的URL。

同源策略

从文档服务器之外的服务器里载入脚本有重要的安全隐患，同源策略会阻止一个域的文档中的Javascript和另一个域的内容进行交互。

同源策略并不适用于：当在页面中用src属性包含一个脚本时，就给了脚本作者完全控制web页面的权限。


<h4>四、HTML中的事件处理程序</h4>

<pre>
    &ltinput type="checkbox" name="options" value="gifwrap" onchange="order.options.gifwrap = this.checked"&gt
</pre>

<h4>五、URL中的Javascript</h4>

在URL后面跟一个javascript:协议限定符，是另一种嵌入Javascript代码到客户端的方式。

嵌入的Javascript代码会被Javascript代码解释器当作单独的一行代码对待，语句之间必须用分号隔开，注释必须用/* */。

javascript:URL可以用在可以使用常规URL的地方：

*   a标签的href属性。
*   form的action属性。
*   甚至window.open()方法的参数。


<pre>
    &lt href="javascript:void "&gt a 标签 &lt/a&gt
</pre>

javascript:URL返回值处理。

*   部分浏览器（比如Firefox）会执行URL里的代码，并使用返回的字符串作为待显示新文档的内容。就像http:URL链接。
*   其他浏览器（比如Chrome和Safari）不允许URL像Firefox一样覆盖当前文档，它们会忽略代码的返回值。
*   确保javascript:URL不会覆盖当前文档，可以使用void操作符强制函数调用或给表达式赋予undefined值。

javascript:URL使用场景：

*   测试一小段javascript代码,可以在浏览器地址栏里直接输入javascript:URL。 
*   另一正统且强大的用法。浏览器书签(bookmarklet)。

bookmarklet: 一个小型程序（javascript: URL）。

bookmarklet里的代码执行起来就像页面上的脚本一样，可以查询和设置文档的内容、呈现和行为。只要书签不返回值，就可以操作当前显示的任何文档，而不把文档替换成新的内容。

<pre>
    &a href='javascript:
	  var e = "",
	      r = "";
      do {
		  e = prompt("Expression: " + e + "\n" + r + "\n", e);
		  try {
			  r = "Result: " + eval(e);
		  } catch (ex) {
		      r = ex;
		  }
	  } while();
      void 0;
	  '&gt
	  Javascript Evaluator
	  &lt/a&gt
</pre>

bookmarklet编写注意事项：

*   即便Javascript URL写成多行，解释器仍然将它作为单独的一行对待
*   单行//注释无效,使用多行注释/* */。
*   代码是单引号中HTML属性的一部分，代码中不可以包含任何单引号。
*   硬编码在页面中是有用的，把它另存为可以在任何页面上运行的书签，就更有用。

<h4>六、Javascript程序的运行</h4>

客户端Javascript程序由web页面所包含的所有Javascript代码（内联脚本、HTML事件处理程序和javascript:URL）和通过&ltscript&gt标签的src属性引用的外部Javascript代码组成。

所有这些单独的代码共用一个全局变量Window对象。

*   它们可以看到相同的Document对象
*   可以共享相同的全局函数和变量的集合（如果一个脚本定义了新的全局变量或函数，那么这个变量互函数会在脚本执行之后对任意Javascript代码可见。）

*   如果Web页面包含一个嵌入的窗体（通常使用iframe元素），嵌入文档中的Javascript代码和被嵌入文档里的Javascript代码会有不同的全局变量，它可以当作一个单独的Javascript程序。但是如果外面和里面的文档来自同一个服务器，两个文档中的代码就可以进行交互，如果你愿意，可以把它们当作是同一程序的两个相互作用的部分。

bookmarklet里的javascript:URL存在于文档之外，可以当作一种用户扩展或者对其他程序的修改。当用户执行一个bookmarklet时，书签里的javascript代码可以访问全局变量和当前文档的内容，以及操作。

javascript程序执行的两个阶段。

*   第一阶段载入文档内容，并执行&ltscript&gt元素里的代码（包括内联脚本和外部脚本）。脚本通常会按它们在文档中的出现顺序执行（从上往下，按照它在条件、循环以及其他控制语句中的出现顺序执行）。
*   所有脚本执行完后，Javascript执行进入第二阶段。异布执行阶段。由事件驱动执行。web浏览器调用事件处理程序函数响应异步发生的事件。

Javascript程序的载入阶段是相对短暂的，通常只持续1～2秒。在文档载入完成之后，只要Web浏览器显示文档，事件驱动阶段就会一直持续下去。

事件驱动阶段里发生的第一件事件是load事件（window.onload）。

javascript:URL也可以被当作是一种事件处理程序，因为直到用户通过单击链接或提交表单来激活之后才有效果。

<h4>七、同步、异步和延迟脚本</h4>
在没有API可以用来遍历和操作文档的结构和内容时，Javascript影响文档内容的唯一方法是使用document.write()方法快速生成内容。当脚本把文本传递给document.write()时，这个文本被添加到文档输入流中，HTML解析器会在当前位置创建一个文本节点，将文本插入这个文本节点后面。

脚本的执行在默认情况下是同步和阻塞的。当HTML解析器遇到script元素时，默认必须先执行脚本，然后在恢复文档的解析和渲染。如果脚本源码是一个由src属性指定的外部文件，意味着脚本后面的文档部分在下载和执行脚本之前，都不会出现在浏览器中。

改变脚本的执行方式（在支持的浏览器）。

*   defer属性 使得浏览器延迟脚本的执行，直到文档的载入和解析完成，并可以操作。
*   async属性 使得浏览器可以尽快地执行脚本，而不用在下载脚本时阻塞文档解析。
*   如果script标签同时有两个属性，同时支持两者的浏览器会遵从async属性并忽略defer属性。

defer和async属性都像在告诉浏览器链接进来的脚本不会使用document.write()，也不会生成文档内容，浏览器可以在下载脚本是继续解析和渲染文档。HTML5说这些属性只在和src属性联合使用时才有效，但有些浏览器还支持延迟的内联脚本。

延迟的脚本会按它们在文档里出现顺序执行。而异步脚本在它们载入后执行，它们可能是无序的。

<pre>
	&ltscript defer src="deferred.js"&gt 延迟的脚本。 &lt/script&gt
	&ltscript async src="async.js"&gt 异步的脚本。 &lt/script&gt
</pre>
在不支持async属性的浏览器里，通过动态创建script元素并把它插入到文档中，来实现脚本的异步载入和执行。例如：

<pre>
    function loadAsync(url) {
	  var head = document.getElementByTagName("head")[0];
	  var s = document.createElement("script");
	  s.src = url;
	  head.appendChild(s);
	}
</pre>

这个loadAsync()函数会动态载入脚本。脚本载入到文档中，成为正在执行的javascript程序的一部分。

<h4>八、事件驱动的Javascript</h4>
通过注册事件处理程序函数写程序。之后在注册事件发生时异步调用这些函数。

*   事件的名字：例如： click、change、load、mouseover、keypress或readystatechange，指示发生的事件的通用类型。
*   事件的目标：它是一个对象，并且事件就是在它上面发生的。如：click事件发生在HTMLButtonElement对象，readystatechange事件发生在XMLHttpRequest对象上。
*   事件的处理程序：事件处理程序的属性的名字是以“on”开始，后面跟着事件的名字。没函数调用，只要把函数本身赋值给这些属性。浏览器会在这些事件发生是调用。
*   注册事件处理程序最简单的方法：把Javascript函数赋值给目标对象的属性。

<pre>
    window.onload = function () {
	  // coding
	};
    document.getElementById('button1').onclick = function () {
	  // coding
	};
    function handleResponse() {
	  // coding
	}
    request.onreadystatechange = handleResponse;
</pre>

*   参数传递：传递一个事件对象，事件对象属性中提供了事件的详细信息。（在IE里，这些事件信息被存储在全局对象event对象里）。
*   事件处理程序的返回值 有时用来指示函数是否充分处理了事件，以及阻止浏览器执行它默认会进行的各种操作。
*   事件冒泡：有些事件的目标是文档元素，它们会经常向上传递给文档树。这个过程叫“冒泡”。

一个事件注册多个事件处理程序函数。或者写一个可以安全注册事件处理程序的代码块（就算另一个模块已经为相同的目标的相同的事件注册了一个处理程序）。事件目标对象的addEventListener()方法。

<pre>
    window.addEventListener('load', function () {...}, false);	
	request.addEventListener('readystatechange', function () {...}, false);
	// IE
	window.attachEvent("onload", function () {...});
</pre>
客户端Javascript的异步通知类型（不是事件）。

1.   设置Window对象的onerror属性为一个函数，在发生Javascript错误时调用函数。
2.   setTimeout()和setInterval()函数会在指定的一段时间之后触发指定函数（回调逻辑）的调用。

<h4>九、客户端Javascript线程模型</h4>
Javascript语言核心不包括任何线程机制，并且客户端Javascript传统上也没有定义任何线程机制。

单线程执行是为了让编程更简单。

*   编写代码时可以确保两个事件处理程序不会同一时刻运行。
*   操作文档内容时不必担心会有其它线程试图同时修改文档。
*   编写代码是不需要担心锁、死锁和竟态条件（race condition）。

缺点。

*   javascript脚本不能运行太长时间。
*   如果一个脚本执行计算密集的任务，它将会给文档载入带来延迟，而用户无法在脚本完成之前看到文档内容。
*   如果事件处理程序执行计算密集的任务，浏览器可能变得无法响应，可能会导致用户认为浏览器崩溃了。

方法。
*   允许文档在执行密集任务前完全载入，并确保告诉用户计算正在进行并且浏览器没有挂起。
*   或者将密集任务分解为离散的子任务可以使用setTimeout()和setInterval()方法在后台运行子任务，同时更新一个进度指示器向用户显示反馈。

HTML5定义了一种并发的控制方式，叫做“Web worker”。用来执行计算密集任务而不冻结用户界面的后台线程。

*   运行在Web worker线程里的代码不能访问内容，
*   不能和主线程或其他worker共享状态，
*   只可以和主线程和其他worker通过异步事件进行通信，
*   所以主线程不能检测并发性，并且Web worker不能修改Javascript程序的基础单线程执行模式。

<h4>十、客户端Javascript时间线</h4>
Javascript从脚本执行阶段开始，然后切换到事件处理阶段。

1.   Web浏览器创建Document对象，并且开始解析Web页面，解析HTML元素和它们的文本内容后添加Element对象和Text节点到文档中。在这个阶段document.readystate属性的值是"loading"。
2.   当HTML解析器遇到没有async和defer属性script元素时，它把这些元素添加到文档中，执行脚本，在脚本下载和执行时解析器会暂停。这样脚本就可以用document.write()来把文本插入到输入流中。解析器恢复时这些文本会成为文档的一部分。
3.   当解析器遇到设置了async属性的script元素时，它开始下载脚本文本，并继续解析文档。脚本会在它下载完成后尽快执行，但是解析器没有停下来等它下载。 异步脚本禁止使用document.write()方法。它们可能访问不到完整的文档树。
4.   当文档完成解析，document.readyState属性变成“interactive”。
5.   所有的defer属性的脚本，会按它们在文档里出现的顺序执行。异步脚本可能也会在这个时间执行。延迟脚本能访问完整的文档树，禁止使用document.write()方法。
6.   浏览器在Document对象上触发DOMContentLoaded事件。程序执行从同步脚本执行阶段转换到异步事件驱动阶段。这是可能还有异步脚本没有执行完成。
7.   这是，文档已经完全解析完成，但是浏览器可能还在等待其他内容载入，如图片。当所有这些内容完成载入时，并且所有异步脚本完成载入和执行，document.readyState属性改变为“complete”，Web浏览器触发Window对象上的load事件。
8.   从此刻起，会调用异步事件，以异步响应用户输入事件、网络事件、计算器过期等。

这是一条理想的时间线，但是所有浏览器都没有支持它的全部细节。

*   所有浏览器都支持load事件，都会触发它，它是决定文档完全载入并可以操作最通用的技术。
*   DOMContentLoaded事件在load事件之前触发，当前所有的浏览器都支持这个事件，除了IE之外。
*   document.readyState属性在写本书时已被大部分浏览器实现，但是属性的值在浏览器之间有细微的差别。
*   defer属性被所有当前版本的IE支持，但未被其他浏览器实现。
*   async属性的支持还不通用。

<h4>十一、兼容性</h4>
客户端Javascript编程中的很多不兼容性都是针对IE的。也就是说必须按照某种方式为IE编写代码，而按照另一种方式为其它的浏览器编写代码。

*   HTML中的条件注释
<pre>
    &lt;!--[if&nbsp;IE6]&gt;
    //&nbsp;coding
    &lt;![endif]--&gt;
    
    &lt;!--[if&nbsp;lte&nbsp;IE&nbsp;7]&gt;
    //&nbsp;coding
    &lt;![endif]--&gt;
    
    &lt;!--[if&nbsp;!IE]&gt;&lt;--&gt;
    //&nbsp;coding
    &lt;!--&gt;&lt;![endif]--&gt;
    
    &lt;!--[if&nbsp;IE]&gt;&lt;script&nbsp;scr=&quot;..../...js&quot;&gt;&lt;/script&gt;&lt;![endif]--&gt;
</pre>

*   Javascript中的条件注释

<pre>
    /*@cc_on
    &nbsp;&nbsp;@if&nbsp;(@_jscript)
    &nbsp;&nbsp;&nbsp;&nbsp;//jscript&nbsp;coding
    &nbsp;&nbsp;@end
    &nbsp;@*/
    &nbsp;&nbsp;&nbsp;&nbsp;
    /*@cc_on
    &nbsp;&nbsp;@if&nbsp;(@_jscript)
    &nbsp;&nbsp;//&nbsp;IE&nbsp;coding
    &nbsp;&nbsp;@else*/
    &nbsp;&nbsp;//&nbsp;other&nbsp;coding
    &nbsp;/*@end
    &nbsp;@*/
</pre>
<h4>十二、安全性</h4>
*   Javascript不能做什么：Web浏览器针对恶意代码的第一条防线就是它们不支持某些功能。
*   浏览器针对恶意代码的第二条防线是在自己支持的某些功能上施加限制。

不支持某些功能。

*   客户端Javascript没有权限来写入或删除客户计算机上的任意文件或列出任意目录。Javascript程序不能删除数据或植入病毒。
*   客户端Javascript没有任何通用的网络能力。客户端Javascript程序可以对HTTP协议编程。

HTML5有一个附属标准叫WebSockets，定义了一个类套接字的API，用于和指定的服务器通信。

在自己某些功能上施加限制。

*   Javascript程序可以打开一个新的浏览器窗口，但是为了防止广告商滥用弹出窗口，很多浏览器限制了这一功能，使得只有为了响应鼠标单击这样的用户触发事件的时候，才能使用它。
*   Javascript程序可以关闭自己打开的浏览器窗口，但是不允许它不经过用户确认就关闭其他窗口。
*   HTML FileUpload元素的value属性是只读的。如果可以设置这个属性，脚本就能设置它为任意期望的文件名，从而导致表单上传指定文件（比如密码文件）的内容到服务器。
*   脚本不能读取从不同服务器载入的文档内容，脚本不能在来自不同服务器的文档上注册事件监听器。这就防止了脚本窃取其他页面的用户输入。这一限制叫同源策略。

<h4>十三、同源策略</h4>
脚本只能读取和所属文档来源相同的窗口和文档的属性。脚本本身的来源和同源策略并不相关。相关的是脚本所嵌入的文档来源。

文档来源包含。

*   协议：使用http:协议载入的文档和使用https:协议载入的文档具有不同的来源
*   主机：从不同Web服务器载入的文档具有不同的来源。
*   端口：通过同一主机的不同端口载入的文档具有不同的来源。

同源策略应用于使用XMLHttpRequest生成的HTTP请求。这个对象允许客户端Javascript生成任意的HTTP请求到脚本所属文档的Web服务器。但不允许脚本和其他Web服务器通信。

不严格的同源策略

*   两个窗体包含的脚本把domain设置为相同的值，那么这两个窗体就不再受同源策略的约束，可以互相读取对方的属性。例如，从order.example.com和catalog.example.com载入的文档中的脚本可以把它们的document.domain的属性都设置为example.com。
*   跨域资源共享。允许服务器用头信息显示地列出源，或者使用通配符来匹配所有的源并允许由任何地址请求文件。这样XMLHttpRequest就不会被同源策略所限制。
*   跨文档消息，允许来自一个文档的脚本可以传递文本消息到另一个文档里的脚本，不管脚本的来源是否相同。调用Window对象上的postMessage()方法，可以异步传递消息事件（可以用onmessage事件处理程序函数来处理它）到窗口的文档里。

ActiveX脚本化

IE已经能够访问各种各样的脚本化ActiveX控件，这些控件是Windows操作系统的一部分，并且在过去，操作系统还存在很多可被控件利用的安全漏洞。

<h4>十四、跨站脚本xss</h4>
跨站脚本是攻击者向目标Web站点注入HTML标签或者脚本。防止XSS攻击是服务器端Web开发者的一项基本工作。

之所以叫跨站脚本攻击，因为涉及多个站点。站点B包含一个专门构造的到站点A的链接，它会注入一个来自站点B的脚本。脚本驻留在站点B中，但现在，它嵌入到站点A中，并且可以对站点A的内容进行任何操作。

易遭受XSS攻击。

*   Web页面动态地产生文档内容。
*   内容是基于用户提交的数据。
*   没有通过从中移除任何嵌入的HTML标签来“消毒”。

<h4>十五、拒绝服务攻击</h4>
这种攻击非常暴力。如果访问了启用Javascript功能的一个恶意Web站点，这个站点可以使用一个alert()对话框的无限循环占用浏览器，或者用一个无限循环或没有意义的计算来占用CPU。拒绝服务在Web上不是一个常见的问题。
