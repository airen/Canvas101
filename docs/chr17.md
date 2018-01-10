# Canvas学习：渐变

通过前面的Canvas学习，了解到在Canvas中绘制图形时，使用`strokeStyle`和`fillStyle`来设置绘制图形的边框颜色和填充颜色。前面的教程我们看到的都是给这两个属性设置具体的颜色值，比如`#fff`、`red`之类。其实在Canvas中，除了给`strokeStyle`和`fillStyle`设置颜色之外，还可以设置渐变色。今天我们要学习的就是Canvas中的渐变知识。

Canvas中的渐变和CSS的渐变也有点类似，分为线性渐变和径向渐变（也被称之为扩散性渐变）。`CanvasRenderingContext2D`对象中提供了两个方法：

- `CanvasRenderingContext2D.createLinearGradient()`：创建线性渐变
- `CanvasRenderingContext2D.createRadialGradient()`：径向渐变

那么在Canvas中如何使用渐变的方法，给绘制的图形或文本增强效果。

## 线性渐变

在Canvas中首先使用`createLinearGradient()`创建一个新的`CanvasGradient`对象，然后将它赋值给一个变量，这样就可以将变量运用给`strokeStyle`和`fillStyle`。`createLinearGradient()`使用语法如下：

    ctx.createLinearGradient(x1, y1, x2, y2)

也就是说`createLinearGradient()`方法接受四个参数，分别代表渐变的起始点`(x1, y1)`和渐变终点`(x2, y2)`。起点和终点描述了所绘制渐变效果的长度、位置和方向。

前面也说过了，Canvas中的渐变和CSS的渐变非常类似，上面的内容我们只是通过`createLinearGradient()`创建了`CanvasGradient`对象，但并没有渐变的颜色，要是没有颜色，对于这个对象而言就没有太多的意义了。所以，在Canvas中，可以通过`addColorStop`来定义渐变的颜色。

`addColorStop`是Canvas中制作渐变效果不可缺少的一个属性，其使用语法如下：

    gradient.addColorStop(position, color)

也就是说`addColorStop`接受两个参数：

- `position`：指定渐变中颜色所在的相对位置，其接受一个`0`至`1`之间的值
- `color`：指定渐变中的颜色

我们来看一个简单的例子：

    var gradient = ctx.createLinearGradient(0, 0, 0, h);
   
    gradient.addColorStop(0, 'rgb(255,0,0)'); //红 
    gradient.addColorStop(0.5, 'rgb(0,255,0)');//绿
    gradient.addColorStop(1, 'rgb(0,0,255)'); //蓝
    
    ctx.fillStyle = gradient;
    
    ctx.fillRect(0, 0, w, h);

效果如下：

