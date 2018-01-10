# Canvas学习：save()和restore()

本来今天想开始学习怎么在Canvas中绘制矩形。但昨天发朋友圈，聊到Canvas。有网友提醒我将所有绘制的东西放在`ctx.save()`和`ctx.restore()`，能起到保存绘制状态和防止污染状态栈。养成一个良好的习惯。

![](/sites/default/files/blogs/2017/1703/canvas-6-1.png)

此时一脸蒙逼！因为我并不知道这两个东东是什么意思，怎么使用？

后来翻了书，也只是提到了在Canvas中有`save()`和`restore()`两个方法，它们都是属于Canvas中`CanvasRenderingContext2D`中与状态操作有关的两个方法。并没有详细的介绍，对于初学者的我而言，也是一知半解。下面的内容记录了我对`save()`和`restore()`两个方法的一些理解。

## Canvas 状态的保存和恢复

Canvas的API提供了两个名叫`save()`和`restore()`的方法，用于保存及恢复当前Canvas绘图环境的所有属性。其中`save()`可以保存当前状态，而`restore()`可以还原之前保存的状态。

这两个方法再绘图中有着重要的作用，比如我们在绘图的时候需要使用多种颜色，颜色需要不时的切换。那么使用`save()`和`restore()`方法即可比较方便的实现此功能。

## 理解save()和restore()

对于`save()`和`restore()`方法，一开始有一个错误的理解，以为每一步都`save()`之后`restore()`就等同于`command + z`（或者`ctrl + z`），其实`save()`保存的只是`CanvasRenderingContext2D`对象的状态以及对象的所有属性，并不包括这个对象上绘制的图形。

### Canvas中状态和非状态

在Canvas环境中绘图时，可以利用所谓的绘图堆栈状态。每个状态随时存储Canvas上下文数据。下面是存储在状态堆栈的数据列表。

- 当前的坐标变换（变换矩阵）信息，比如旋转或平移时使用的`rotate()`和`setTransform()`方法
- 当前剪贴区域
- 图形上下文对象（`CanvasRenderingContext2D`）的当前属性值

`CanvasRenderingContext2D`的当前属性值主要包括：

| 属性 | 描述 |
| --- | --- |
| `canvas` | 取得画布`<canvas>`元素 |
| `fillStyle` | 填充路径的当前的颜色、模式或渐变 |
| `globalCompositeOperation` | 指定颜色如何与画布上已有颜色组合（合成） |
| `lineCap` | 指定线段端点的绘制方式 |
| `lineJoin` | 指定线段连接的绘制方式 |
| `lineWidth` | 绘制线段的宽度 |
| `miterLimit` | 当`lineJoin`为`miter`时，这个属性指定斜连接长度和二分之一线宽的最大比率 |
| `shadowBlur` | 指定阴影模糊度 |
| `shadowColor` | 指定阴影颜色 |
| `shadowOffsetX` | 指定阴影水平偏移值 |
| `shadowOffsetY` | 指定阴影垂直偏移值 |
| `strokeStyle` | 指定线段颜色 |

上面是Canvas绘图中的状态，那么什么情形不属于Canvas状态。

在Canvas中当前路径和当前位图受Canvas环境控制，不属于保存状态。这个重要的功能允许在画布上对单个对象进行绘画和制作动画。

