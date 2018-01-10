# Canvas学习：封装Canvas绘制基本图形API

到今天这节是有关于在Canvas中绘制图形部分的最后一篇文章。从前面的文章中我们了解到，通过Canvas中的`CanvasRenderingContext2D`对象中的属性和方法，我们可以很轻松的绘制出一些基本图形，比如直线、弧线、矩形、圆形、三角形等。但有很多基本图形的绘制是没有现成的方法，需要通过`CanvasRenderingContext2D`对象中的属性和方法组合在一起才能绘制出来，比如说点划线、箭头和正多边形等。为了更好的帮助大家在Canvas中绘制这些基本图形，可以将这些基本图形的绘制封装起来。今天这篇文章，我们主要来看看怎么将这些函数封装。

## 回忆前面的内容

[Canvas学习系列](//www.w3cplus.com/blog/tags/616.html)到目前为止已整理的都是关于基本图形绘制相关的知识，主要涵盖：

- [Canvas入门准备](//www.w3cplus.com/canvas/introduction-to-prepare.html)
- [Canvas里的坐标系统](//www.w3cplus.com/canvas/canvas-coordinate-system.html)
- [Canvas坐标变换](//www.w3cplus.com/canvas/transformation-coordinates.html)
- [Canvas自定义的坐标变换](//www.w3cplus.com/canvas/custom-of-coordinate-transformation.html)
- [Canvas绘制线段](//www.w3cplus.com/canvas/draw-lines.html)
- [Canvas线型](//www.w3cplus.com/canvas/canvas-line-style.html)
- [Canvas状态：`save()`和`restore()`](//www.w3cplus.com/canvas/canvas-states.html)
- [Canvas绘制矩形](//www.w3cplus.com/canvas/drawing-rectangular.html)
- [Canvas绘制圆和圆弧](//www.w3cplus.com/canvas/drawing-arc-and-circle.html)
- [Canvas绘制贝塞尔曲线](//www.w3cplus.com/canvas/drawing-curve.html)
- [Canvas绘制虚线和圆点线](//www.w3cplus.com/canvas/draw-dashed-and-dotted-lines.html)
- [Canvas绘制箭头](//www.w3cplus.com/canvas/drawing-arrow.html)
- [Canvas绘制正多边形](//www.w3cplus.com/canvas/drawing-regular-polygons.html)

如果您和我一样初次接触Canvas，建议您先花一定的时间阅读上面这些文章，有助于您更好的理解下面的内容。

## Canvas绘图相关属性和方法

`<canvas>`元素有一个`getContext()`方法，这个方法可以用来获取上下文和它的绘画功能。`getContext()`只有一个参数，上下文的格式。我们目前学习的上下文环境都是一个2D环境，所以我们所说的也是对于2D图像而言。它具`CanvasRenderingContext2D`对象。这个对象包括一些绘制图形方法和设置图形样式的属性。

### 状态

`CanvasRenderingContext2D`渲染环境包含了多种绘图的样式状态（属性有线的样式、填充样式、阴影样式、文本样式等），其中该对象提供了两个方法，能帮助我们更好的使用好这些状态：

- **`CanvasRenderingContext2D.save()`**：使用栈保存当前的绘画样式状态，你可以使用 `CanvasRenderingContext2D.restore()` 恢复任何改变
- **`CanvasRenderingContext2D.restore()`**：恢复到最近的绘制样式状态，此状态是通过 `CanvasRenderingContext2D.save()` 保存到”状态栈“中最新的元素

有关于这方面的详细介绍可以阅读[**Canvas状态：`save()`和`restore()`** ](//www.w3cplus.com/canvas/canvas-states.html)一文。

### 变换

`CanvasRenderingContext2D`渲染背景中的对象会有一个当前的变换矩阵，一些方法可以对其进行控制。当创建当前的默认路径、绘制文本、图形等会应用此变换矩阵。

- **`CanvasRenderingContext2D.rotate(rad)`**：在变换矩阵中增加旋转，角度变量表示一个顺时针旋转角度，并且用弧度表示
- **`CanvasRenderingContext2D.scale(sx, sy)`**：根据`sx`水平方向和`sy`垂直方向，为Canvas单位添加缩放变换
- **`CanvasRenderingContext2D.translate(dx,dy)`**：通过在网格中移动Canvas和Canvas原点`dx`水平方向、原点`dy`垂直方向，添加平移变换
- **`CanvasRenderingContext2D.transform(a, b, c, d, e, f)`**：使用方法参数描述的矩阵多次叠加当前的变换矩阵
- **`CanvasRenderingContext2D.setTransform(a, b, c, d, e, f)`**：重新设置当前的变换为单位矩阵，并使用同样的变量调用`CanvasRenderingContext2D.transform(a, b, c, d, e, f)`方法
- **`CanvasRenderingContext2D.resetTransform()`**：使用单位矩阵重新设置当前的变换

有关于Canvas中的变换涉及到了Canvas的坐标系统相关知识，建议您阅读前面介绍的[**Canvas里的坐标系统**](//www.w3cplus.com/canvas/canvas-coordinate-system.html)、[**Canvas坐标变换**](//www.w3cplus.com/canvas/transformation-coordinates.html)和[**Canvas自定义的坐标变换**](//www.w3cplus.com/canvas/custom-of-coordinate-transformation.html)三篇文章。

### 线型

`CanvasRenderingContext2D`提供了相关的方法和属性控制如何在Cavnas画布中绘制线的样式风格：

- **`CanvasRenderingContext2D.lineWidth`**：线的宽度，默认值`1.0`
- **`CanvasRenderingContext2D.lineCap`**：线末端的类型。允许的值：`butt`（默认值）、`round`和`square`
- **`CanvasRenderingContext2D.lineJoin`**：定义两线相交拐点的类型。允许值：`miter`（默认值）、`round`和`bevel`
- **`CanvasRenderingContext2D.miterLimit`**：斜接面限制比例，默认`10`

有关于这几个属性的详细介绍，可以阅读[Canvas绘制线段](//www.w3cplus.com/canvas/draw-lines.html)和[Canvas线型](//www.w3cplus.com/canvas/canvas-line-style.html)。

### 填充和描边

填充和描边有对应的属性和方法。其中属性主要用于填充设计用于图形内部的颜色和样式，描边设计用于图形的边线；方法主要用于填充路径和描边路径：

- **`CanvasRenderingContext2D.fillStyle`**：图形内部的颜色和样式（填充），默认`#000`
- **`CanvasRenderingContext2D.strokeStyle`**：图形边线的颜色和样式（描边），默认`#000`
- **`CanvasRenderingContext2D.fill()`**：使用当前的样式填充子路径
- **`CanvasRenderingContext2D.stroke()`**：使用当前的样式描边子路径

### 路径

Canvas的`CanvasRenderingContext2D`对象中用于操作路径的方法主要有：

- **`CanvasRenderingContext2D.beginPath()`**：清空子路径列表开始一个新的路径。当你想创建一个新的路径时，调用此方法
- **`CanvasRenderingContext2D.closePath()`**：使笔点返回到当前子路径的起始点。它尝试从当前点到起点绘制一条直线。如果图形已经是封装的或者只有一个点，那么此方法不会做任何操作
- **`CanvasRenderingContext2D.moveTo(x, y)`**：将一个新的子路径的起始点移动到`(x, y)`坐标
- **`CanvasRenderingContext2D.lineTo(x, y)`**：使用直线连接子路径的最后的点到`(x, y)`坐标
- **`CanvasRenderingContext2D.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)`**：添加一个三次贝塞尔曲线路径。该方法需要三个点。 第一、第二个点是控制点，第三个点是结束点。起始点是当前路径的最后一个点，绘制贝赛尔曲线前，可以通过调用 `moveTo()` 进行修改
- **`CanvasRenderingContext2D.quadraticCurveTo(cpx, cpy, x, y)`**：添加一个二次贝塞尔曲线路径
- **`CanvasRenderingContext2D.arc(x, y, r, startAngle, endAngle, [anticlockwise])`**：绘制一段圆弧路径，圆弧路径的圆心在`(x,y)`位置，圆弧的半径为`r`，根据`anticlockwise`指定圆弧的方向从`startAngle`开始绘制，到`endAngle`结束（也就是旋转方向，默认为顺时针）
- **`CanvasRenderingContext2D.arcTo(x1, y1, x2, y2, radius)`**：根据控制点和半径绘制圆弧路径，使用直线连接前一个点
- **`CanvasRenderingContext2D.rect(x,y,width,height)`**：创建一个矩形路径，矩形的起始点位置是`(x, y)`，尺寸为`width`和`height`

这些路径方法可以帮助我们在Canvas中绘制直线、曲线、弧线，贝塞尔曲线、圆、矩形，甚至结合这些路径方法，可以绘制出其他的图形，比如矩形、多边形，三角形或者其他的复杂图形。这些方法也是绘制图形的基本方法，也是核心方法，只有更好的掌握这些方法，才能更好的在Canvas中绘制出你自己想要的图形。

有关于这些方法的使用，在下面这些文章都有详细介绍过：

- [Canvas绘制线段](//www.w3cplus.com/canvas/draw-lines.html)
- [Canvas绘制矩形](//www.w3cplus.com/canvas/drawing-rectangular.html)
- [Canvas绘制圆和圆弧](//www.w3cplus.com/canvas/drawing-arc-and-circle.html)
- [Canvas绘制贝塞尔曲线](//www.w3cplus.com/canvas/drawing-curve.html)
- [Canvas绘制虚线和圆点线](//www.w3cplus.com/canvas/draw-dashed-and-dotted-lines.html)
- [Canvas绘制箭头](//www.w3cplus.com/canvas/drawing-arrow.html)
- [Canvas绘制正多边形](//www.w3cplus.com/canvas/drawing-regular-polygons.html)

### 绘制矩形

在Canvas中除了可以使用`CanvasRenderingContext2D`对象中路径方法绘制之外，还专门提供了几个方法来绘制矩形：

- **`CanvasRenderingContext2D.clearRect(x, y, width, height)`**：设置指定矩形区域内（以`(x,y)`为起点，范围是`(width, height)`）所有像素变成透明，并擦除之前绘制的所有内容
- **`CanvasRenderingContext2D.fillRect(x, y, width, height)`**： 绘制填充矩形，矩形的起点在`(x, y)`位置，矩形的尺寸是`width`和`height`
- **`CanvasRenderingContext2D.strokeRect(x, y, width, height)`**：在Canvas中，使用当前的绘画样式，描绘一个起点在`(x, y)`位置，尺寸为`width`和`height`的矩形

前面我们专门花了一节的内容：[Canvas绘制矩形](//www.w3cplus.com/canvas/drawing-rectangular.html)来介绍这几个方法的使用。

## 封装绘图的API

虽然Canvas中的`CanvasRenderingContext2D`对象有很多方法和属性能帮助我们绘不同的图形，但如果你的工作每天都跟图形打交道的话，建议你使用这些方法和属性封装出绘图的函数或者方法。在接下来的内容我们来看看怎么封装绘制基本图形的函数。

首先回想一下，我们常常碰到的基本图形有：线段（分别，实现、虚线和圆点线）、箭头、弧线、圆、矩形、扇形和正多边形等。那下面的内容就是来看看怎么写代码。

### 声明环境

在Canvas中都有一个2D的绘图环境，那么我们可以简单的封装一个`initDrawCanvas()`函数来处理：

    // 声明Canvas对象
    var canvas;
    // 声明Context对象
    function initDrawCanvas(canvas, ctx) {
        this.canvas = canvas;
        this.ctx = ctx;
    }

### 线段

线段我们主要常看到的有实线（Solid）、虚线（Dashed）和圆点线（Dotted）。在学习几何知识时，我们知道，两点确定一条线段。那么在我们封装的函数中，我们需要两个点的坐标，比如起始点坐标`(startX, startY)`和结束点坐标`(endX, endY)`。另外为了更好的通过封装的函数控制绘制的线段，我们还需要设置线段的宽度和颜色，也就是需要另外两个参数，比如使用`lineWidth`来传线宽，`color`传线段的颜色。

前面也说了，线段分为三种，也就是说我们封装的函数也封装成三个，比如：

- 实线线段：`drawSolidLine()`
- 虚线线段：`drawDashedLine()`
- 圆点线段：`drawDottedLine()`

通过前面的知识，我们可以使用`moveTo()`和`lineTo()`两个方法来控制线段的起点和终点，另外`lineWidth`和`fillStyle`（或者`strokeStyle`）控制线段粗线和颜色。如此一来，我们可以这样来写`drawSolidLine()`函数：

    // 绘制实线线段
	// @param {Number} startX - 线段起点x轴坐标
	// @param {Number} startY - 线段起点y轴坐标
	// @param {Number} endX - 线段终点x轴坐标
	// @param {Number} endY - 线段终点y轴坐标
	// @param {Number} lineWidth - 线宽
	// @param {String} color - 线颜色
	function drawSolidLine(startX, startY, endX, endY, lineWidth, color){
	    ctx.save();
		ctx.strokeStyle = color;
		ctx.lineWidth = lineWidth;
		ctx.beginPath();
		ctx.moveTo(startX, startY);
		ctx.lineTo(endX, endY);
		ctx.stroke();
		ctx.restore();
	}

对于虚线的绘制，在Canvas的对象中提供了一个`setLineDash()`方法，在这个方法中，我们可以传递一个数组，来控制虚线的间距和长度。在`drawSolidLine()`基础上我们进行一下扩展，来封装`drawDashedLine()`函数：

    // 绘制虚线
	// @param {Number} startX - 线段起点x轴坐标
	// @param {Number} startY - 线段起点y轴坐标
	// @param {Number} endX - 线段终点x轴坐标
	// @param {Number} endY - 线段终点y轴坐标
	// @param {Array} setLineDash - 点划线间距
	// @param {Number} lineWidth - 线宽
	// @param {String} color - 线段颜色
	function drawDashedLine(startX, startY, endX, endY, setLineDash, lineWidth, color) {
	    ctx.save();
		ctx.lineWidth = lineWidth;
		ctx.strokeStyle = color;
		ctx.beginPath();
		ctx.setLineDash(setLineDash);				
		ctx.moveTo(startX, startY);
		ctx.lineTo(endX, endY);
		ctx.closePath();
		ctx.stroke();
		ctx.restore();
	} 

对于圆点线绘制函数相对而言要较为复杂一点，因为在Canvas中没有提供一个类似`setLineDash()`方法，让我们来控制圆点的圆点大小以及间距。既然我们绘制的是圆点线，那么在Canvas中我们可以使用`.arc()`方法来绘制圆。如此一来就好办了：

    // 绘制圆点线
	// @param {Number} startX - 线段起点x轴坐标
	// @param {Number} startY - 线段起点y轴坐标
	// @param {Number} endX - 线段终点x轴坐标
	// @param {Number} endY - 线段终点y轴坐标
	// @param {Number} interval - 间隔
	// @param {Number} radius - 圆点半径
	// @param {String} color - 线段颜色
	function drawDottedLine(startX, startY, endX, endY, radius, interval, color) {
	    if (!interval) {
		    interval = 5;
		}
		
        var isHorizontal = true;
		
        if (startX == endX) {
		    isHorizontal = false;
		}

		var len = isHorizontal ? endX - startX : endY - startY;

        ctx.strokeStyle = color;
		ctx.fillStyle = color;
		
        ctx.save();
		ctx.beginPath();
		
        ctx.moveTo(startX, startY);
		
        var progress = 0;
		
        while (len > progress) {
		    progress += interval;

			if (progress > len) {
			    progress = len;
			}

			if (isHorizontal) {
			    ctx.moveTo(startX + progress, startY);
				ctx.arc(startX + progress, startY, radius, 0, Math.PI * 2, true);
				ctx.fill();
			} else {
			    ctx.moveTo(startX, endX + progress);
				ctx.arc(startX, startY + progress, radius, 0, Math.PI * 2, true);
				ctx.fill();
			}
		}
        ctx.restore();
	}

通过前面的知识，我们可以通过`lineJoin`和`lineCap`控制线型，但在上面的函数封装中，并没有做这方面相关的考虑。不过并不要紧，在实际使用可以通过`ctx.lineJoin`或`ctx.lineCap`来设置，当然你也可以修改上面的函数，将这个参数传进去。

### 矩形

在Canvas中可能通过`rect()`路径的绘制，也可以通过`fillRect()`和`strokeRect()`绘制矩形。使用这些方法绘制矩形都有相同的参数：

- `(x,y)`：矩形起点坐标，也就是矩形左上角的坐标
- `width`：矩形的宽度
- `height`：矩形的高度

在绘制矩形我们有填充和描边矩形之分，另外在Canvas中可以直接使用`fillRect()`和`strokeRect()`来绘制。只不过Canvas中自带的方法只能绘制直角矩形，如果我们要绘制圆角矩形，那还是需要借用`arcTo()`方法来制作圆角。为了更好的区分直角矩形和圆角矩形，我们各自为他们封装了一个函数：

- `drawRect()`：直角矩形
- `drawRoundedRect()`：圆角矩形

下面代码是各自函数对应的：

    // 封装直角矩形
	// 矩形包括： 填充矩形、边框矩形和清除矩形区域
	// @param {Number} x - 矩形起点的x坐标
	// @param {Number} y - 矩形起点的y坐标
	// @param {Number} width - 矩形宽度
	// @param {Number} height - 矩形高度
	// @param {Boolean} isClear - 是否绘制清除画布的矩形区域； true则是绘制一个清除画布矩形区域, false就是绘制其他两种矩形
	// @param {Boolean} isFill - 是否填充；true绘制填充矩形， false绘制边框矩形
	// @param {String} color - 矩形颜色
	function drawRect(x, y, width, height, isClear, isFill, color) {
	    // 为true表示绘制清除画布的矩形区域，那么传入的isFill，color值可以为任意值
		if (isClear) {
		    ctx.clearRect(x, y, width, height);
		} else {
		    if (isFill) {
			    ctx.fillStyle = color;
				ctx.fillRect(x, y, width, height);
			} else {
			    ctx.strokeStyle = color;
				ctx.strokeRect(x, y, width, height);
			}
		}
	}

其中`isClear`是一个布尔值，用来判断是否要绘制一个清除矩形区域，功能对应的是Canvas中的`clearRect()`方法。而`isFill`也是一个布尔值，用来判断是否绘制一个填充矩形还是描边矩形，如果值为`true`调用`fillRect()`绘制一个填充矩形，`false`则调用`strokeRect()`绘制一个描边矩形。最后传了一个`color`参数，用来控制矩形的填充颜色或者描边颜色。

从上面的代码中可以看出，绘制描边矩形时并没有设置边框的粗线，如果你绘制一个描边矩形时，需要设置边框粗组时，在实际调用时，可以借用`ctx.lineWidth`属性来设置。

**注：**`drawRect()`函数只能绘制填充或描边直角矩形，如果你需要绘制具有填充和描边的一个矩形时，上面的函数就无能为力了，当然你可以通过其他的方法来进行封装，这里就不做过多的阐述了。有兴趣的同学可以自己动手，比如封装一个`xxx`函数。

上面是封装绘制直角矩形的函数，接下来看圆角矩形的函数的封装。大致方法是类似的，只不过我们封装圆角矩形时，使用了`arcTo()`方法来实现圆角，而这个圆角弧度需要一个半径，所以在上面的直角矩形基础上再传一个`radius`参数：

    // 绘制圆角矩形
	// @param {Number} x - 矩形左上角x轴坐标
	// @param {Number} y - 矩形左上角y轴坐标
	// @param {Number} width - 矩形的宽度
	// @param {Number} height - 矩形的高度
	// @param {Number} radius - 矩形圆角的半径
	// @param {Boolean} isFill - 是否绘制填充，true填充，false边框
	// @param {String} color - 矩形的颜色
	function drawRoundedRect(x, y, width, height, radius, isFill, color) {
	    ctx.save();
        ctx.beginPath();
		ctx.moveTo(x + radius, y);
		ctx.arcTo(x + width, y, x + width, y + radius, radius);
		ctx.arcTo(x + width, y + height, x + width - radius, y + height, radius);
		ctx.arcTo(x, y + height, x, y + height - radius, radius);
		ctx.arcTo(x, y, x + radius, y, radius);
		ctx.closePath();
					
		if (isFill) {
		    ctx.fillStyle = color;
			ctx.fill();
		} else {
		    ctx.strokeStyle = color;
			ctx.stroke();
		}
        ctx.restore();
	}

有关于矩形的绘制和对应函数封装，在前面的[Canvas绘制矩形](//www.w3cplus.com/canvas/drawing-rectangular.html)一文中或多或少的介绍过，对于`arcTo()`的详细使用，可以阅读[Canvas绘制圆和圆弧](//www.w3cplus.com/canvas/drawing-arc-and-circle.html)一文。

### 圆、圆弧和扇形

在Canvas中可以使用`arc()`和`arcTo()`绘制圆弧、圆和扇形等基本形状。当绘制圆弧时，当`startAngle`角度值到`endAngle`角度值是`0~360`时，就可以绘制一个圆。所以我们在这里只需要封装两个函数：

- `drawArc()`：圆弧（或圆）函数
- `drawSector()`：扇形函数

他们具有相同的参数，圆心`(x,y)`、半径`radius`、`startAngle`起始弧度、`endAngle`结束弧度和`anticlockwise`旋转方向。由于`startAngle`和`endAngle`只接受弧度单位值，所以在封装这两个函数之前，先封装一个角度`deg`和弧度`rad`之间的转换函数，方便后面函数的使用：

    // 将角度转换为弧度
    // @param {Number} deg - 角度值
    function getAngle(deg) {
        return Math.PI * deg / 180;
    }

    // 绘制圆弧或圆
	// 分类：填充圆弧和边框圆弧
	// @param {Number} x - 圆心x轴坐标
	// @param {Number} y - 圆心y轴坐标
	// @param {Number} radius - 圆弧的半径
	// @param {Number} startAngle - 开始的弧度（开始角度），只接受弧度单位，需要将deg先转换为rad： rad = Math.PI * deg / 180
	// @param {Number} endAngle - 结束的弧度（结束的角度）
	// @param {Boolean} anticlockwise - 旋转方向；true为逆时针，false为顺时针
	// @param {Boolean} isFill - 是否填充；true为填充，false为边框
	// @param {Boolean} isOnlyArc - 是否仅绘制弧边，如果使用closePath()终点和起点会连接到一起，否则不会。true时不连接，false连接
	// @param {String} color - 圆弧的颜色
	function drawArc(x, y, radius, startAngle, endAngle, anticlockwise, isOnlyArc, isFill, color) {
	
        if (isFill) {
		    ctx.fillStyle = color;
            ctx.save();
			ctx.beginPath();
			ctx.arc(x, y, radius, getAngle(startAngle), getAngle(endAngle), anticlockwise);
			ctx.closePath();
			ctx.fill();
            ctx.restore();
		} else {
		    ctx.strokeStyle = color;
            ctx.save();
			ctx.beginPath();
			ctx.arc(x, y, radius, getAngle(startAngle), getAngle(endAngle), anticlockwise);
			
            if (isOnlyArc) {

			} else {
			    ctx.closePath();
			}
			ctx.stroke();
            ctx.restore();
		}
	} 

    // 绘制扇形
	// @param {Number} x - 圆心x轴坐标
	// @param {Number} y - 圆心y轴坐标
	// @param {Number} radius - 圆半径
	// @param {Number} startAngle - 开始弧度
	// @param {Number} endAngle - 结束弧度
	// @param {Number} anticlockwise - 旋转方向， true逆时针，false顺时针
	// @param {Boolean} isFill - true为填充，false为边框
	// @param {String} color - 扇形的颜色
	function drawSector(x, y, radius, startAngle, endAngle, anticlockwise, isFill, color) {
	    ctx.save();
        ctx.beginPath();
		ctx.moveTo(x, y);
		ctx.arc(x, y, radius, getAngle(startAngle), getAngle(endAngle), false);
		ctx.closePath();
					
		if (isFill) {
		    ctx.fillStyle = color;
			ctx.fill();
		} else {
		    ctx.strokeStyle = color;
			ctx.stroke();
		}
        ctx.restore();
	}

### 绘制箭头

在Canvas中没有直接的方法可以绘制箭，但@Patrick Horgan在《[Drawing lines and arcs with arrow heads on HTML5 Canvas](//www.dbp-consulting.com/tutorials/canvas/CanvasArrow.html)》一文中把绘制箭对的函数已经封装好了。我直接把代码放这里：

    // From: http://www.dbp-consulting.com/tutorials/canvas/CanvasArrow.html
  	// Draw arrow head
	function drawHead (x0, y0, x1, y1, x2, y2, style, color, width) {
	    
        if (typeof(x0) == 'string') {
		    x0 = parseInt(x0);
		}
		
        if (typeof(y0) == 'string') {
		    y0 = parseInt(y0);
		}
		
        if (typeof(x1) == 'string') {
		    x1 = parseInt(x1);
		}
		
        if (typeof(y1) == 'string') {
		    y1 = parseInt(y1);
		}
		
        if (typeof(x2) == 'string') {
		    x2 = parseInt(x2);
		}
		
        if (typeof(y2) == 'string') {
		    y2 = parseInt(y2);
		}
				
		var radius = 3,
		twoPI = 2 * Math.PI;
				
		ctx.save();
		ctx.beginPath();
		ctx.strokeStyle = color;
		ctx.fillStyle = color;
		ctx.lineWidth = width;
		ctx.moveTo(x0, y0);
		ctx.lineTo(x1, y1);
		ctx.lineTo(x2, y2);
				
		switch (style) {
		    case 0:
			    var backdist = Math.sqrt(((x2 - x0) * (x2 - x0)) + ((y2 - y0) * (y2 - y0)));
				ctx.arcTo(x1, y1, x0, y0, .55 * backdist);
				ctx.fill();
				break;
			case 1:
			    ctx.beginPath();
				ctx.moveTo(x0, y0);
				ctx.lineTo(x1, y1);
				ctx.lineTo(x2, y2);
				ctx.lineTo(x0, y0);
				ctx.fill();
				break;
			case 2:
			    ctx.stroke();
				break;
			case 3:
			    var cpx = (x0 + x1 + x2) / 3;
				var cpy = (y0 + y1 + y2) / 3;
				ctx.quadraticCurveTo(cpx, cpy, x0, y0);
				ctx.fill();
				break;
			case 4:
			    var cp1x, cp1y, cp2x, cp2y, backdist;
				var shiftamt = 5;
				if (x2 == x0) {
				    backdist = y2 - y0;
					cp1x = (x1 + x0) / 2;
					cp2x = (x1 + x0) / 2;
					cp1y = y1 + backdist / shiftamt;
					cp2y = y1 - backdist / shiftamt;
				} else {
				    backdist = Math.sqrt(((x2 - x0) * (x2 - x0)) + ((y2 - y0) * (y2 - y0)));
					var xback = (x0 + x2) / 2;
					var yback = (y0 + y2) / 2;
					var xmid = (xback + x1) / 2;
					var ymid = (yback + y1) / 2;
					var m = (y2 - y0) / (x2 - x0);
					var dx = (backdist / (2 * Math.sqrt(m * m + 1))) / shiftamt;
					var dy = m * dx;
					cp1x = xmid - dx;
					cp1y = ymid - dy;
					cp2x = xmid + dx;
					cp2y = ymid + dy;
				}
				ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x0, y0);
				ctx.fill();
				break;
			}
			ctx.restore();
		}
			
  		// draw arrow
		function drawArrow(x1, y1, x2, y2, style, which, angle, d, color, width) {
		
            if (typeof(x1) == 'string') {
			    x1 = parseInt(x1);
			}
			
            if (typeof(y1) == 'string') {
			    y1 = parseInt(y1);
			}
			
            if (typeof(x2) == 'string') {
			    x2 = parseInt(x2);
			}
			
            if (typeof(y2) == 'string') {
			    y2 = parseInt(y2);
			}
			
            style = typeof(style) != 'undefined' ? style : 3;
			which = typeof(which) != 'undefined' ? which : 1;
			angle = typeof(angle) != 'undefined' ? angle : Math.PI / 9;
			d = typeof(d) != 'undefined' ? d : 10;
			color = typeof(color) != 'undefined' ? color : '#000';
			width = typeof(width) != 'undefined' ? width : 1;
			var toDrawHead = typeof(style) != 'function' ? drawHead : style;
			var dist = Math.sqrt((x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 - y1));
			var ratio = (dist - d / 3) / dist;
			var tox, toy, fromx, fromy;
			
            if (which & 1) {
				tox = Math.round(x1 + (x2 - x1) * ratio);
				toy = Math.round(y1 + (y2 - y1) * ratio);
			} else {
				tox = x2;
				toy = y2;
			}
				
			if (which & 2) {
				fromx = x1 + (x2 - x1) * (1 - ratio);
				fromy = y1 + (y2 - y1) * (1 - ratio);
			} else {
				fromx = x1;
				fromy = y1;
			}
				
			ctx.beginPath();
			ctx.strokeStyle = color;
			ctx.lineWidth = width;
			ctx.moveTo(fromx, fromy);
			ctx.lineTo(tox, toy);
			ctx.stroke();
				
			var lineangle = Math.atan2(y2 - y1, x2 - x1);
			var h = Math.abs(d / Math.cos(angle));
			
            if (which & 1) {
				var angle1 = lineangle + Math.PI + angle;
				var topx = x2 + Math.cos(angle1) * h;
				var topy = y2 + Math.sin(angle1) * h;
				var angle2 = lineangle + Math.PI - angle;
				var botx = x2 + Math.cos(angle2) * h;
				var boty = y2 + Math.sin(angle2) * h;
				toDrawHead(topx, topy, x2, y2, botx, boty, style, color, width);
			}
				
			if (which & 2) {
				var angle1 = lineangle + angle;
				var topx = x1 + Math.cos(angle1) * h;
				var topy = y1 + Math.sin(angle1) * h;
				var angle2 = lineangle - angle;
				var botx = x1 + Math.cos(angle2) * h;
				var boty = y1 + Math.sin(angle2) * h;
				toDrawHead(topx, topy, x1, y1, botx, boty, style, color, width);
			}
		}
  
		// draw arced arrow
		function drawArcedArrow(x, y, r, startangle, endangle, anticlockwise, style, which, angle, d, color, width) {
		
        	style = typeof(style) != 'undefined' ? style : 3;
			which = typeof(which) != 'undefined' ? which : 1;
			angle = typeof(angle) != 'undefined' ? angle : Math.PI / 8;
			d = typeof (d) != 'undefined' ? d : 10;
			color = typeof(color) != 'undefined' ? color : '#000';
			width = typeof(width) != 'undefined' ? width : 1;
				
			ctx.save();
			ctx.beginPath();
			ctx.lineWidth = width;
			ctx.strokeStyle = color;
			ctx.arc(x, y, r, startangle, endangle, anticlockwise);
			ctx.stroke();
			var sx, sy, lineangle, destx, desty;
			ctx.strokeStyle = 'rgba(0,0,0,0)';
		
        	if (which & 1) {
				sx = Math.cos(startangle) * r + x;
				sy = Math.sin(startangle) * r + y;
				lineangle = Math.atan2(x - sx, sy - y);
		
        		if (anticlockwise) {
					destx = sx + 10 * Math.cos(lineangle);
					desty = sy + 10 * Math.sin(lineangle);
				} else {
					destx = sx - 10 * Math.cos(lineangle);
					desty = sy - 10 * Math.sin(lineangle);
				}
				drawArrow(sx, sy, destx, desty, style, 2, angle, d, color, width);
			}
				
			if (which & 2) {
				sx = Math.cos(endangle) * r + x;
				sy = Math.sin(endangle) * r + y;
				lineangle = Math.atan2(x - sx, sy - y);
		
        		if (anticlockwise) {
					destx = sx - 10 * Math.cos(lineangle);
					desty = sy - 10 * Math.sin(lineangle);
				} else {
					destx = sx + 10 * Math.cos(lineangle);
					desty = sy + 10 * Math.sin(lineangle);
				}
		
        		drawArrow(sx, sy, destx, desty, style, 2, angle, d, color, width);
			}
			ctx.restore();
		}

是不是好复杂呀。如果上面代码看起来痛苦的话，可以阅读@Patrick Horgan写的《[Drawing lines and arcs with arrow heads on HTML5 Canvas](//www.dbp-consulting.com/tutorials/canvas/CanvasArrow.html)》文章或者阅读早前整理的[Canvas绘制箭头](//www.w3cplus.com/canvas/drawing-arrow.html)一文。

### 绘制正多边形

绘制正多边形也相对于其他的绘图函数封装而言也较为复杂一点。我们将封装一个`drawStarPolygons()`函数，这个函数既能实现正多边形绘制，也能实现星形多边形的绘制。具体代码如下：

    // 绘制正多边形
	// @param {Number} xCenter 中心坐标X点
	// @param {Number} yCenter 中心坐标Y点
	// @param {Number} radius 外圆半径
	// @param {Number} sides 多边形边数
	// @param {Number} sideIndent (0 ~ 1)
	// @param {Number} alpha 角度 默认270度
	// @param {Boolean} isFill true填充，false边框
	// @param {String} color 正多边形颜色 
	function drawStarPolygons(xCenter, yCenter, radius, sides, sideIndent, alpha, isFill, color) {
				
	    var sideIndentRadius = radius * (sideIndent || 0.38);
		var radAngle = alpha ? alpha * Math.PI / 180 : -Math.PI / 2;
		var radAlpha = Math.PI * 2 / sides / 2;

		ctx.save();
		ctx.beginPath();

		var xPos = xCenter + Math.cos(radAngle) * radius;
		var yPos = yCenter + Math.sin(radAngle) * radius;

		ctx.moveTo(xPos, yPos);

		for (var i = 1; i <= sides * 2; i++) {
		    var rad = radAlpha * i + radAngle;
			var len = (i % 2) ? sideIndentRadius : radius;
			var xPos = xCenter + Math.cos(rad) * len;
			var yPos = yCenter + Math.sin(rad) * len;

			ctx.lineTo(xPos, yPos);

		}

		ctx.closePath();

		if (isFill) {
		    ctx.fillStyle = color;
			ctx.fill();
		} else {
		    ctx.strokeStyle = color;
			ctx.stroke();
		}
	}

详细的可以阅读[Canvas绘制正多边形](//www.w3cplus.com/canvas/drawing-regular-polygons.html)一文。

### 示例

前面我们封装了一些我们常常需要使用的绘图函数。那我们拿一个示例来验证一下。比如说我们要绘制一个时钟：

    var canvas = document.getElementById('canvasOne');
	var ctx = canvas.getContext('2d');
	var w = canvas.width = window.innerWidth;
	var h = canvas.height = window.innerHeight;
	initDrawCanvas(canvas, ctx);

	// 绘制一个时钟

			
	var radius = 150;
	var handTruncation = canvas.width / 25;
	var	hourHandTruncation = canvas.width / 10;
			
	// 绘制时钟刻度盘
	function drawClockFace() {
	    
        // step1: 绘制时钟的外圆和圆心
		ctx.lineWidth = 4;
		ctx.translate(w / 2, h / 2);
				
		drawArc(0, 0, radius, 0, 360, true, true, false, '#000');
		drawArc(0, 0, 10, 0, 360, true, true, true, '#000');
		
		// step2: 绘制时钟刻度线
		for (var i = 0; i < 60; i++) {
			var rad = getAngle(i * 6);
			ctx.save();
			ctx.rotate(rad);

			if (i % 5 === 0) {
				drawSolidLine(radius - 15, -1, radius - 4, -1, 4, '#000');
			} else {
				drawSolidLine(radius - 8, -1, radius - 4, -1, 2, '#999');					
			}
			ctx.restore();
		}
				
		// step3: 绘制时钟数字
		ctx.font = radius * 0.15 + "px arial";
		ctx.textBaseline = "middle";
		ctx.textAlign = "center";

		for (var i = 1; i < 13; i++) {
			var ang = getAngle(30 * i);
			ctx.fillText(i.toString(), radius * 0.80 * Math.sin(ang), -radius * 0.80 * Math.cos(ang));
		}
	}

	// 绘制时钟针
	function drawHand(angle, length, width, color) {
		var endX = Math.sin(angle) * length;
		var endY = -Math.cos(angle) * length;
		ctx.lineCap = 'round';
		drawSolidLine(0, 0, endX, endY, width, color);
	}
			
	function drawHands(radius) {
		var now = new Date();
		var hour = now.getHours();
		var minute = now.getMinutes();
		var second = now.getSeconds();

		hour = hour % 12;
		hour = getAngle(30) * hour + getAngle(30) * minute / 60 + getAngle(30) * second / 3600;
		minute = Math.PI / 30 * minute + second * Math.PI / 1800;
		second = Math.PI / 30 * second;
		drawHand(hour, radius * 0.4, radius * 0.07); // 时针
		drawHand(minute, radius * 0.6, radius * 0.05); // 分针
		drawHand(second, radius * 0.7, radius * 0.03, 'red'); // 秒针
	}
			
	function drawClock() {
		ctx.resetTransform();
		drawRect(0, 0, w, h, true);
		drawClockFace();
		drawHands(radius);
	}
	setInterval(drawClock, 1000);

最终效果如下：

<div  style="margin-bottom: 20px;"><iframe id="pPjRgN" src="//codepen.io/airen/embed/pPjRgN?height=400&amp;theme-id=0&amp;slug-hash=pPjRgN&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 总结

这篇文章我们整理了Canvas中`CanvasRenderingContext2D`对象中自带绘制基本图形的方法、属性和样式。并且借助这些方法封装了一些绘制基本图形的函数，比如绘制线段、矩形、圆和正多边形的。最后绘制了一张图，把相关的知识汇总在一起。这篇文章也是介绍Canvas绘制基本图形的最后一篇文章了。

[![](/sites/default/files/blogs/2017/1704/CanvasDrawAPI.png)](//www.w3cplus.com/sites/default/files/blogs/2017/1704/CanvasDrawAPI.png)

**注：**点击图片可以下载原图。

在后续的文章中，我们将继续介绍Canvas中相关的知识点，感兴趣的同学欢迎持续关注后续内容的更新。			

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/canvas-drawing-function.html](//www.w3cplus.com/canvas/canvas-drawing-function.html)