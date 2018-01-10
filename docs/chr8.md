# Canvas学习：绘制圆和圆弧

圆和圆弧是图形中基本图形之一，今天我们来了解在Canvas中怎么绘制圆和圆弧。在Canvas中绘制圆和圆弧其实和绘制[线段](//www.w3cplus.com/canvas/draw-lines.html)和[矩形](//www.w3cplus.com/canvas/drawing-rectangular.html)一样的简单。在Canvas中，`CanvasRenderingContext2D`对象提供了两个方法（`arc()`和`arcTo()`）来绘制圆和圆弧。

## 与圆和圆弧相关的基础知识

在学习如何绘制圆和圆弧之前，有一些相关的基础知识有必要先进行了解。

- 角度旋转
- 角度和弧度
- 正切

### 角度旋转

在[坐标系](//www.w3cplus.com/canvas/canvas-coordinate-system.html)中，旋转分为顺时针和逆时针两个方向旋转：

![](/sites/default/files/blogs/2017/1703/canvas-8-1.png)

### 角度和弧度

在CSS中，做旋转常用到的都是角度（`deg`）。但在Canvas中绘制圆或圆弧时用到的是弧度（`rad`）。[维基百科](//zh.wikipedia.org/wiki/%E5%BC%A7%E5%BA%A6)中是这样描述弧度的：

> 弧度又称弪度，是平面角的单位，也是国际单位制导出单位。单位弧度定义为圆弧长度等于半径时的圆心角。角度以弧度给出时，通常不写弧度单位，或有时记为`rad`。

![](/sites/default/files/blogs/2017/1703/Radian_cropped_color.svg)

一个完整的圆的弧度是`2π`，所以`2π rad = 360°`，`1 π rad = 180°`，`1°=π/180 rad`，`1 rad = 180°/π`（约`57.29577951°`）。以度数表示的角度，把数字乘以`π/180`便转换成弧度；以弧度表示的角度，乘以`180/π`便转换成度数。

	rad = (π / 180) * deg

同样的：

	deg = (rad * 180) / π

平时我们常看到的各种弧度如下：

![](/sites/default/files/blogs/2017/1703/Radian-common.svg)

#### JavaScript中弧度角度换算

仅难了解角度和弧度之间的关系是不够的，我们还需要知道怎么使用JavaScript来实现角度和弧度之间的换算。一个`π`大约是`3.141592653589793`，在JavaScript中对应的是`Math.PI`。那么角度和弧度之间的换算：

	rad = (Math.PI * deg) / 180

同样的：

	deg = (rad * 180) / Math.PI

为了方便计算和使用，可以将其封装成JavaScript函数：

	function getRads (degrees) {
		return (Math.PI * degrees) / 180;
	}

	function getDegrees (rads) {
		return (rads * 180) / Math.PI;
	}

比如我们要将`30deg`转换成`rad`，可以直接使用：

	getRads(30); // 0.5235987755982988rad
	getDegrees(0.7853981633974483); // 45deg

下图展示了常见的角度和弧度之间的换算：

![](/sites/default/files/blogs/2017/1703/Unit_circle_angles.svg)

### 正切

[正切](//zh.wikipedia.org/wiki/%E6%AD%A3%E5%88%87)（Tangent，tan，也作tg）是[三角函数](//zh.wikipedia.org/wiki/%E4%B8%89%E8%A7%92%E5%87%BD%E6%95%B0)的一种。它是周期函数，其最小正周期为`π`（`Math.PI`）。正切函数是[奇函数](//zh.wikipedia.org/wiki/%E5%A5%87%E5%87%BD%E6%95%B0)。

> 在Canvas中常常需要和[三角函数](//zh.wikipedia.org/wiki/%E4%B8%89%E8%A7%92%E5%87%BD%E6%95%B0)打交道，这也说明了数学是多么的重要，真后悔当初没有认真学。有关于Canvas中三角函数的运用，后面我们将会花很大的篇幅来介绍。

为什么在画圆要提到正切呢？那是因为我们后面在介绍`artTo()`时会涉及到正切相关的知识。下图可以说明，正切和圆以及圆弧之间的关系，看上去一点复杂，但不用急于求成，后面会慢慢懂的：

![](/sites/default/files/blogs/2017/1703/Circle-trig6.svg)

有了这些基础，我们就可以开始学习在Canvas中怎么画圆和圆弧了。这也是这篇文章真正的主题，如果你等不及了，那继续往后阅读。

## arc()方法

先来看`arc()`方法怎么绘制圆和圆弧。Canvas中的`arc()`方法接受六个参数：

	arc(x, y, radius, startRad, endRad, [anticlockwise]);

在Canvas画布上绘制以坐标点`(x,y)`为圆心、半么为`radius`的圆上的一段弧线。这段弧线的起始弧度是`startRad`，结束弧度是`endRad`。这里的弧度是以`x`轴正方向为基准、进行顺时针旋转的角度来计算。其中`anticlockwise`表示`arc()`绘制圆或圆弧是以顺时针还是逆时针方向开始绘制。如果其值为`true`表示逆时针，如果是`false`表示为顺时针。该参数是一个可选参数，如果没有显式设置，其值是`false`（也是`anticlockwise`的默认值）。

![](/sites/default/files/blogs/2017/1703/canvas-8-2.jpg)

记得当初我们学数时，圆的周长与半径的关系是：`C = πd`或`C = 2πr`。具备这些基础，我们就可以使用`arc()`绘制弧线或圆了。

### 绘制弧线

先来看`arc()`绘制弧线，根据上面介绍的内容，传对应参数给他：

	function drawScreen () {
	    // x,y => 圆心坐标点
	    // r => 圆弧半径
	    var arc = {
	      	x: myCanvas.width / 2, 
	      	y: myCanvas.height / 2,
	      	r: 100
	    },
	    	w = myCanvas.width,
	    	h = myCanvas.height;

	    ctx.save();
	    ctx.lineWidth = 10;
	    ctx.strokeStyle = '#e3f';

	    // startRad => getRads(-45)
	    // endRad => getRads(45)
	    // 顺时针旋转
	    ctx.beginPath();
	    ctx.arc(arc.x, arc.y, arc.r,getRads(-45),getRads(45));
	    ctx.stroke();
	    

	    // startRad => getRads(-135)
	    // endRad => getRads(135)
	    // 逆时针旋转
	    ctx.beginPath();
	    ctx.strokeStyle = "#f36";
	    ctx.arc(arc.x, arc.y, arc.r,getRads(-135),getRads(135),true);
	    ctx.stroke();
	    ctx.restore();

	}

[![](/sites/default/files/blogs/2017/1703/canvas-8-3.png)](//codepen.io/airen/full/PpOOwG/)

当我们把上面的`stroke()`换`fill()`，上面的效果就不是一个弧线了：

[![](/sites/default/files/blogs/2017/1703/canvas-8-4.png)](//codepen.io/airen/full/BWmrXr/)

另外在`stroke()`之前调用`closePath()`，那么弧线的起始点和终止点将会以一条直接连接在一起。比如上面的示例，加上之后的效果：

	ctx.beginPath();
    ctx.arc(arc.x, arc.y, arc.r,getRads(-45),getRads(45));
    ctx.closePath();
    ctx.stroke();

[![](/sites/default/files/blogs/2017/1703/canvas-8-5.png)](//codepen.io/airen/full/pedVzY/)

`arc()`绘制弧线是不是很简单，在实际中，借助一些条件循环，我们可以做一些有意思的效果。比如下面的这个示例，使用`arc()`绘制一个声波波率放大图：

	function drawScreen () {
	    
	    var arc = {
	      	x: myCanvas.width / 2,
	      	y: myCanvas.height / 2,
	      	r: 10
	    },
	    	w = myCanvas.width,
	    	h = myCanvas.height;
	    
	    ctx.save();
	    ctx.lineWidth = 1;
	    ctx.strokeStyle = '#e3f';
	    
	    for(var i = 0;i < 10; i++){
	      	ctx.beginPath();
	      	ctx.arc(arc.x, arc.y, arc.r * i,getRads(-45),getRads(45));
	      	ctx.stroke();
	      
	      	ctx.beginPath();
	      	ctx.arc(arc.x, arc.y, arc.r * i,getRads(-135),getRads(135),true);
	      	ctx.stroke();
	    }
	}

[![](/sites/default/files/blogs/2017/1703/canvas-8-6.png)](//codepen.io/airen/full/Npwwgw/)

**特别注意：**

- 使用`arc()`绘制图形时，如果没有设置`moveTo()`那么会从圆弧的开始的点（`startRad`处）作为起始点。如果设置了`moveTo()`，那么该点会连线到圆弧起始点。
- 如果使用`stroke()`方法，那么会从开始连线到圆弧的起始位置。 如果是 fill 方法, 会自动闭合路径填充

![](/sites/default/files/blogs/2017/1703/canvas-8-7.png)

### 绘制制圆

使用`arc`绘制圆和绘制圆弧是一样的，只不过绘制圆的时候`startRad`和`endRad`是相同的。比如：

	function drawScreen () {
	    
	    var arc = {
	      	x: myCanvas.width / 2,
	      	y: myCanvas.height / 2,
	      	r: 50
	    },
	        w = myCanvas.width,
	        h = myCanvas.height;
	    
	    ctx.save();
	    ctx.lineWidth = 2;
	    ctx.strokeStyle = '#fff';
	    ctx.fillStyle = '#000';
	    // 绘制一个边框圆
	    ctx.beginPath();
	    ctx.arc(arc.x / 2, arc.y, arc.r, getRads(0), getRads(360), false);
	    ctx.stroke();
	    
	    // 绘制一个闭合边框圆
	    ctx.beginPath();
	    ctx.arc(arc.x, arc.y, arc.r, getRads(0), getRads(360), false);
	    ctx.closePath();
	    ctx.stroke();
	    // 绘制一个填充圆
	    ctx.beginPath();
	    ctx.arc(arc.x * 1.5, arc.y, arc.r,getRads(0), getRads(360), true);
	    ctx.fill();
	    //绘制一个带边框填充的圆
	    ctx.beginPath();
	    ctx.arc(arc.x, arc.y, arc.r / 2,getRads(0), getRads(360), false);
	    ctx.stroke();
	    ctx.fill();
	    ctx.restore();
	    
	}

[![](/sites/default/files/blogs/2017/1703/canvas-8-8.png)](//codepen.io/airen/full/pedVOv/)

来做个小练习，使用`arc()`绘制一个太极图：

- 绘制一个白色和黑色大半圆，拼成一个圆形
- 绘制制一个小的白色半圆和另一个黑色小半圆
- 绘制一个白色和黑色小圆点

这几个组合起来，就是我们想要的太极图：

	function drawScreen () {
	    
	    var arc = {
	      	x: myCanvas.width / 2,
	      	y: myCanvas.height / 2,
	      	r: 100
	    },
	        w = myCanvas.width,
	        h = myCanvas.height;
	    
	    ctx.save();
	    ctx.lineWidth = 1;
	    
	    // 绘制白色大圆
	    ctx.beginPath();
	    ctx.fillStyle = '#fff';
	    ctx.arc(arc.x, arc.y, arc.r, getRads(-90), getRads(90), false);
	    ctx.fill();
	    
	    // 绘制黑色大圆
	    ctx.beginPath();
	    ctx.fillStyle = '#000';
	    ctx.arc(arc.x, arc.y, arc.r, getRads(-90), getRads(90), true);
	    ctx.fill();
	    
	    // 绘制白色小圆
	    ctx.beginPath();
	    ctx.fillStyle = '#fff';
	    ctx.arc(arc.x, arc.y - arc.r/2, arc.r / 2,getRads(-90), getRads(90), true);
	    ctx.fill();
	    
	    // 绘制黑色小圆
	    ctx.beginPath();
	    ctx.fillStyle = '#000';
	    ctx.arc(arc.x, arc.y + arc.r/2, arc.r / 2,getRads(-90), getRads(90), false);
	    ctx.fill();
	    
	    // 绘制小黑点
	    ctx.beginPath();
	    ctx.fillStyle = '#000';
	    ctx.arc(arc.x, arc.y - arc.r/2, arc.r / 10,getRads(0), getRads(360), false);
	    ctx.fill();
	    
	    // 绘制小白点
	    ctx.beginPath();
	    ctx.fillStyle = '#fff';
	    ctx.arc(arc.x, arc.y + arc.r/2, arc.r / 10,getRads(0), getRads(360), false);
	    ctx.fill();
	  }

[![](/sites/default/files/blogs/2017/1703/canvas-8-9.png)](//codepen.io/airen/full/wJPPQy/)

### 绘制扇形

使用`arc()`除了可以绘制弧线和圆之外，还可以绘制扇形。绘制扇形关键点是通过`moveTo()`把起始点位置设置为圆心处，然后通过`closePath()`闭合路径。

	function drawScreen () {
	    
	    var arc = {
	      	x: myCanvas.width / 2,
	      	y: myCanvas.height / 2,
	      	r: 100
	    },
	        w = myCanvas.width,
	        h = myCanvas.height;
	    
	    ctx.save();
	    ctx.lineWidth = 1;
	    ctx.strokeStyle = '#e3f';
	    ctx.fillStyle = '#e3f';
	    
	    ctx.beginPath();
	    // 起始点设置在圆心处
	    ctx.moveTo(arc.x, arc.y);
	    ctx.arc(arc.x, arc.y, arc.r,getRads(-45),getRads(45));
	    // 闭合路径
	    ctx.closePath();
	    ctx.stroke();
	    
	    ctx.beginPath();
	    // 起始点设置在圆心处
	    ctx.moveTo(arc.x, arc.y);
	    ctx.arc(arc.x, arc.y, arc.r,getRads(-135),getRads(135),true);
	    // 闭合路径
	    ctx.closePath();
	    ctx.fill();
	    
	    ctx.restore();
	}

[![](/sites/default/files/blogs/2017/1703/canvas-8-10.png)](//codepen.io/airen/full/zZPaaj/)

利用这个原理，可以很轻松的实现一个饼图效果。

> 特别声明：`arc()`方法中的起始弧度参数`startRad`和结束弧度参数`endRad`都是以弧度为单位，即使你填入一个数字，例如`360`，仍然会被看作是`360`弧度。

## arcTo()方法

前面学习了`arc()`方法如何绘制弧线、圆或扇形等。在Canvas中`CanvasRenderingContext2D`还提供了另一个方法`arcTo()`用来绘制弧线，但`arcTo()`绘制不出圆。为什么呢？接下来，咱们就来了解`arcTo()`的使用方法。

`arcTo()`接受五个参数：

	arcTo(x1, y1, x2, y2, radius)

`arcTo()`方法将利用当前端点、端点一`(x1, y1)`和端点二`(x2, y2)`这三点所形成的夹角，然后绘制一段与夹角的两边相切并且半径为`radius`的圆上的弧线。弧线的起点就是当前端点所在边与圆的切点，弧线的终点就是商端点二`(x2,y2)`所在边与圆的切点，并且绘制的弧线是两个切点之间长度最短的那个圆弧。此外，如果当前端点不是弧线起点，`arcTo()`方法还将添加一条当前端点到弧线起点的直线线段。

上面理解起来有点吃力。事实上，`arcTo()`尽管是通过两点和半径绘制弧线或圆，但事实上是有三个点参与。也就是说，不管是否调用`arcTo()`，其实就有一个点已经存在`(x0,y0)`。这样就`(x0,y0)`和`(x1,y1)`构成一线，然后`(x1,y1)`和`(x2,y2)`构成一线，这两条线交叉点就是`(x1,y1)`。然后以`radius`绘制的圆弧或圆都会与这两条线相切。这也就是在文章开头提正切的基础。

![](/sites/default/files/blogs/2017/1703/canvas-8-11.png)

或者换下图，你能更好的理解。

![](/sites/default/files/blogs/2017/1703/canvas-8-12.png)

在 `arcTo()` 函数中，虽然参数只涉及到 `P1`也就是参数中的`(x1,y1)` 和 `P2`也就是参数中的`(x2,y2)` 两个点，实际上还有一个隐含的点，就是画布上的当前点（`P0`）也就是前面所说的`(x0,y0)`。当 `P0`, `P1`, `P2` 不重叠也不在一条直线的时候，这 `3` 个点可以构成一个三角形。想象一下，从 `P0` 开始，向 `P1` 画一条线段，从 `P1` 开始到 `P2` 再画一条线段，这两条线段形成一个夹角，然后以 `r` 画一个圆，移动这个圆将这个圆与线段 `P0P1` 和线段 `P1P2` 相切（也可能切点是在 `P0P1` 或者 `P1P2` 的延长线上），然后保留朝向 `P1` 这个点的弧线，就是 `arcTo()` 在弧线这部分做的事情。

实际绘制是这样的：

- `moveTo()` 给出 `P0` 点坐标
- `arcTo()` 函数中的参数给出了 `P1` 点和 `P2` 点的坐标，以及圆形的半径 `r`
- 计算以 `r` 为半径的圆和直线 `P0P1` 以及 `P1P2` 的切点，记为 `S` 点和 `E` 点，对应图上 `Start` 和 `End` 两个点
- 从 `P0` 向 `S` 点画出一条线段
- 从 `S` 点到 `E` 点，画出一段圆弧，半径为 `r`
- 此时，画布当前点为 `E` 点
- 然后从 `E` 点又画了一条线段到 `P2` 点，至此 `arcTo` 的工作已经完成，接下来你可以 `stroke()` 或者 `fill()` 了

来看一个绘制过程：

	function drawScreen () {
	    ctx.lineWidth = 1;
	    ctx.strokeStyle = '#f36';
	    ctx.fillStyle = 'red';
	    
	    // 一个起始点 ( 100, 50 ), 那么绘制其点. 颜色设置为红色
	    ctx.fillRect( 100 - 4, 50 - 4, 8, 8 );
	    // 两个参考点分别为 ( 100, 200 ) 和 ( 300, 200 ), 绘制出该点
	    ctx.fillRect( 100 - 4, 200 - 4, 8, 8 );
	    ctx.fillRect( 300 - 4, 200 - 4, 8, 8 );
	    
	    // 连接两个参考点
	    ctx.beginPath();
	    ctx.strokeStyle = 'red';
	    ctx.moveTo( 100, 200 );
	    ctx.lineTo( 300, 200 );
	    ctx.stroke();
	    
	    // 调用 arcTo 方法绘制圆弧. 记得将起始点设置为 ( 100, 50 )
	    ctx.beginPath();
	    ctx.strokeStyle = 'blue';
	    ctx.moveTo( 100, 50 );
	    ctx.arcTo( 100, 200, 300, 200, 80);
	    ctx.stroke();
	}

![](/sites/default/files/blogs/2017/1703/canvas-8-13.gif)

### 绘制带圆角矩形

在学习Canvas中[绘制矩形](https://www.w3cplus.com/canvas/drawing-rectangular.html)一节时，我们提到通过`lineJoin`改变线段端点形状来模拟一个圆角矩形。通过这样的方法绘制带圆角的矩形，局限性还是非常大的。不过值得庆達的是，`acrTo()`可以轻易实现两线内切圆弧，言外之意，使用`arcTo()`来绘制一个圆角矩形是非常的方便。

圆角矩形是由四段线条和四个`1/4`圆弧组成，拆解如下。

![](/sites/default/files/blogs/2017/1703/canvas-8-14.jpg)

如此一来，我们就可以封装一个函数，用来绘制圆角矩形。根据上图，我们可以给这个函数，比如`drawRoundedRect()`函数传递对应的参数：

- `ctx`：Canvas画布绘图环境
- `x,y`：左上角
- `width`：矩形宽度
- `height`：矩形高度
- `r`：矩形圆角半径
- `fill`： 绘制一个填充的矩形
- `stroke`：绘制一个边框矩形

开始封装函数：

	function drawRoundedRect(ctx, x, y, width, height, fill, stroke) {
		ctx.save();
		ctx.beginPath();
		
		// draw top and top right corner
		ctx.moveTo(x + r, y);
		ctx.arcTo(x + width, y, x + width, y + r, r);

		// draw right side and bottom right corner
		ctx.arcTo(x + width, y + height, x + width - r, y + height, r);

		// draw bottom and bottom left corner
		ctx.arcTo(x, y + height, x, y + height - r, r);

		// draw left and top left corner
		ctx.arcTo(x, y, x + r, y, r);

		if (fill) {
			ctx.fill();
		}

		if (stroke) {
			ctx.stroke();
		}

		ctx.restore();
	}

函数封装好之后，只需要调用，就可以轻易绘制出带圆角的矩形，而且简单易用：

	function drawScreen () {
	    
	    ctx.strokeStyle = 'rgb(150,0,0)';
	    ctx.fillStyle = 'rgb(0,150,0)';
	    ctx.lineWidth = 7;
	    drawRoundedRect(ctx, 30, 50, 200, 220, 20, true, true);

	    ctx.strokeStyle = 'rgb(150,0,150)';
	    ctx.fillStyle = 'rgba(0,0,150,0.6)';
	    ctx.lineWidth = 7;
	    drawRoundedRect(ctx, 300, 100, 250, 150, 8, true, false);
	}

[![](/sites/default/files/blogs/2017/1703/canvas-8-15.png)](//codepen.io/airen/full/EWbOPG/)

是不是很简单。可以来个复杂点的练习。比如，当初火热的2048游戏。

![](/sites/default/files/blogs/2017/1703/canvas-8-16.png)

使用`drawRoundedRect()`函数就可以很轻易的绘制出来，有兴趣的同学不仿一试。

## 绘制月亮

这节主要学习了`arc()`和`arcTo()`两个方法，学习了怎么使用这两个方法在Canvas中如何绘制圆弧或圆。那么我们来看一个小示例，结合两者来绘制一月亮。

用一张图来阐述绘制月亮的原理：

![](/sites/default/files/blogs/2017/1703/moon.jpg)

此图已经说明一切，直接上代码吧：

	function drawMoon(cxt, d, x, y, R, rot){
	    cxt.save();
	    cxt.translate(x, y);
	    cxt.scale(R, R);
	    cxt.rotate(Math.PI / 180 * rot);
	    pathMoon(cxt, d);
	    cxt.fillStyle = 'hsl' + randomColor();
	    cxt.fill();
	    cxt.restore();
	 }
	 //画路径
	 function pathMoon(cxt, d){
	    //D表示控制点的横坐标；
	    cxt.beginPath();
	    cxt.arc(0, 0, 1, Math.PI * 0.5, Math.PI * 1.5, true);
	    cxt.moveTo(0, -1);
	    cxt.arcTo(d, 0, 0, 1, dis(0, -1, d, 0) * 1 / d);
	    cxt.closePath();
	 }

	 function dis(x1, y1, x2, y2){
	    return Math.sqrt((x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2));
	 }
	  
	 function drawScreen () {
	 	drawMoon(ctx,2,myCanvas.width / 2,myCanvas.height / 2,100,15);
	 }

<div  style="margin-bottom: 20px;"><iframe id="pedLJa" src="//codepen.io/airen/embed/pedLJa?height=400&amp;theme-id=0&amp;slug-hash=pedLJa&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 总结

在Canvas中，`CanvasRenderingContext2D`对象提供了两个方法（`arc()`和`arcTo()`）来绘制圆和圆弧。其中`arc()`即可绘制弧线，圆，也可以绘制扇形，但`arcTo()`仅能绘制出弧线。但`arcTo()`可以更轻易的帮助我们实现带圆角的矩形。到目前为止，我们学习了在Canvas中绘制线段、矩形、弧线和圆等，但基本图形不仅仅这些，接下来我们将学习如何在Canvas中绘制箭头。感兴趣的同学，欢迎持续关注相关更新。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/drawing-arc-and-circle.html](//www.w3cplus.com/canvas/drawing-arc-and-circle.html)