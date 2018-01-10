# Canvas学习：Canvas里的坐标系统

上一节我们做好了[使用Canvas的准备工作](//www.w3cplus.com/canvas/introduction-to-prepare.html)，但并没有深入的介绍如何使用Canvas，比如怎么绘制图形，怎么做Canvas动画等。但并不用着急，随着我们后面的深入学习，这一切都会知道的。在深入学习之前，我们有必要先了解Canvas里的坐标系统。那么这一节，就先来了解Canvas的坐标系统。

## 笛卡坐标系

在数学里，我们学过一种坐标系统，叫作**笛卡坐标系**（Cartesian Coordinate system），这个坐标系统也称为**直角坐标系**，是一种正交坐标系。

![](/sites/default/files/blogs/2017/1703/Cartesian-coordinate-system-with-circle.svg.png)

二维的直角坐标系是由两条相互垂直、`0`点重合的数轴构成的。在平面内，任何一点的坐标是根据数轴上对应的点的坐标设定的。在平面内，任何一点与坐标的对应关系，类似于数轴上点与坐标的对应关系。

> 有关于笛卡坐标系详细介绍可以在[维基百科](//en.wikipedia.org/wiki/Cartesian_coordinate_system)中了解。

可能你对笛卡坐标系有了一定的了解，咱们在此基础上来看一个简单的示例，比如下图：

![](/sites/default/files/blogs/2017/1703/Cartesiancoordinatesystem.png)

你可以看到，每个点都有一双与之关联的值。这些被称为坐标点，通常表示为`(x,y)`。`x`位于水平轴上，`y`位于垂直轴上。其中`(0,0)`点是坐标原点。`x`轴从原点向右方向为正值，反之为负值，`y`轴从原点向上为正值，反之为负值。

正如上图所示，`(2,3)`表示`x = 2`，`y=3`。我们从坐标原点，沿`x`轴向右`2`格位置处画一条与`y`轴平行的线；另外沿`y`轴向上`3`格位置处画一条与`x`轴平行的线。这两条线交汇处就是`(2, 3)`的坐标点。

是不是很好理解。

## Web中的坐标系统

上面是我们印象中的数学坐标系统，但在Web页面中，或者说我们的浏览器中也有一个坐标系统。只是他和我们数学中的坐标系统不一样。Web的坐标系统的原点是在屏幕（浏览器屏幕）的左上角。如下图所示：

![](/sites/default/files/blogs/2017/1703/canvas-2-1.png)

同样的它有两个坐标轴，`x`轴（水平轴）和`y`轴（垂直轴）。两轴的交汇点（左上角）为坐标原点`(0,0)`。原点沿`x`轴向右是正值，原点沿`y`轴向下是正值。

![](/sites/default/files/blogs/2017/1703/canvas-2-3.png)

这看上去和笛卡坐标有点不一样。没有显式的`x`轴负值和`y`轴负值。其实在Web的坐标系统中一样可以使用负坐标，只不过有可能会跑到屏幕外面，让人看不到。

![](/sites/default/files/blogs/2017/1703/canvas-2-4.png)

当然这也不是说所有情况都是不可以见的。如果一个容器`div`里的另一个元素做`x`和`y`负值的移动，容器`div`如未做`overflow:hidden`处理，他在屏幕中是可见的。这方面就在这不做过多的阐述。

这似乎并没有讲到我们今天要说的主题。是不是有点等不及了，那接着往下看吧。

## Canvas坐标系统

上一篇文章中简单的提到过，在Canvas中有`2D`和`3D`之分，可以通过`getContext('2d')`让Canvas得到一个`2D`环境。言外之意，它还有一个`3D`环境。只不过我们现在只聊Canvas中的`2D`。这样一来，在Canvas中坐标系统也是有分的。

在Canvas中`2D`环境中其坐标系统和Web的坐标系统是一致的。坐标原点`(0,0)`在`<canvas>`画布的的左上角。同样的分为`x`和`y`两个轴。`x`轴向右为正值，`y`轴向下为正值。同样在`canvas`中，是没有办法直接看到。但同样，在`canvas`中使用负坐标不会导致`canvas`不能使用，只不过会移到`canvas`画布的外面。

比如，我们在`drawScreen ()`函数中绘制一个矩形（这个`drawScreen()`函数是我们为Canvas应用程序封装的一个函数，[详细的可以看上一篇文章的介绍](//www.w3cplus.com/canvas/introduction-to-prepare.html)）：

	function drawScreen () {
	    ctx.fillStyle = '#f36';
	    ctx.fillRect(15,15,20,20);
	}

上面的代码，我们在`canvas`画布中以坐标`(15,15)`（注意，这里的单位是`px`）绘制了一个宽度和高度都为`20px`的[红色矩形](//codepen.io/airen/full/LWGGOB/)。如果用一个坐标系统来表示的话类似下图：

![](/sites/default/files/blogs/2017/1703/canvas-2-2.png)

在`canvas`画布中要画出图案之前，我们必须知道我们会在哪个地方画出图案。就如上图所示。在Canvas的坐标系统中，其两轴的坐标单位都是`px`。

是不是很简单。不过在`canvas`除了默认的坐标系统之外，还有一个概念叫**Canvas坐标系转换**。那什么叫做Canvas坐标系转换，这里不做阐述，因为要阐述清楚这个概念，我们需要用一节内容专门来阐述。后面我们会花一节内容专门来介绍这个概念。

## 3D坐标系统

前面也说过了，Canvas除了一个`2D`环境之外还有一个`3D`环境。既然`2D`有坐标系统，其实`3D`环境也有坐标系统。而这个坐标称为**3D坐标系统**。

3D坐标系统多了一个`z`轴，用来描述深度。比如说一个物体在绘制时，在屏幕之内或之外多远的距离。这里简单的介绍一下3D坐标系统。

正如下图所示，`x`轴从左向右在水平方向延展，`y`轴纵向延展，`z`轴的正值从屏幕中穿出。如果你熟悉2D坐标系统的概念，那么过渡到3D坐标系统会相当直观容易。

![](/sites/default/files/blogs/2017/1703/figure1-3.png)

## 使用Canvas绘制一个坐标系统

前面介绍了Canvas中的坐标系统。那么我们来看看怎么使用`canvas`来绘制一个坐标系统。虽然我们还没有学怎么使用Canvas来画图形相关的知识，但不用担心，我们后面会学到的。本文介绍的这个示例，是用来展示一下坐标系统，虽然还不知道怎么画图形，但只要知道这样就画出图形就可以了。其实这也是为了下一篇文章做一个简单的铺垫，让你对Canvas的学习充满好奇心。

别的不多说了，直接上代码，代码中做了一些简单的注释：

	function drawScreen () {
	    // 横线与竖线的是距
	    var dx = 50;
	    var dy = 50;
	    
	    // 初始坐标原点
	    var x = 0;
	    var y = 0;
	    var w = myCanvas.width;
	    var h = myCanvas.height;
	    
	    // 单个步长所表示的长度
	    
	    var xy = 10;
	    
	    ctx.lineWidth = 1;
	    
	    // 画横线
	    while (y < h) {
	      	y = y + dy;
	      	ctx.moveTo(x, y);
	      	ctx.lineTo(w, y);
	      	ctx.stroke();
	      
	      	//横坐标的数字  
	      	ctx.font = "1px Calibri";  
	      	ctx.fillText(xy, x, y);  
	      	xy += 10;  
	    }
	    
	    // 画竖线
	    y =0;  
	    xy =10; 
	    while (x < w) {
	      	x = x + dx;
	      	ctx.moveTo(x, y);  
	      	ctx.lineTo(x,h);  
	      	ctx.stroke();   
	      	//纵坐标的数字  
	      	ctx.font = "1px Calibri";  
	      	ctx.fillText(xy,x,10);    
	      	xy+=10;  
	    }
	  }

最终效果如下：

<div  style="margin-bottom: 20px;"><iframe id="wJMGwW" src="//codepen.io/airen/embed/wJMGwW?height=400&amp;theme-id=0&amp;slug-hash=wJMGwW&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 总结

在Canvas中的画布上绘制图案之前，我们必须要知道我们会在哪个地方画出图案。如此一来，我们就必须先了解Canvas的坐标系统，因为只有知道了Canvas的坐标系统，我们会知道图案会在画布中那个地方展示出来。

Canvas具有两个环境，一个是`2D`环境，另一个是`3D`环境。因为我们主要了学的是Canvas中2D相关的知识。在Canvas中的`2D`环境中，其坐标系统和我们的Web坐标系统（浏览器坐标系统）是一样的。坐标原点`(0,0)`在画布的左上角位置处。沿`x`轴向右为正值，没`y`轴向下为正值。在Canvas中，坐标点也可以是负值，如果为负值是，坐标会移到画布的外面。另外，在Canvas中其两轴坐标单位都是`px`。

现在我们对Canvas的`2D`坐标系统有了一定的了解，也为后续在画布中绘制图案打下了基础。在接下来的学习中，我们将学习如何在画布中绘制各种图形。如果你感兴趣的话，请持续关注后续相关更新。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/canvas-coordinate-system.html](//www.w3cplus.com/canvas/canvas-coordinate-system.html)