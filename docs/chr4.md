# Canvas学习：绘制虚线和圆点线

[上一节中](//www.w3cplus.com/canvas/draw-lines.html)，使用`moveTo()`、`lineTo()`和`stroke()`可以很轻易的在Canvas中绘制出实线（线段）。整篇文章中看到的都是绘制实现。不知道你和我是不是一样的好奇，那么要在Canvas中绘制虚线和圆点线又要怎么绘制，在Canvas有没有类似的API能直接绘制呢？

其实我也很好奇，看了一些资料，要像绘制实线那样简单或者直接，相对来说是没有的，但不用担心，通过其他的方法也是可以实现的。因为JavaScript是一门万能的语言，那么接下来的内容，就来看看如何在Canvas中绘制虚线和圆点线。

## 绘制虚线

在MDN上，我查到一个叫`[CanvasRenderingContext2D.setLineDash()](//developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/setLineDash)`的API。官方介绍：

> `CanvasRenderingContext2D.setLineDash()` 是 Canvas 2D API 设置虚线样式的方法。

使用方式很简单：

	ctx.setLineDash(segments);

其接受一个参数`segments`，这个参数是一个`Array`数组。一组描述交替绘制线段和间距（坐标空间单位）长度的数字。如果数组元素的数量是奇数，数组的元素会被复制并重复。例如，`[5,15,25]`会变成`[5,15,25,5,15,25]`。

言外之意，在Canvas中，可以使用`setLineDash`方法来绘制虚线。既然有这样的方法，那还等什么呢？动手吧。

	function drawScreen() {

		ctx.setLineDash([5,15]);
		ctx.lineWidth = 4;
		ctx.strokeStyle = '#f36';

		ctx.beginPath();
		ctx.moveTo(10, 100);
		ctx.lineTo(400, 100);
		ctx.stroke();
	}

![](/sites/default/files/blogs/2017/1703/canvas-4-1.png)

咱们来改变一下`setLineDash()`里面的值：

	ctx.setLineDash([25,5]);

效果变成：

![](/sites/default/files/blogs/2017/1703/canvas-4-2.png)

将上图放大来看：

![](/sites/default/files/blogs/2017/1703/canvas-4-3.png)

上图是`setLineDash([25,5])`的效果，第一个虚线点是`25`个像素，第二个空白点是`5`个像素。然后按这样的组合在不断的循环，直到线段的终点。那么如果，我们把上面的示例再做一下调整：

	function drawScreen() {

		ctx.lineWidth = 4;

		ctx.beginPath();
		ctx.setLineDash([40,30,20]);
		ctx.strokeStyle = '#f36';
		ctx.moveTo(10, 100);
		ctx.lineTo(400, 100);
		ctx.stroke();

		ctx.beginPath();
		ctx.setLineDash([40,30,20,10]);
		ctx.strokeStyle = 'blue';
		ctx.moveTo(10, 120);
		ctx.lineTo(400, 120);
		ctx.stroke();
	}

![](/sites/default/files/blogs/2017/1703/canvas-4-4.png)

同样我们把它们放大来看，这样好理解一点：

![](/sites/default/files/blogs/2017/1703/canvas-4-5.png)

上图应该解释清楚了吧。简单的理解`setLineDash([])`值，“线|间距”，不断的循环。如此一来，在绘制虚线时，就需要注意他们的循环次数，以及你需要的虚线格式，不然说不定绘制出来的虚线，并不是你所需要的哟。

其实如果你想线和间距一样大小，最简单的方法，就是只给`setLineDash()`传一个值：

	ctx.setLineDash([10]);

![](/sites/default/files/blogs/2017/1703/canvas-4-6.png)

上面实现了，我们所说的虚线，也就是CSS中的`dashed`。除此之外，还有圆点线（`dotted`）。接下来就来看，怎么在Canvas中绘制圆点线。

## 绘制圆点线

查了一下，在Canvas中没有直接绘制圆点(dotted)线的API。也就是说，如果要绘制这样的线段（路径）需要特殊处理。也就是说，需要通过JavaScript另外封装一个API。比如封装一个`CanvasRenderingContext2D.dottedLine`，然后通过`context.dottedLine`绘制。接下来，看看如何封装这个API：

	var canvasPrototype = window.CanvasRenderingContext2D && CanvasRenderingContext2D.prototype;
    canvasPrototype.dottedLine = function (x1,y1, x2, y2, interval) {
        if (!interval) {
            interval = 5;
        }

        var isHorizontal = true;

        if (x1 == x2) {
            isHorizontal = false;
        }

        var len = isHorizontal ? x2 - x1 : y2 - y1;

        this.moveTo(x1, y1);

        var progress = 0;

        while (len > progress) {
            progress += interval;

            if (progress > len) {
                progress = len;
            }

            if (isHorizontal) {
                this.moveTo(x1 + progress, y1);
                this.arc(x1 + progress, y1, 1, 0, Math.PI * 2, true);
                this.fill();
            } else {
                this.moveTo(x1, y1 + progress);
                this.arc(x1, y1 + progress, 1, 0, Math.PI * 2, true);
                this.fill();
            }
        }
    }

在Canvas画布中，通过下面的方式就可以绘制圆点线：

	context.dottedLine(10, 100, 200, 200);

![](/sites/default/files/blogs/2017/1703/canvas-4-7.png)

当`x1`和`x2`的值相等时，可以绘制竖线：

	context.dottedLine(10, 100, 10, 200);

![](/sites/default/files/blogs/2017/1703/canvas-4-8.png)

同时我们也可以改变间距值：

	context.dottedLine(10, 100, 200, 200, 10);

![](/sites/default/files/blogs/2017/1703/canvas-4-9.png)

使用类似的方法，我们就可以绘制一个圆点线的距形：

	context.dottedLine(10, 100, 200, 200, 10);
    context.dottedLine(10, 100, 10, 200, 10);
    context.dottedLine(200, 100, 200, 200, 10);
    context.dottedLine(10, 200, 200, 200, 10);

效果如下：

![](/sites/default/files/blogs/2017/1703/canvas-4-10.png)

这样一来是不是也很简单。其实除了封装成Canvas的API之外，我们是不是也可以将其封装成一个函数呢？思考一下？

## 总结

这篇文章主要使用`setLineDash()`绘制虚线（dashed），这是Canvas里提供的API。但在Canvas中没有类似的API，可以直接让我们绘制圆点划线。不过这不是什么大问题，可以利用JavaScript封装一个类似 `setLineDash()`的API `dottedLine`来绘制圆点划线。当然也可以将其封装成一个JavaScript。

在这篇文章中，我们看到的是在Canvask 绘制虚线和圆点划线，那么我们思考一下，在Canvas中怎么绘制对角线。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/draw-dashed-and-dotted-lines.html](//www.w3cplus.com/canvas/draw-dashed-and-dotted-lines.html)