[![](/sites/default/files/blogs/2017/1705/canvas-18-1.png)](//codepen.io/airen/full/aWGeKZ/)

上面的示例，我们创建了一个名为`gradient`的渐变，这个渐变的起点是`(0, 0)`，终点是`(0, h)`，并且通过`addColorStop`给渐变`gradient`设置了三个渐变色，第一个渐变颜色`rgb(255, 0, 0)`的相对位置是`0`，第二个渐变颜色`rgb(0, 255, 0)`的相对位置是`0.5`，第三个颜色`rgb(0, 0, 255)`的相对位置是`1`。

![](/sites/default/files/blogs/2017/1705/canvas-18-2.png)

在Canvas中线性渐变常见的有三种：**水平的线性渐变**、**垂直的线性渐变**和**角度的线性渐变**。但是不管哪种线性渐变，都是依靠起点`(x1, y1)`和终点`(x2,y2)`来决定：

- 当起点`x1`和终点`x2`不同时，将会构造出水平的线性渐变
- 当起点`y1`和终点`y2`不同时，将会构造出垂直的线性渐变
- 当`x1`和`x2`以入`y1`和`y2`都不同时，将会构造出角度的线性渐变，这一点和CSS的不一样，在Canvas中并没有类似`deg`这样的方法决定角度线性渐变

比如下面的效果:

![](/sites/default/files/blogs/2017/1705/canvas-18-3.gif)

正如上图所演示的一样，渐变不同的起始位置可以决定渐变方向以及效果。不过上面我们看到的效果，起始位置都是在所绘图形上。如果我们的渐变位置在所绘图形外和内，效果又将不一样，比如下图的演示效果：

![](/sites/default/files/blogs/2017/1705/canvas-18-4.gif)

当然，如果你在上面的示例基础上通过`addColorStop`还可以改变渐颜色效果。更好的体验可以在下面的示例中进行体验：

<div  style="margin-bottom: 20px;"><iframe id="vmdRrJ" src="//codepen.io/airen/embed/vmdRrJ?height=400&amp;theme-id=0&amp;slug-hash=vmdRrJ&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 径向渐变

径向渐变又称之为扩散性渐变。在Canvas中通过`createRadialGradient()`方法创建径向渐变。这个方法接受六个参数：前三个参数描述一个圆（开始圆），后三个参数描述另一个圆（结束圆）。这两个圆本身不仅描述了方向及渐变的起止位置，还描述了渐变的形状。用于描述每个圆有三个参数，`(x,y)`表示圆心位置，`r`表示圆的半径。

    ctx.createRadialGradient(x1, y1, r1, x2, y2, r2)

实际的渐变效果是连接两个圆周的锥体，其中开始圆之前的锥体部分显示偏移值为`0`的颜色，而结束圆之后的锥体部分则显示偏移值为`1`的颜色。理解这个概念可能有些难度，不过下图可能可以帮助大家更好的理解：

![](/sites/default/files/blogs/2017/1705/canvas-18-3.png)

简单的来看一个简单的示例：

    var gradient = ctx.createRadialGradient(300, 300, 10, 100, 100, 50);
   
    gradient.addColorStop(0, 'rgb(255,0,0)'); //红 
    gradient.addColorStop(0.5, 'rgb(0,255,0)');//绿
    gradient.addColorStop(1, 'rgb(0,0,255)'); //蓝
    
    ctx.fillStyle = gradient;
    
    ctx.fillRect(0, 0, w, h);

最终效果如下：

[![](/sites/default/files/blogs/2017/1705/canvas-18-5.png)](//codepen.io/airen/full/VbdYzp/)

其实径向渐变和线性渐变也是非常类似的，不同的位置，不同的圆半么，所得的效果都将不一样。对于径向渐变，渐变有下面几种情形：

- 两个圆大小不相等情况
- 两个圆大小相等情况

下面我们来实际看看这几个情形所产生的渐变效果：

[![](/sites/default/files/blogs/2017/1705/canvas-18-6.png)](//codepen.io/airen/full/oWygpB/)

上图的效果是圆心都在`(w / 2, h / 2)`，起始圆的半径`50`，终止圆的半径`150`。

[![](/sites/default/files/blogs/2017/1705/canvas-18-7.png)](//codepen.io/airen/full/pPKvam)

圆一`(50, 50, 100)`和圆二`(100, 100, 300)`不在同一位置，而且圆一在圆二内的渐变效果。

[![](/sites/default/files/blogs/2017/1705/canvas-18-8.png)](//codepen.io/airen/full/MmXYVN/)

和上面的效果刚好相反，圆一`(100, 100, 300)`和圆二`(50, 50, 100)`位置在不同一位置，而且圆一在圆外的渐变效果。

[![](/sites/default/files/blogs/2017/1705/canvas-18-9.png)](//codepen.io/airen/full/GmGgGx/)

圆一`(50, 50, 150)`和圆二`(250, 250, 50)`都在不同的位置，而且两者并没有任何包含关系。

上面看到的都是两圆大小不同的效果，接下来看看几个两圆大小相同的效果。

[![](/sites/default/files/blogs/2017/1705/canvas-18-10.png)](//codepen.io/airen/full/WjybgP/)

上图大家并没有看到任何的渐变效果，上图是当圆一和圆二的位置都相同。

[![](/sites/default/files/blogs/2017/1705/canvas-18-11.png)](//codepen.io/airen/full/VbdYVM/)

上图是圆一和圆二相离的效果。接下来再看一个圆一和圆二相交的效果(但不完全重合)：

[![](/sites/default/files/blogs/2017/1705/canvas-18-12.png)](//codepen.io/airen/full/rmKaPx/)

在径向渐变中和线性渐变类似，同样需要通过`addColorStop()`来添加渐变颜色。

## 总结

这篇文章主要介绍了Canvas中的渐变。在Canvas中也和CSS的渐变类似分为线性渐变和径向渐变。通过`crteateLinearGradient(x1, y1, x2, y2)`创建线性渐变，`createRadialGradient(x1, y1, r1, x2, y2, r2)`创建径向渐变。不管是线性渐变还是径向渐变都可以将其赋值给一个变量，然后通过`addColorStop()`添加渐变颜色。另外Canvas的渐变可以通过渐变的起点和终点位置来控制渐变的效果。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/gradient.html](//www.w3cplus.com/canvas/gradient.html)