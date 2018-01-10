# Canvas学习：坐标变换

在[Canvas里的坐标系统](//www.w3cplus.com/canvas/canvas-coordinate-system.html)一节中，了解到Canvas的坐标系统如下图所示，它以Canvas画布的左上角为原点（也就是`(0,0)`），`x`坐标向右方增长，而`y`坐标则向下方延伸。

![](/sites/default/files/blogs/2017/1703/canvas-2-2.png)

然而，Canvas的坐标系统并不是一尘不变的。可以对Canvas坐标系统进行**移动**、**旋转**和**缩放**等操作。而这些操作被称为**坐标变换**。如下图所示：

[![](/sites/default/files/blogs/2017/1704/canvas-12-3.png)](//codepen.io/airen/full/BWvxzr/)

在很多场景中，Canvas的坐标变换可以让我们的操作变得更简单，更灵活。今天这节我们主要来了解Canvas中的坐标变换相关的知识。

## 移动Translating

先来看坐标变换中的移动，即**translate**方法，它用来移动Canvas和它的原点到一个不同的位置。

	ctx.translate(x, y)

`translate`方法接受两个参数。`x`是左右偏移量，`y`是上下偏移量。当偏移量操作出Canvas的`width`或`height`时，坐标将会移出Canvas的画布，这个时候你绘制的东西都将看不到。

![](/sites/default/files/blogs/2017/1704/translate.png)

来看一个移动的示例。为了后面更好的介绍坐标的变换，我先绘制一个坐标系统：

	function drawGrid(ctx, w, h, stroke, steps){
	    ctx.save();
	    ctx.beginPath();
	    for (var i = 0.5; i < w; i += steps) {
	      ctx.moveTo(i, 0);
	      ctx.lineTo(i, h);
	    }
	    for(var i = 0.5; i < h; i += steps) {
	      ctx.moveTo(0, i);
	      ctx.lineTo(w, i);
	    }
	    ctx.strokeStyle = stroke;
	    ctx.stroke();
	    ctx.restore();
	}

通过`drawGrid(ctx, w, h, '#eee', 10);`绘制一个恢复坐标系统，然后我们通过`translate()`方法来移动坐标系统：

	drawGrid(ctx, w, h, '#eee', 10);
    
    ctx.translate(40, 40);
    drawGrid(ctx, w - 40, h - 40, 'hsl(10,70%,80%)', 10);

看到的效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-12-1.png)](//codepen.io/airen/full/EWGBEr/)

从上图可以看出，后面绘制出来的坐标（红色）向右向下偏移了`40`。

可以在Canvas中移动坐标系统之后，有很多时候让我们的操作变得更为方便，比如下图这样的效果：

![](/sites/default/files/blogs/2017/1704/canvas-12-2.png)

在不使用坐标变换之前，我们需要对每个矩形的起点坐标定位：

	for(var i = 0; i < 4; i++) {
	    for (var j = 0; j < 4; j++) {
	        ctx.fillStyle = 'rgba(' + ( 120 + j * 10 ) + ',' + ( 185 - i * 10 ) + ',' + ( 10 + j * 3 ) + ',1)';

	        ctx.fillRect(i * (w - 50) / 4 + (i + 1) * 10 , j * (h - 50) / 4 + (j + 1) * 10 , (w - 50) / 4, (h - 50) / 4);
	    }
	}

是不是感觉每次要算矩形的起点很麻烦，使用Canvas的坐标移动之后，就显得容易多了。

	for(var i = 0; i < 4; i++) {
	    for (var j = 0; j < 4; j++) {
	        ctx.fillStyle = 'rgba(' + ( 120 + j * 10 ) + ',' + ( 185 - i * 10 ) + ',' + ( 10 + j * 3 ) + ',1)';
	        ctx.save();

			// 通过translate移动坐标
	        ctx.translate(i * (w - 50) / 4  + (i + 1) * 10, j * (h - 50) / 4  + (j + 1) * 10);
	        // 每一个矩形的起点都是(0,0)
	        ctx.fillRect(0, 0, (w - 50) / 4, (h - 50) / 4);
	    }
	}

得到的[效果是一样的](//codepen.io/airen/full/aJPejN/)。

## 旋转Rotating

上一节看到的是坐标变换中的移动，接下来看第二个方法`rotate`，即**旋转**，它用于以原点为中心旋转Canvas画布。

	ctx.rotate(angle)

`rotate()`方法只接受一个参数，旋转的角度`angle`，它是顺时针方向的，以弧度为单位的值。

![](/sites/default/files/blogs/2017/1704/rotate.png)

同样的，我们来看一个示例。

	// 默认坐标系统
	drawGrid(ctx, w, h, '#eee', 10);
    
    // 旋转后坐标系统
    ctx.rotate(Math.PI / 6);
    drawGrid(ctx, w, h, 'hsl(10,70%,80%)', 10);

这个时候坐标系统旋转了`Math.PI / 6`也就是`30`度：

[![](/sites/default/files/blogs/2017/1704/canvas-12-4.png)](//codepen.io/airen/full/vxvowd/)

从上面的可以看出，坐标旋转它始终是围绕Canvas坐标原点`(0,0)`进行旋转，如果需要改变的话，就要借助坐标变换中的移动`translate()`方法。

比如下面这个效果：

[![](/sites/default/files/blogs/2017/1704/canvas-12-5.png)](http://codepen.io/airen/full/wJVqZG/)

先通过`translate()`方法将坐标移动到Canvas画布的中心，然后`rotate()`方法旋转的中心都在移动后的画布原点（也就是说，当初的`(0,0)`变成现在的`(w/2,h/2)`）。

	drawGrid(ctx, w, h, '#eee', 10);
	// 将Canvas画布移动到画布的中心位置
	ctx.translate(w / 2, h / 2);
	    
	for(var i = 0; i < 10; i++) {
		ctx.save();
	    ctx.fillStyle = 'rgb(' + (51 * i ) + ',' + ( 255 - 51 * i ) + ',255)';
	      
	    for (var j = 0; j < i * 10; j++) {
	    	// 旋转坐标
	        ctx.rotate(Math.PI * 2 / (i * 10));
	        ctx.beginPath();
	        // 绘制圆
	        ctx.arc(0, i * 20, 5, 0, Math.PI * 2, true);
	        ctx.fill();
	    }
	    ctx.restore();
	}

这样一来你将[看到上图的效果](//codepen.io/airen/full/wJVqZG/)。

在很我实际场景中，我们对某个图形元素做旋转，默认情况之下，其旋转都会围绕Canvas坐标系统原点`(0,0)`进行旋转。但实际上，我们需要围绕元素中心点来做旋转。在CSS中，我们有一个`transform-origin`属性可以修改原点。但在Canvas中，就需要借助Canvas的坐标变换中的`translate()`方法来修改元素的原点，也就是将原点移动到元素的中心位置。

比如我们绘制一个矩形：

	var x = 100;
	var y = 100;
	var width = 100;
	var height = 100;
	ctx.strokeRect(x, y, width, height);

上面将一个正方形绘制在`100, 100`位置处：

![](/sites/default/files/blogs/2017/1704/canvas-12-6.png)

这个时候如果我们直接旋转的话，正方形只为围绕`(0, 0)`位置旋转，并不会围绕正形的中心点`(150, 150)`做旋转。接下来，使用`ctx.translate()`方法，将画布原点`(0, 0)`平移到正方形的中心点位置处。这个时候`ctx.translate()`方法中的`x`和`y`对应的就是：

	x + width / 2;
	y + height / 2;

也就是说`ctx.translate(x + width / 2; y + height / 2)`。这个时候绘制正方形，我们就不能再是`ctx.strokeRect(x, y, width, height)`了。因为我们将坐标移动了。为了将元素的中心点和坐标点在同一个地方，此时绘制正方形应该是`ctx.strokeRect(-width / 2, -height / 2, width, height)`。如下图所示：

![](/sites/default/files/blogs/2017/1704/canvas-12-7.png)

那么要围绕元素中心旋转就很方便了，在绘制矩形之前，再使用`ctx.rotate()`方法。我们就可以得到这样的效果：

![](/sites/default/files/blogs/2017/1704/canvas-12-8.png)

## 缩放Scaling

接着是坐标变换中的**缩放**。我们用它来增减图形在Canvas中的像素数目，对形状，位图进行缩小或者放大。

	ctx.scale(x, y)

`scale()`方法接受两个参数。`x`和`y`分别是横轴和纵轴的缩放因子。其缩放因子默认是`1`，如果比`1`小是缩小，如果比`1`大则放大。

![](/sites/default/files/blogs/2017/1704/scale.png)

来看一个示例，将坐标系统放大两倍:

	drawGrid(ctx, w, h, '#eee', 10);
    
    ctx.scale(2, 2);
    drawGrid(ctx, w, h, 'hsl(10,70%,80%)', 10);

效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-12-9.png)](//codepen.io/airen/full/qreVzX/)

前面我们介绍旋转的时候，配合`ctx.translate()`可以实现围绕元素的中心进行旋转，同样的，在这里也能实现元素围绕其中心进行缩放。

	var x = 100;
	var y = 100;
	var width = 100;
	var height = 100;
	ctx.translate(x + width / 2, y + height / 2);
	ctx.scale(2, 2);
	ctx.strokeRect(width / 2, height / 2, width, height);

> 前面找元素中心位置看到的示例都是正方形的示例，那么对于任何形状的中心怎么来寻找呢？其实其他形状和正方形类似：**只要在缩放、旋转或者组合旋转缩放前将原点平移到形状的中心，都可以得到想要的效果。记住，任何形状的中心点都是半宽的`x`值和半高的`y`值。这需要使用边界框理论找到中心点。**

坐标变换中的缩放可以用于实现很多不同的效果，比如说，在绘制了某个图形后，可以调用`ctx.scale(-1, 1)`来绘制其水平镜像或者调用`ctx.scale(1, -1)`来绘制其垂直镜像。如下所示：

	ctx.save();
    ctx.strokeStyle = '#f36';
    ctx.beginPath();
    ctx.moveTo(100, 100);
    ctx.lineTo(150, 150);
    ctx.lineTo(100, 200);
    ctx.closePath();
    ctx.stroke();
    
    ctx.translate(350, 0);
    ctx.strokeStyle = 'lime';
    ctx.beginPath();
    ctx.moveTo(-175, 0.5);
    ctx.lineTo(-175, 300.5);
    ctx.stroke();
    ctx.scale(-1, 1);
    ctx.save();
    ctx.strokeStyle = '#000';
    ctx.beginPath();
    ctx.moveTo(100, 100);
    ctx.lineTo(150, 150);
    ctx.lineTo(100, 200);
    ctx.closePath();
    ctx.stroke();

上面的代码得到一个水平镜像的效果：

[![](/sites/default/files/blogs/2017/1704/canvas-12-10.png)](//codepen.io/airen/full/XRrgxb/)

## 缩合示例

前面分别介绍了Canvas坐标变换中的移动、旋转和缩放。在实际使用当中，可以将这三者结合起来，可以帮助我们轻易实现需要的效果。比如下面这个效果，就是将三者结合在一起。

<div  style="margin-bottom: 20px;"><iframe id="BRBjPB" src="//codepen.io/airen/embed/BRBjPB?height=400&amp;theme-id=0&amp;slug-hash=BRBjPB&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 总结

这篇文章主要介绍了Canvas坐标系统中的坐标变换。Canvas坐标变换有**移动**、**旋转**和**缩放**。在实际中，将其结合在一起，可以帮助我们轻易需要的效果。

| 方法 | 描述 |
| --- | --- |
| `rotate(angle)` | 按照给定的角度来旋转坐标系，`angle`是一个弧度值 |
| `scale(x, y)` | 在`x`和`y`方向上分别按照给定的数值来缩放坐标系，默认值是`1`，小于`1`是缩小，反之是放大 |
| `translate(x, y)` | 将坐标平移到给定的`x`和`y`坐标处 |

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/transformation-coordinates.html](//www.w3cplus.com/canvas/transformation-coordinates.html)