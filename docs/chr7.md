# Canvas学习：绘制矩形

[通过前面教程](//www.w3cplus.com/blog/tags/616.html)的学习，我们可以在Canvas中轻易[绘制路径（线段）](//www.w3cplus.com/canvas/draw-lines.html)。这仅仅是Canvas中的一小部分，今天我们来看看在Canvas中怎么绘制矩形。

## 绘制矩形的方法

在Canvas中提供了绘制矩形的API：

- **`fillRect(x, y, width, height)`**：绘制一个填充的矩形
- **`strokeRect(x, y, width, height)`**：绘制一个矩形的边框
- **clearRect(x, y, width, height)**：清除指定矩形区域，让清除部分完全透明

除此之外还可以通过Canvas中`CanvasRenderingContext2D.rect()`路径方法创建矩形。这个方法需要配合`CanvasRenderingContext2D.fill()`绘制一个填充的矩形，`CanvasRenderingContext2D.stroke()`绘制一个填充的矩形。另外，还可以直接使用Canvas的绘制路径的方法来绘制矩形。那我们来先看看怎么使用路径绘制矩形。

### 路径绘制矩形

记得在学习绘制线段的时候，我们知道`moveTo()`和`lineTo()`可以绘制线段，如此一来，四条线就能拼出一个矩形，然后通过`fill()`和`stroke()`绘制出填充和边框矩形。

	function drawScreen () {
	    ctx.strokeStyle = '#00';
	    ctx.fillStyle = '#9f9'
	    ctx.lineWidth = 4;
	    ctx.beginPath();
	    ctx.moveTo(30,30);
	    ctx.lineTo(230,30);
	    ctx.lineTo(230,200);
	    ctx.lineTo(30,200);
	    ctx.lineTo(30,30);
	    ctx.stroke();
	    
	    ctx.beginPath();
	    ctx.moveTo(300,30);
	    ctx.lineTo(500,30);
	    ctx.lineTo(500,200);
	    ctx.lineTo(300,200);
	    ctx.lineTo(300,30);
	    ctx.fill();
	    
	}

[![](/sites/default/files/blogs/2017/1703/canvas-7-1.png)](//codepen.io/airen/full/RpZKOJ/)

在Canvas中我们有一个`closePath()`的方法，在绘制矩形的时候，借助这个方法，我们通过绘制三条线段，就能和起始点闭合，也就绘制出相应的矩形。基于上面的示例，在`stroke()`和`fill()`前面添加`closePath()`即可：

	function drawScreen () {
	    ctx.strokeStyle = '#00';
	    ctx.fillStyle = '#9f9'
	    ctx.lineWidth = 4;
	    ctx.beginPath();
	    ctx.moveTo(30,30);
	    ctx.lineTo(230,30);
	    ctx.lineTo(230,200);
	    ctx.lineTo(30,200);
	    ctx.closePath();
	    ctx.stroke();
	    
	    ctx.beginPath();
	    ctx.moveTo(300,30);
	    ctx.lineTo(500,30);
	    ctx.lineTo(500,200);
	    ctx.lineTo(300,200);
	    ctx.closePath();
	    ctx.fill();
	    
	}

[![](/sites/default/files/blogs/2017/1703/canvas-7-1.png)](//codepen.io/airen/full/mWMWJz/)

### rect()绘制矩形

`rect()`也是Canvas中路径的一个方法，前面说过了，也需要配合`fill()`和`stroke()`。`rect()` 具有四个参数：

	rect(x, y, width, height)

其中`x`和`y`是矩形左上角的坐标点，`width`是矩形的宽度，`height`是矩形的高度。接下来，看如何使用`rect()`绘制矩形：

	function drawScreen () {
	    ctx.strokeStyle = '#00';
	    ctx.fillStyle = '#9f9'
	    ctx.lineWidth = 4;
	    
	    ctx.beginPath();
	    ctx.rect(30,30,200,200);
	    ctx.stroke();
	    
	    ctx.beginPath();
	    ctx.rect(300,30,200,200);
	    ctx.fill();
	    
	}

[![](/sites/default/files/blogs/2017/1703/canvas-7-1.png)](//codepen.io/airen/full/KWvWaV/)

### fillRect()绘制填充矩形

前面两种方法是通过Canvas的路径方法绘制填充和边框矩形。那么在Canvas中可以直接通过`fillRect()`绘制一个矩形：

	fillRect(x,y,width,height)

和`rect()`一样，`x`和`y`是矩形左上角的坐标点，`width`是矩形宽度，`height`是矩形高度：

	function drawScreen () {
	    ctx.fillStyle = '#9f9'
	    ctx.fillRect(30,30,200,200);   
	}

[![](/sites/default/files/blogs/2017/1703/canvas-7-2.png)](//codepen.io/airen/full/MpvprJ/)

### strokeRect()绘制边框矩形

`strokeRect()`和`fillRect()`方法类似，只不过不同的是，`strokeRect()`绘制的是边框矩形：

	function drawScreen () {
	    ctx.lineWidth = 4;
	    ctx.strokeStyle = '#9f9'
	    ctx.strokeRect(30,30,200,200);   
	}

[![](/sites/default/files/blogs/2017/1703/canvas-7-3.png)](//codepen.io/airen/full/jBLBKM/)

### 同时绘制有边框和填充色的矩形

前面我们看到的都是单独绘制边框或填充的矩形。那么将这两种结合在一起，我们就可以很容易的绘制出同时带有边框和填充色的矩形：

	function drawScreen () {
	    ctx.lineWidth = 4;
	    ctx.fillStyle = "orange";
	    ctx.strokeStyle = '#9f9'
	    
	    // Methods #1
	    ctx.beginPath();
	    ctx.moveTo(10,10);
	    ctx.lineTo(110,10);
	    ctx.lineTo(110,110);
	    ctx.lineTo(10,110);
	    ctx.closePath();
	    ctx.stroke();
	    ctx.beginPath();
	    ctx.moveTo(12,12);
	    ctx.lineTo(108,12);
	    ctx.lineTo(108,108);
	    ctx.lineTo(12,108);
	    ctx.closePath();
	    ctx.fill();
	    
	    // Methods #2
	    ctx.beginPath();
	    ctx.rect(120,10,100,100);
	    ctx.closePath();
	    ctx.stroke();
	    ctx.beginPath();
	    ctx.rect(122,12,96,96);
	    ctx.fill();
	    
	    // Methods #3
	    ctx.strokeRect(240,10,100,100);
	    ctx.fillRect(242,12,96,96);
	}

[![](/sites/default/files/blogs/2017/1703/canvas-7-4.png)](//codepen.io/airen/full/QpMvGM/)

### 绘制折角或圆角矩形

在学习[Canvas线型](//www.w3cplus.com/canvas/canvas-line-style.html)一节中，知道在Canvas中`lineJoin`可以改变线段连接端点的形状。如果我们要绘制一个折角的矩形或者圆角的矩形时，就需要借助`lineJoin`这个属性。不过有一点需要特别注意，`lineJoin`只适合于线段连接触端的样式控制。也就是说，他只适合边框矩形，如果没有边框的矩形是不生效的。话又说回来，如果需要一个填充的矩形需要有折角或圆角的效果时，就需要在填充矩形上加一个与填充色相同的边框。

	 function drawScreen () {
	    ctx.lineWidth = 10;
	    ctx.strokeStyle = '#f99'
	    
	    ctx.lineJoin = "bevel";
	    ctx.strokeRect(10,10,200,200);
	    
	    ctx.lineJoin = "round";
	    ctx.strokeRect(250,10,200,200);
	}

[![](/sites/default/files/blogs/2017/1703/canvas-7-5.png)](//codepen.io/airen/full/BWdRmW/)

上面的示例基础上调整一下：

	function drawScreen () {
	    ctx.lineWidth = 10;
	    ctx.fillStyle = "#f36";
	    ctx.strokeStyle = '#f36';
	    
	    ctx.lineJoin = "bevel";
	    ctx.strokeRect(10,10,200,200);
	    ctx.fillRect(15, 15,190,190);
	    
	    ctx.lineJoin = "round";
	    ctx.strokeRect(250,10,200,200);
	    ctx.fillRect(255, 15,190,190);
	   
	}

[![](/sites/default/files/blogs/2017/1703/canvas-7-6.png)](//codepen.io/airen/full/RpZVEG/)

## 改变矩形的样式

不管是使用Canvas中的路径方法还是自带绘制矩形的API，都可以通过`fillStyle`和`strokeStyle`来给矩形设置样式，比如填充颜色和边框颜色。

## 清除矩形

在Canvas中有一个`clearRect()`可以指定矩形区域内（以 点 `(x, y)` 为起点，范围是`(width, height)` ）所有像素变成透明，并擦除之前绘制的所有内容的方法：

	ctx.clearRect(x, y, width, height);

比如有时候需要清除画布，可以这样使用：

	ctx.save();
	ctx.clearRect(0, 0, canvas.width, canvas.height);

## 通过JS绘制矩形

前面我们学习的是通过Canvas的API来绘制矩形。很多时候，我们希望在Canvas画布上直接使用鼠标就拖拉就能绘制出矩形。那接下来，用自己蹩脚的JavaScript来实现这功能。

### 第一步：监听画布上的鼠标事件

可以通过`addEventListener()`对画布上的鼠标事件进行监听，比如`mousedown`、`mouseup`和`mousemove`等：

	myCanvas.addEventListener('mousedown', mouseDown, false);
	myCanvas.addEventListener('mouseup', mouseUp, false);
	myCanvas.addEventListener('mousemove', mouseMove, false);

在写`mouseDown`、`mouseUp`和`mouseMove`函数的时候，先声明两变量：

	var rect = {},
      	drag = false;

### 第二步：写mouseDown()函数

`mouseDown()`函数是监听鼠标在画布上按下时需要做的事情：

	function mouseDown(e) {
	  	rect.startX = e.pageX - this.offsetLeft;
	  	rect.startY = e.pageY - this.offsetTop;
	  	drag = true;
	}

当鼠标按下时，函数`mouseDown()`通过`e.pageX`和`e.pageY`找到`e`的位置，然后减去距离Canvas画布左边和顶部的距离。最后设置拖动`drag`为`true`。

### 第三步：写mouseUp()函数

	function mouseUp(){
		drag = false;
	}

这个函数很简单，当用户释放鼠标时，拖动`drag`设回`false`。表示鼠标不能拖动。

### 第四步：写mouseMove()函数

	function mouseMove(e) {
	  	if (drag) {
	    	rect.w = (e.pageX - this.offsetLeft) - rect.startX;
	    	rect.h = (e.pageY - this.offsetTop) - rect.startY ;
	    	ctx.clearRect(0,0,myCanvas.width,myCanvas.height);
	    	drawRect("fill");
	  	}
	}

`mouseMove()`函数才是关键的一步。在`mouseMove()`函数中首先检测`drag`，如果是`true`意味着想要绘制一个矩形，如果是`false`只是意味着用户只在画布上移动鼠标，并不想画矩形。如果`drag`是`true`，通过鼠标跟随位置计算出矩形的宽度和高度。如果要做到这一点，我们需要减去鼠标当前的位置。这样得到想要绘制矩形的`width`和`height`，但在绘制之前，需要通过`clearRect()`方法，将Cavans画布清除干净。然后再调用绘制矩形的函数`drawRect()`。

### 第五步：绘制矩形

	function drawRect(style){
	    if (style==="fill"){
	      ctx.fillRect(rect.startX, rect.startY, rect.w, rect.h);
	    }
	    if (style==="stroke"){
	      ctx.strokeRect(rect.startX, rect.startY, rect.w, rect.h);
	    }
	}

`drawRect()`函数是真正绘制矩形的，在这个函数中传了一个`style`参数。如果是`fill`绘制一个填充矩形，如果是`stroke`将绘制一个边框矩形。

<div  style="margin-bottom: 20px;"><iframe id="oZGxWO" src="//codepen.io/airen/embed/oZGxWO?height=400&amp;theme-id=0&amp;slug-hash=oZGxWO&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

效果出来了。你可以将传的参数`"fill"`换成'"stroke"'就可以绘制一个只有边框的矩形。

## 总结

本文介绍了在Canvas中绘制矩形的几种方法：

- 使用Canvas中的路径`moveTo()`、`lineTo()`配合`fill()`和`stroke()`绘制矩形
- 使用Canvas中的`rect(x,y,width,heihgt)`配合`fill()`和`stroke()`绘制矩形
- 使用`fillRect(x,y,width,height)`绘制一个填充的矩形
- 使用`strokeRect(x,y,width,height)`绘制一个边框矩形

另外还可以通过`clearRect(x,y,width,height)`清除画布。在Canvas中除了矩形之外，还有圆形之类的。在下一节中，我们来学习怎么在Canvas绘制圆。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/drawing-rectangular.html](//www.w3cplus.com/canvas/drawing-rectangular.html)