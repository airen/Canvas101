# Canvas学习：绘制正多边形

到目前为止，我们了解了如何在Canvas中绘制[线段](//www.w3cplus.com/canvas/draw-lines.html)、[矩形](//www.w3cplus.com/canvas/drawing-rectangular.html)、[圆或圆弧线](//www.w3cplus.com/canvas/drawing-arc-and-circle.html)和[贝塞尔曲线](//www.w3cplus.com/canvas/drawing-curve.html)等。这些都是Canvas的`CanvasRenderingContext2D`对象自身提供绘制基本图形。但是，我们肯定需要在Canvas中绘制除此之外的其他图形，比如前面所说的[绘制箭头](//www.w3cplus.com/canvas/drawing-arrow.html)或者说我们今天要聊的绘制正多边形。

![](/sites/default/files/blogs/2017/1703/canvas-11-1.jpg)

## 正多边形

[维基百科](//zh.wikipedia.org/wiki/%E6%AD%A3%E5%A4%9A%E8%BE%B9%E5%BD%A2)上是这样描述的：**正多边形是所有角都相等、并且所有边都相等的简单多边形，简单多边形是指在任何位置都不与自身相交的多边形**。

### 正多边形的特性

正`n`边形每个内角为`(1 - 2 / n) * 180`或者表示为`(n - 2) * 180 / n`角度。也可以用弧度表示为`(n - 2) * π / n` 或者`(n - 2) / 2n`。

正多边形的所有顶点都在同一个外接圆上，每个正多边形都有一个外接圆，这也称为**圆内接正多边形**。

![](/sites/default/files/blogs/2017/1703/canvas-11-2.png)

把一个圆分成相等的一些弧，就可以得到这个圆的内接正多边形（Regular Polygon），这个圆就是正多边形的外接圆。外接圆的圆心叫做这个正多边形的中心，外接圆的半径叫做正多边形的半径（Radius），正多边形每一边所对的圆心角叫做正多边形的中心角（圆心角，Central Angle），中心到正多边形的一边的距离叫做正多边形的边心距（Apothem）。

一个圆的圆周是`2π`，当边的数目为`n`时，每一个中心角都是`2π / n`。半径`r`、边心距`a`、边长`s` 都存在着固定的关系，已知其中的两个，都可由上面的公式求出第三个。

当`n`接近`48`这个值时，那这个正多边形也就接近是一个圆了。如下图所示：

![](/sites/default/files/blogs/2017/1703/canvas-11-4.png)

### 正多边形属性

先上张图：

![](/sites/default/files/blogs/2017/1703/canvas-11-3.png)

上图描述了正多边形的相关属性：

- 正多边形的中心点正好是一个正多边形的外接圆的圆心
- 正多边形每条边的长度都相等，如上图中的`x`
- 正多边形的每个内角都相等，如上图中的`β`
- 正多边形的每个外角都相等，如上图中的`α`
- 正多边形的中心角都相等，如上图中的`θ`
- 正多边形的中心点距正多边形的内切圆的半行为`r`
- 正多边形的顶点数和边数相等，常用`n`表示
- 正多边形中心距正多边形的外接圆（或者正多边中心点距正多边形的顶点）就是正多边形外接圆半么，如上图中的`R`
- 正多边形中心点距和每条边的端点构成一个等腰三角形，如上图中的`A1`。这个三角形的两条边长度相等，刚好是正多边形外接圆半径`R`，而这个三角形的高，刚好是正多边形内切圆半径`r`

那么在Canvas中要使用`CanvasRenderingContext2D`对象自带的方法，比如`moveTo()`和`lineTo()`绘制多边形，我们就必须知道正多边形属性之间的关系。也就是这些属性之间的三角函数。言外之意，在Canvas中，我们使用**`moveTo()`和`lineTo()`方法，再配合一些简单的三角函数，就可以绘制出任意边数的多边形。**

既然绘制正多边形需要一定的三角函数知道，我们在绘制正多边形之前，先了简单的了解一下这方面的基础。

#### 外角（Exterior Angle）

正多边形的外角是正多边形任意边与相邻边延长直线构成的角：

![](/sites/default/files/blogs/2017/1703/canvas-11-5.png)

正多边形所有外角之和等于`360°`。也就是说，每个外角`α = 360° / n`。比如`n=8`，一个正八边形，它的外角`α = 360° / n = 360° / 8 = 45`。

#### 内角（Interior Angles）

正多边形相邻两条边构成的夹角就是正多边形的一个内角。每个内角都有其相邻的一个外角，它们构成一条直线，也就是说内角加上外角，刚好是`180°`。也就是内角``









<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/drawing-curve.html](//www.w3cplus.com/canvas/drawing-curve.html)