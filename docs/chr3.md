# Canvas学习：绘制线段

在上一篇介绍[Canvas坐标系统](//www.w3cplus.com/canvas/canvas-coordinate-system.html)的结尾处，我们使用了Canvavs绘制了一个网格。整个效果是由直线和文本构成。在这一节中，我们来看看如何使用Canvas绘制线段。

在Canvas中，线段也是路径中的一种，被称之为**线性路径**。在Canvas中绘制线性路径主要用到`moveTo(x,y)`、`lineTo(x,y)`和`stroke()`几个方法。

## 先画一条简单的直线

Canvas画一下直线非常的容易。众所周之，两点就能构成一条直线。使用两个API就可以：`moveTo()`告诉你把画笔移到Canvas画布中的某个位置（直线的起点），然后通过`lineTo()`把画笔移到另一个点。从而两个点构成一条直线。

	function drawScreen () {
	    ctx.moveTo(50, 10);
	    ctx.lineTo(350, 100);
	}

但这样在画布看不到任何的线条。如果需要看到效果，还需要使用`stroke()`方法：

	function drawScreen () {
	    ctx.moveTo(50, 10);
	    ctx.lineTo(350, 100);
	    ctx.stroke();
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-1.png)

是不是很简单，通过这三个方法就可以绘制出一条线段。

- `moveTo(x,y)`：移动画笔到指定的坐标点`(x,y)`，该点就是新的子路径的起始点。该方法并不会从当前路径中清除任何子路径
- `lineTo(x,y)`：使用直线连接当前端点和指定的坐标点`(x,y)`。
- `stroke()`：沿着绘制路径的坐标点顺序绘制直线

### 改变线段宽度

我们在实际绘制线段时，总是有粗细的情况发生。那么在Canvas中可以通过`lineWidth`来改变绘制线段的粗细。比如：

	function drawScreen () {
	    ctx.lineWidth = 10; // 改变线的粗细
	    ctx.moveTo(50, 10); // 起始点
	    ctx.lineTo(350, 100); // 第二点(如果是一条直线的话，就是终点)
	    ctx.stroke();
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-2.png)

`lineWidth`主要用来定义绘制线条的宽度。默认值是`1.0`，并且这个属性必须大于`0.0`。较宽的线条在路径上居中，每边各有线条宽的一半。

### 改变线段的颜色

既然能改为线段的粗细，那必然能改变线段的颜色。在Canvas中可以通过`strokeStyle`来改变线段的颜色：

	function drawScreen () {
	    ctx.lineWidth = 10;
	    ctx.strokeStyle = '#f36';
	    ctx.moveTo(50, 10);
	    ctx.lineTo(350, 100);
	    ctx.stroke();
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-3.png)

`strokeStyle`主要用于设置画笔绘制路径的颜色、渐变和模式。该属性的值可以是一个表示CSS颜色值的字符串。如果你的绘制需求比较复杂，该属性的值还可以是一个[`CanvasGradient`](//developer.mozilla.org/zh-CN/docs/Web/API/CanvasGradient)对象或者[`CanvasPattern`](//developer.mozilla.org/zh-CN/docs/Web/API/CanvasPattern)对象。

也就是说，我们也可以绘制渐变色的线段：

	function drawScreen () {
	    // 创建一个表示线性颜色渐变的CanvasGradient对象，
	    // 并设置该对象的作用区域就是线段所在的区域
	    var canvasGradient = ctx.createLinearGradient(50, 50, 250, 50);
	    //在offset为0的位置(即起点位置)添加一个蓝色的渐变
	    canvasGradient.addColorStop(0, "blue");
	    //在offset为0.2的位置(线段左起20%的位置)添加一个绿色的渐变
	    canvasGradient.addColorStop(0.2, "green");
	    //在offset为0的位置(即终点位置)添加一个红色的渐变
	    canvasGradient.addColorStop(1, "red");
	    //将strokeStyle的属性值设为该CanvasGradient对象
	    ctx.strokeStyle = canvasGradient;
	    ctx.lineWidth = 10;
	    ctx.moveTo(50, 10);
	    ctx.lineTo(350, 100);
	    ctx.stroke();
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-4.png)

`CanvasGradient` 接口表示描述渐变的不透明对象。通过 `CanvasRenderingContext2D.createLinearGradient()` 或 `CanvasRenderingContext2D.createRadialGradient()` 的返回值得到。

如此一来，是不是可以画具有纹理的线段呢？思考一下，你就会有答案。

## beginPath()和closePath()

前面也说过了，线段也是线性路径中的一种。有开始也会有结束。其实在Canvas中具有两个方法：`beginPath()`和`closePath()`。

- **[`beginPath()`](//developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/beginPath)**：开始一个新的绘制路径。每次绘制新的路径之前记得调用该方法。它将重置内存中现有的路径
- **[`closePath()`]()**：如果当前的绘制路径是打开的，则关闭掉该绘制路径。此外，调用该方法时，它会尝试用直线边接当前端点与起始端点来关闭路径，但如果图形已经关闭（比如先调用`stroke()`）或者只有一个点，它会什么都不做。

在Canvas中绘制路径，最好加上`beginPath()`和`closePath()`。配合`lineTo()`不同点，我们可以绘制不同的路径。

	function drawScreen () {

	    ctx.strokeStyle = '#f36';
	    ctx.lineWidth = 4;
	    
	    ctx.beginPath();
	    ctx.moveTo(50, 10);
	    ctx.lineTo(150, 10);
	    ctx.lineTo(150,200);
	    ctx.lineTo(200,200);
	    ctx.lineTo(200,150);
	    ctx.stroke();
	    ctx.closePath();
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-5.png)

把上面的代码稍做修改：

	 function drawScreen () {
	    
	    ctx.strokeStyle = '#f36';
	    ctx.lineWidth = 4;
	    
	    ctx.beginPath();
	    ctx.moveTo(50, 10);
	    ctx.lineTo(150, 10);
	    ctx.lineTo(150,200);
	    ctx.stroke();
	    ctx.closePath();
	    
	    ctx.beginPath();
	    ctx.moveTo(200,200);
	    ctx.lineTo(200,150);   
	    ctx.stroke();
	    ctx.closePath();    
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-6.png)

你在效果中可以看到，这个示例，我们是绘制了两个路径。

> **特别提醒**：在绘制图形路径时，一定要先调用`beginPath()`。`beginPath()`方法将会清空内存中之前的绘制路径信息。如果不这样做，对于绘制单个图形可能没什么影响，但是在绘制多个图形时(例如上面示例的两条直线)，将会导致路径绘制或者颜色填充等操作出现任何意料之外的结果。

此外，对于`closePath()`方法，初学者一定要稍加注意。在上面绘制折线的代码示例中，我们先调用了`stroke()`，再调用了`closePath()`。其实在调用`stroke()`方法时，折线就已经绘制好了，当前的绘制路径也就被关闭掉了，所以再调用`closePath()`方法时，它就不会使用直线连接当前端点和起始端点(也就是说，这里的`closePath()`是可有可无的，不过为了保持良好的习惯，还是建议写上)。如果我们交换一下`stroke()`和`closePath()`的调用顺序，则情况完全不一样了。由于`closePath()`先调用，此时绘制路径并没有关闭，那么`closePath()`将会用直线连接当前端点和起始端点。

来看下面这段代码，一条路径是`stroke()`在`closePath()`前面（红色折线）；另一条路径是`stroke()`在`closePath()`后面（蓝色折线）：

	 function drawScreen () {
	    
	    ctx.strokeStyle = 'red';
	    ctx.lineWidth = 4;
	    
	    ctx.beginPath();
	    ctx.moveTo(50, 10);
	    ctx.lineTo(150, 10);
	    ctx.lineTo(150,200);
	    ctx.lineTo(200,200);
	    ctx.lineTo(200,150);   
	    ctx.stroke();
	    ctx.closePath();    
	    
	    ctx.strokeStyle = 'blue';
	    ctx.beginPath();
	    ctx.moveTo(250, 10);
	    ctx.lineTo(350,10);
	    ctx.lineTo(350,200);
	    ctx.lineTo(400,200);
	    ctx.lineTo(400,150);
	    ctx.closePath();
	    ctx.stroke();
	    
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-7.png)

很明显，红色的终点和起点没连在一起，而蓝色的则连起来了。

对于上面的这种多条线段（路径），如果我们在代码中添加一个`fill()`，这个时候效果就不是线条效果了，而是线条起点和终点连起来的一个图形：

	 function drawScreen () {
	    
	    ctx.strokeStyle = 'red';
	    ctx.lineWidth = 4;
	    
	    ctx.beginPath();
	    ctx.moveTo(50, 10);
	    ctx.lineTo(150, 10);
	    ctx.lineTo(150,200);
	    ctx.lineTo(200,200);
	    ctx.lineTo(200,150);   
	    ctx.stroke();
	    ctx.fill();
	    ctx.closePath();    
	    
	    ctx.strokeStyle = 'blue';
	    ctx.beginPath();
	    ctx.moveTo(250, 10);
	    ctx.lineTo(350,10);
	    ctx.lineTo(350,200);
	    ctx.lineTo(400,200);
	    ctx.lineTo(400,150);
	    ctx.closePath();
	    ctx.stroke();
	    ctx.fill();
	    
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-8.png)

同时，在上例的基础上，如果把`strokeStyle`换成`fillStyle`，同时删除代码中的`stroke()`。效果又不一样：

	function drawScreen () {
	    
	    ctx.fillStyle = '#ddaae2';
	    ctx.lineWidth = 4;
	    
	    ctx.beginPath();
	    ctx.moveTo(50, 10);
	    ctx.lineTo(150, 10);
	    ctx.lineTo(150,200);
	    ctx.lineTo(200,200);
	    ctx.lineTo(200,150);   
	    ctx.fill();
	    ctx.closePath();    
	    
	    ctx.beginPath();
	    ctx.moveTo(250, 10);
	    ctx.lineTo(350,10);
	    ctx.lineTo(350,200);
	    ctx.lineTo(400,200);
	    ctx.lineTo(400,150);
	    ctx.closePath();
	    ctx.fill();
	    
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-9.png)

这个时候，不管是`fill()`在`closePath()`前后，最终看到的效果都是一样的。也就是说`fill()`会把路径填充成一个图形。

## 简单小结一下

在HTML5 Canvas中绘制直线只需要使用**`[CanvasRenderingContext2D](//developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D)`**对象的几个属性和方法即可轻松实现。

| 属性或方法 | 基本描述 |
| -------- | ------- |
| `strokeStyle` | 用于设置画笔绘制路径的颜色、渐变和模式 |
| `lineWidth` | 定义绘制线条的宽度 |
| `beginPath()` | 开始一个新的绘制路径 |
| `moveTo(x,y)` | 移动画笔到指定的坐标点`(x,y)`，该点就是新的子路径的起始点 |
| `lineTo(x,y)` | 使用直线边接当前端点和指定的坐标点`(x,y)` |
| `stroke()` | 沿着绘制路径的坐标点顺序绘制直线 |
| `closePath()` | 如果当前的绘制路径是打开的，则关闭掉该绘制路径 |

在Canvas的图形绘制过程中，几乎都是先按照一定顺序先定下几个坐标点，也就是所谓的绘制路径，然后再根据我们的需要将这些坐标点用指定的方式连接起来，就形成了我们所需要的图形。当我们了解了`CanvasRenderingContext2D`对象的上述API后，那么绘制线条就显得非常简单了。

## 线段与像素边界

这是绘制线段的一个小细节。在说这个细节之前，咱们先来看一个小示例，就是绘制两条简单的直线。

	 function drawScreen () {
	    
	    ctx.strokeStyle = 'red';
	    ctx.lineWidth = 1;
	    
	    ctx.beginPath();
	    ctx.moveTo(50, 50);
	    ctx.lineTo(350, 50);
	    ctx.stroke();
	    
	    ctx.beginPath();
	    ctx.moveTo(50.5, 100.5);
	    ctx.lineTo(350.5,100.5);
	    ctx.closePath();
	    ctx.stroke();
	}

![](/sites/default/files/blogs/2017/1703/canvas-3-10.png)

明显第二条比第一条线。借助制图软件放大功能，来看一下：

![](/sites/default/files/blogs/2017/1703/canvas-3-11.png)

虽然我们在代码中设置了`lineWidth`的值为`1`，同样的值，但绘制出来的结果却不一样，第一条的宽度变成了`2`，而第二条的宽度是`1`。这就是我们接下来要说的线段与像素边界。

如果你在某`2`个像素的边界处绘制一条`1`像素宽的线段，那么该线段实际上会占据`2`个像素的宽度，如下图所示：

![](/sites/default/files/blogs/2017/1703/canvas-3-12.png)

如果在像素边界处绘制一条`1`像素宽的垂直线段，那么Canvas的绘图环境对象会试着将半个像素画在边界中线的右边，将另外半个像素画在边界中线的左边。

然而，在一个整像素的范围内绘制半个像素宽的线段是不可能的，所以左右两个方向上的半像素都被扩展为`1`个像素。正如上图中左图，本来我们想要将线段绘制在深灰色的区域内，但实际上浏览器却将其延伸绘制到整个灰色的范围内。

另一方面，我们来看看如果将线段绘制在某`2`个像素之间的那个像素中，效果就如上图中右图。这条垂直线段绘制在两个像素之间，这样的话，中线左右两端的那半个像素就不会再延伸了，它们合半起来恰好占据`1`像素的宽度。所以说，**如果绘制一条真正`1`像素宽的线段，你必须将该线段绘制在某两个像素之间的那个像素中，而不能将它绘制在两个像素的交界处**。

> 所有浏览器的Canvas实现都使用了**[抗锯齿](//en.wikipedia.org/wiki/Supersampling)**技术，以便创建出**[亚像素](//en.wikipedia.org/wiki/Subpixel_rendering)**线段的绘制效果来。

再回过头来看[上一节中](//www.w3cplus.com/canvas/canvas-coordinate-system.html)，[绘制的网格线](//codepen.io/airen/full/wJMGwW/)，我们可以看到它的线条宽度其实不是真正的`1px`。也就是上面说的原因造成的。既然明白原因了，那我们就可以轻松修改上节中绘制的网格。

	var x = 0.5;
    var y = 0.5;
    var w = myCanvas.width + .5;
    var h = myCanvas.height + .5;

效果如下：

<div  style="margin-bottom: 20px;"><iframe id="QpNLZO" src="//codepen.io/airen/embed/QpNLZO?height=400&amp;theme-id=0&amp;slug-hash=QpNLZO&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

将上面示例，绘制网格的代码，可以用JavaScript将其封装成为一个`drawGrid()`函数。

	function drawGrid(color, stepX, stepY) {
	    ctx.strokeStyle = color;
	    ctx.lineWidth = 0.5;
	    
	    for (var i = stepX + 0.5; i < myCanvas.width; i += stepX) {
	      	ctx.beginPath();
	      	ctx.moveTo(i, 0);
	      	ctx.lineTo(i, myCanvas.height);
	      	ctx.stroke();
	    }
	    
	    for (var i = stepY + 0.5; i < myCanvas.height; i += stepY) {
	      	ctx.beginPath();
	      	ctx.moveTo(0, i);
	      	ctx.lineTo(myCanvas.width, i);
	      	ctx.stroke(); 
	    }
	  }

其中`color`是网格线颜色，`stepX`是`x`轴的网格间距，`stepY`是`y`轴的网格间距。只需要在`drawScree()`中绘制网格线刻度，调用`drawGrid()`函数：

	function drawScreen() {
	    var dx = 50,
	    	dy = 50,
	    
	    	// 初始坐标原点
	    	x = 0,
	    	y = 0,
	    	w = myCanvas.width,
	    	h = myCanvas.height,
	    	xy = 10;
	    
	    while (y < h) {
	      	y = y + dy;
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
	      	//纵坐标的数字
	      	ctx.font = "1px Calibri";
	      	ctx.fillText(xy,x,10);
	      	xy+=10;
	    }
	    
	    drawGrid('#000', 50,50);
	  }

最后的效果如下：

<div  style="margin-bottom: 20px;"><iframe id="qrZBEE" src="//codepen.io/airen/embed/qrZBEE?height=400&amp;theme-id=0&amp;slug-hash=qrZBEE&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 总结

这篇文章主要记录了如何在Canvas中绘制线段（路径）。简单的说，使用`moveTo(x,y)`、`lineTo(x,y)`和`stroke()`就可以绘制出一条线段，或者说多线段。另外使用`lineWidth`可以改变线段的宽度，而`strokeStyle`可以改变线段的颜色。不过，在Canvas中绘制路径时记得在开始时先调用`beginPath()`。

是不是很简单呀。不知道大家发现没有，在这篇文章中，看到绘制的线都是实线，那么在Canavs中有没有直接的API可以绘制虚线或者点划线呢？先思考一下，我们后续来回答。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/draw-lines.html](//www.w3cplus.com/canvas/draw-lines.html)