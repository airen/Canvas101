# Canvas学习：Canvas入门准备

由于工作的需要，最近开始在学习HTML5的[`canvas`](//www.w3.org/TR/2dcontext2/)相关的知识。这里主要记录自己学习`canvas`相关的知识笔记。如果文章有不对之处，还请大婶们多多指正。

今天这篇文章是学习`canvas`的一些准备工作。

## canvas元素

`<canvas>`也是HTML中的一个元素，可以给这个元素添加一些HTML属性，比如使用`width`和`height`来控制其大小，也可以通过`style`给它设置一些基本样式。同样也可以它添加`id`名，在JavaScript中能更好的操作它。

在Web页面中添加一个`canvas`很容易，只需要在`<body>`标签内添加一个`<canvas>`标签就行，如下:

	<canvas id="myCanvas" width="400" height="400">
		Your browser does not support HTML5 Canvas.
	</canvas>

上面的示例中，在`</canvas>`中添加一行文本，主要是用于不支持`canvas`的浏览器，如果浏览器不支持，就会显示这行文本。

上面这种方法是比较简单的方法，当然也可以通过JavaScript的`document.createElement('canvas')`创建一个`<canvas>`，并且使用`document.body.appendChild(canvas)`把创建的`canvas`插入到`body`中：

	(function(){
	  	var canvas = document.createElement('canvas');
	  	document.body.appendChild(canvas);
	  	canvas.id = 'myCanvas';
	  	canvas.width = 400;
	  	canvas.height= 400;
	})();

上面的JS代码就是创建了一个`400 x 400`的`canvas`，并且其`id`名为`myCanvas`。这个时候，你在浏览器中就有一个`canvas`，不过你现在什么都看不到，因为我们还没有在`canvas`中绘制作任何的东西：

![](/sites/default/files/blogs/2017/1702/canvas-1-1.png)

如果我们`canvas`添加一个边框，那就可以看到了，只不过里面没有其他的东东而以：

	canvas {
	  	border: 1px solid #ccc;
	}

![](/sites/default/files/blogs/2017/1702/canvas-1-2.png)

如果你想把`canvas`添加到另一个元素中，比如说`div`中，可以这样操作：

	document.getElementById('eleIdName').appendChild(canvas);

## 文档对象模型（DOM）

文档对象模型（Document Object Model，简称DOM），是W3C组织推荐的处理可扩展标志语言的标准编程接口。**文档对象模型代表了在HTML页面上的所有对象。它是语言中产且平台中立的。它允许页面的内容和样式被Web浏览器渲染之后再次更新。用户可以通过JavaScript访问DOM**。

在开始使用`<canvas>`之前，需要了解两个特定的DOM对象：`window`和`document`。

- `window`对象是DOM的最高一级，需要对这个对象进行检测来确保开始使用`canvas`应用程序之前，已经加载了所有的资源和代码
- `document`对象包含所有在HTML页面上的HTML元素。需要对这个对象进行检索来找出用JavaScript操作`<canvas>`的实例

也就是说，将`<canvas>`放入Web页面时，第一件要做的事就是，**看看整个页面是否已经加载，并且开始操作前是否所有HTML元素都已展现**。这也是在使用Canvas处理图像和声音时非常重要的一点。

为此，做到这一点，我们可以通过监听`window`的`load`事件。该事件在HTML页面加载结束时发生。要监听一个事件，就需要给事件添加一个监听器。在这里，可以通过`addEventListener()`方法来监听`window`的`load`事件。

	window.addEventListener('load', function(){},false)

或者

	window.addEventListener('load', eventWindowLoaded, false);

	function eventWindowLoaded () {
		canvasApp(); //包含整个Canvas应用程序
	}

## 引用Canvas元素

通过前面的内容可以知道，我们可以有两种方法给Web页面添加`<canvas>`元素。虽然在Web页面中有了`<canvas>`元素，但如果我们不通过JavaScript做任何操作的话，你页面中是看不到任何效果的。那么要给一个`canvas`进行操作就是需要一个Canvas对象的引用。在这里的话，可以在`canvasAPP()`函数中先定义一个新变量，比如说这个变量叫`myCanvas`，主要用这个变量来保存Canvas对象的引用。

	function canvasApp () {
		var myCanvas = document.getElementById('myCanvas');
	}

### 检测浏览器是否支持Canvas

`canvas`是HTML5的一个API，有部分浏览器是不支持的（[可以通过Caniuse.com查阅浏览器支持情况](//caniuse.com/#search=canvas)）。这也就有前面所说的，在`<canvas>`元素中添加一段描述文本，让不支持的浏览器能看到这段提示性的文本。那么我们在使用Canvas的API之前，可以先做一个浏览器检测。比如：

	function canvasAPP () {
		var myCanvas = document.getElementById('myCanvas');

		if (!myCanvas || !myCanvas.getContext) {
			return; 
		}

		// 这里开始绘制
	}

上面是通过调用Canvas的`getContext()`方法来检测浏览器是否支持`canvas`。其原理很简单，**在调用Canvas的`getContext()`方法之前，先检测Canvas对象以及`getContext`方法是否存在**。

这段代码其实测试了两件事：

- 它测试了`myCanvas`是否包含`false`(如果命名的`id`不存在，`document.getElementById()`将会返回此值)
- 它测试Canvas的`getContext()`方法是否存在，

如果两者之间有一个测试失败，就会执行`return`语句将中断整个程序的执行。

为了更好的提高代码阅读能力，可以将上面的代码进行一个封装。

	function canvasSupport (e) {
	  	return !!e.getContext;
	}

	function canvasAPP () {
		var myCanvas = document.getElementById('myCanvas');

		if (!canvasSupport(myCanvas)) {
	  		return;
		}

		// 这里开始绘制
	}

### 获得2D环境

在使用Canvas的相关API，除了要获取`canvas`对象之外，还需要得`2D`环境的引用，才能够操作它。HTML5的Canvas被设计可以与多个环境工作，包括一个建议的`3D`环境。不过我们先学的是`2D`环境。

要获取Canvas中的`2D`环境，只需要通过`canvas`的`getContext()`方法即可，给这个方法传入一个`2d`的值。并且将这个环境赋予给一个变量，比如`ctx`：

	function canvasApp () {
	  	var myCanvas = document.getElementById('myCanvas');

	  	if (!canvasSupport(myCanvas)) {
		  	return; 
		}
	  	var ctx = myCanvas.getContext('2d');

	  	// ...
	}

如此一来，我们就可以通过HTML5 Canvas中的一些API进行一些操作。比如下面的代码，可以在Canvas中绘制一个矩形：

	function canvasApp () {
	  	var myCanvas = document.getElementById('myCanvas');

	  	if (!canvasSupport(myCanvas)) {
		  	return; 
		}
	  	var ctx = myCanvas.getContext('2d');
	  	ctx.fillStyle = '#f36';
	  	ctx.fillRect(10, 10, 200, 200);
	}

看到的效果如下：

<div  style="margin-bottom: 20px;"><iframe id="VpvPzr" src="//codepen.io/airen/embed/VpvPzr?height=400&amp;theme-id=0&amp;slug-hash=VpvPzr&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

你可能好奇`fillStyle`和`fillRect()`是什么意思，这里你不用搞清楚他们是做什么的，你只要完全的相信，这样做就可以绘一个填充好的矩形。后面的教程，我们会搞清楚是什么意思。

## 为Canvas封装JavaScript代码

Canvas应用程序与在浏览器中运行的其他应用有所不同。由于Canvas只在屏幕上特定的区域执行并显示效果。可以说它的功能是独占的，因此不太会受到页面上其他内容的影响，反之也是如此。如果想在同一个页面上放置多个`canvas`，那么在定义JavaScript代码时必须将对应的代码分开。

为了避免出现这个问题，可以将变量和函数都封装在另一个函数中。比如前面代码中的`canvasAPP()`函数包含整个Canvas应用程序。另外还可以封装一个`drawScreen()`函数，用来对Canvas应用进行操作。

	window.addEventListener('load', eventWindowLoaded, false);

	function eventWindowLoaded () {
	  	canvasApp();
	}

	function canvasSupport (e) {
		return !!e.getContext;
	}

	function canvasApp () {
	  	var myCanvas = document.getElementById('myCanvas');
	
	  	if (!canvasSupport(myCanvas)) {
		  	return; 
		}
	  
	  	var ctx = myCanvas.getContext('2d');
	  
	  	function drawScreen () {
	      // ...
	  	}
	  
	  	drawScreen();
	}

这样我们就对Canvas进行封装了，如果要实现上面的效果，绘制一个矩形，只需要在`drawScreen()`函数进行操作。

	function drawScreen () {
      	ctx.fillStyle = '#f36';
      	ctx.fillRect(0,0,200,200);
	}

## 总结

这篇文章我们了解了如何在HTML页面中添加`canvas`元素，以及怎么检测浏览器是否支持`canvas`。为了更好的使用`canvas`，我们通过JavaScript对`canvas`进行了封装。这样一来，以后我们要在一个`canvas`画布上做任何操作，都可以在封装好的`drawScreen()`函数中操作。这样就能支持`canvas`的浏览器看到效果。有了这些准备工作之后，后面我们就可以做Canvas能做的事情了。在后面的内容我们将会先来了解怎么使用Canvas的API绘制基本图形。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/introduction-to-prepare.html](//www.w3cplus.com/canvas/introduction-to-prepare.html)