虽然说了那么多，但仅仅知道是用来操作Canvas状态，对于初学者而言还是模棱两棵。在[官方文档](//www.whatwg.org/specs/web-apps/current-work/multipage/the-canvas-element.html#the-canvas-state)中是这样描述的：

> `save()`和`restore()`方法允许你保存和恢复一个`CanvasRenderingContext2D`对象的状态。`save()`把当前状态推入到绘图堆栈中，而`restore()`从绘图堆栈中的顶端弹出最近保存的状态，并且根据这些存储的值来设置当前绘图状态。

简单来说，`save()`主要用来保存目前Canvas的状态，例如`lineWidth`、`fillStyle`、`lineJoin`等，通过`save()`函数它会将目前Canvas的状态推到绘图堆栈中；而`restore()`函数就是从绘图堆栈中弹出上一个Canvas的状态。

画张图来帮助理解：

![](/sites/default/files/blogs/2017/1703/canvas-6-2.png)

### 示例理解save()和restore()

如果上面的描述还是无法帮助你理解`save()`和`restore()`两个方法的话，那我们来写一个简单的示例来帮助大家理解。

先上示例代码：

	function drawScreen () {
	    
	    ctx.fillStyle = '#FA6900';
	    ctx.shadowOffsetX = 5;
	    ctx.shadowOffsetY = 5;
	    ctx.shadowBlur    = 4;
	    ctx.shadowColor   = 'rgba(204, 204, 204, 0.5)';
	    ctx.fillRect(10,10,15,150);
	    ctx.save(); // 将第一个状态推到堆栈中
	    
	    ctx.fillStyle = '#f36';
	    ctx.shadowOffsetX = 10;
	    ctx.shadowOffsetY = 10;
	    ctx.shadowBlur    = 4;
	    ctx.shadowColor   = 'rgba(204, 204, 204, 0.5)';
	    ctx.fillRect(50,10,30,150);
	    ctx.save(); // 将第二个状态推到堆栈中
	    
	    ctx.fillStyle = '#A7DBD7';
	    ctx.shadowOffsetX = 15;
	    ctx.shadowOffsetY = 15;
	    ctx.shadowBlur    = 4;
	    ctx.shadowColor   = 'rgba(204, 204, 204, 0.5)';
	    ctx.fillRect(110,10,45,150);
	    ctx.save(); // 将第三个状态推到堆栈中
	    
	    ctx.restore(); // 取出堆栈3（第三个状态）
	    ctx.beginPath();
	    ctx.arc(225, 75, 22, 0, Math.PI*2, true);
	    ctx.closePath();
	    ctx.fill();
	    
	    ctx.restore(); // 取出堆栈2（第二个状态）
	    ctx.beginPath();
	    ctx.arc(320, 75, 15, 0, Math.PI*2, true);
	    ctx.closePath();
	    ctx.fill();
	    
	    ctx.restore(); // 取出堆栈1（第一个状态）
	    ctx.beginPath();
	    ctx.arc(400, 75, 8, 0, Math.PI*2, true);
	    ctx.closePath();
	    ctx.fill();
	    
	}

你将看到的效果如下：

<div  style="margin-bottom: 20px;"><iframe id="GWmNNm" src="//codepen.io/airen/embed/GWmNNm?height=400&amp;theme-id=0&amp;slug-hash=GWmNNm&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

通过这个示例来阐述`save()`和`restore()`。

首先使用`fillRect()`绘制了一个矩形，并且给这个矩形设置了填充颜色和阴影效果。然后通过`save()`将这个状态添加到Canvas绘图的堆栈中：

	ctx.fillStyle = '#FA6900';
    ctx.shadowOffsetX = 5;
    ctx.shadowOffsetY = 5;
    ctx.shadowBlur    = 4;
    ctx.shadowColor   = 'rgba(204, 204, 204, 0.5)';
    ctx.fillRect(10,10,15,150);
    ctx.save(); // 将第一个状态推到堆栈中

![](/sites/default/files/blogs/2017/1703/canvas-6-3.png)

然又绘制了一个矩形，同样设置了填充和阴影效果，照样通过`save()`将这个状态添加到Canvas绘图的堆栈中：

	ctx.fillStyle = '#f36';
    ctx.shadowOffsetX = 10;
    ctx.shadowOffsetY = 10;
    ctx.shadowBlur    = 4;
    ctx.shadowColor   = 'rgba(204, 204, 204, 0.5)';
    ctx.fillRect(50,10,30,150);
    ctx.save(); // 将第二个状态推到堆栈中

![](/sites/default/files/blogs/2017/1703/canvas-6-4.png)

使用类似的方法将第三个状态添加到Canvas的堆栈中：

	ctx.fillStyle = '#A7DBD7';
    ctx.shadowOffsetX = 15;
    ctx.shadowOffsetY = 15;
    ctx.shadowBlur    = 4;
    ctx.shadowColor   = 'rgba(204, 204, 204, 0.5)';
    ctx.fillRect(110,10,45,150);
    ctx.save(); // 将第三个状态推到堆栈中

![](/sites/default/files/blogs/2017/1703/canvas-6-5.png)

现在通过`restore()`取出绘图堆栈中保存的状态，也就是设置矩形的填充色和阴影效果的属性。然后将这个保存的状态用到一个圆上面。将画出的圆，除了形状不一样之外，其他的图形样式都将一样：

	ctx.restore(); // 取出堆栈3（第三个状态）
    ctx.beginPath();
    ctx.arc(225, 75, 22, 0, Math.PI*2, true);
    ctx.closePath();
    ctx.fill();

![](/sites/default/files/blogs/2017/1703/canvas-6-6.png)

这个圆取出了绘图堆栈中最顶上（最后）一个状态。再次使用`restore()`将第二个状态用于到另一个圆上：

	ctx.restore(); // 取出堆栈2（第二个状态）
    ctx.beginPath();
    ctx.arc(320, 75, 15, 0, Math.PI*2, true);
    ctx.closePath();
    ctx.fill();

![](/sites/default/files/blogs/2017/1703/canvas-6-7.png)

使用类似的方法将第一个状态用到另一个圆上：

	ctx.restore(); // 取出堆栈1（第一个状态）
    ctx.beginPath();
    ctx.arc(400, 75, 8, 0, Math.PI*2, true);
    ctx.closePath();
    ctx.fill();

![](/sites/default/files/blogs/2017/1703/canvas-6-8.png)

看到这里，是不是对Canvas中的`save()`和`restore()`有了一个较清晰的认知了。

## 实例：制作一个扇形

在实际使用当中，`save()`和`restore()`还是非常广泛的，特别是涉及到坐标系统的变换和图形变换方面。最后通过一个简单的示例，让大家体验一下：

	// ctx: Canvas绘图环境
	// x,y: 位移目标点
	// r: 圆弧半径
	// sDeg: 旋转起始角度
	// eDeg: 旋转终点角度
  
	function drawSector(ctx, x, y,r, sDeg, eDeg) {
    	// 初始保存
    	ctx.save();
    
	    //位移到目标点
	    ctx.translate(x, y);
	    ctx.beginPath();
    
	    // 画出圆弧
	    ctx.arc(0, 0, r, sDeg, eDeg);
    
    	// 再次保存以备旋转
    	ctx.save();
    
    	// 旋转至起始角度
    	ctx.rotate(eDeg);
    
    	// 移动到终点，准备连接终点与圆心
    	ctx.moveTo(r, 0);
    
    	// 连接到圆心
    	ctx.lineTo(0, 0);
    
    	// 还原
    	ctx.restore();
    
    	// 旋转至起点角度
    	ctx.rotate(sDeg);
    
    	// 从圆心连接到起点
    	ctx.lineTo(r, 0);
    
    	ctx.closePath();
    	// 还原到最初保存的状态
    	ctx.restore();
	}

然后调用这个封装的函数：

	function drawScreen () {
	    
	    var deg = Math.PI / 180;
	    
	    var obj = {
	      	x: 300,
	      	y: 150,
	      	r: 80,
	      	sDeg: [30, 111, 190, 233, 280, 345],
	      	eDeg: [111, 190, 233, 280, 345, 30],
	      	style: ['#f00', '#0f0', '#00f', '#789', '#abcdef']
	    }
	    
	    for (var i = 0; i < obj.sDeg.length; i++) {
	      	drawSector(ctx,obj.x, obj.y, obj.r, obj.sDeg[i] * deg, obj.eDeg[i] * deg);
	      	ctx.fill();
	      	ctx.fillStyle = obj.style[i];
	    }
	    
	}

最后你能看到的扇形效果如下：

<div  style="margin-bottom: 20px;"><iframe id="gmWLKo" src="//codepen.io/airen/embed/gmWLKo?height=400&amp;theme-id=0&amp;slug-hash=gmWLKo&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 总结

这篇文章主要介绍了Canvas中的`save()`和`restore()`方法。其中`save()`方法会将Canvas的状态推到Canvas绘图的堆栈中，然后通过`restore()`方法从Canvas绘图的堆栈中取出`save()`保存的状态。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/canvas-states.html](//www.w3cplus.com/canvas/canvas-states.html)