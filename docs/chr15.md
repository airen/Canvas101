# Canvas学习：图像合成

合成是指如何精细控制画布上对象的透明度和分层效果。在默认情况之下，如果在Canvas之中将某个物体（源）绘制在另一个物体（目标）之上，那么浏览器就会简单地把源特体的图像叠放在目标物体图像上面。

![](/sites/default/files/blogs/2017/1705/blending-examples.png)

事实上，它和[CSS中的混合模式](//www.w3cplus.com/blog/tags/408.html)非常的类似。简单点讲，在Canvas中，把图像源和目标图像，通过Canvas中的`globalCompositeOperation`操作，可以得到不同的效果，比如下图：

![](/sites/default/files/blogs/2017/1705/09Qgz.png)

正如上图，红苹果和黑色的圆，通过`globalCompositeOperation`的`destination-out`就变成了被咬了一口的红苹果。也就是说，在Canvas中通过图像的合成，我们可以实现一些与众不同的效果，比如我们要制作一个刮刮卡抽奖的效果。 今天我们就来了解Canvas中的图像合成怎么使用。

## 控制图像合成操作

在Canvas中有两个属性`globalAlpha`和`globalCompositeOperation`来控制图像合成操作：

- `globalAlpha`：设置图像的透明度。`globalAlpha`属性默认值为`1`，表示完全不透明，并且可以设置从`0`（完全透明）到`1`（完全不透明）。这个值必须设置在图形绘制之前
- `globalCompositeOperation`：该属性的值在`globalAlpha`以及所有变换都生效后控制在当前Canvas位图中绘制图形

## 图像合成类型

在Canvas中`globalCompositeOperation`属性的值总共有**26**种类型，每种类型都将前生不一样的效果，当然你可能看到效果都将不样，甚至有一些效果在浏览器中并不能正常的渲染。不过不要紧，我们简单的了解这26种类型其代表的含意以及产生的效果。

    ctx.save();
    ctx.translate(w / 2, h / 2);
    ctx.fillStyle = 'red';
    ctx.beginPath();
    ctx.arc(-40, 20, 80, 0, Math.PI * 2, true);
    ctx.closePath();
    ctx.fill();

上面的代码将在Canvas画布上绘制一个半径为`80px`的红色圆形，在这里把它称为图像源。

    ctx.fillStyle = 'orange';
    ctx.beginPath();
    ctx.arc(40, 20, 80, 0, Math.PI * 2, true);
    ctx.closePath();
    ctx.fill();
    ctx.restore();

这段代码将在Canvas画布上绘制一个半径为`80px`的橙色圆形，在这里把它称为图像目标。在图像源和目标图像之间设置`globalCompositeOperation`的值，就可以完成图像的合成操作：

    ctx.save();
    ctx.translate(w / 2, h / 2);
    ctx.fillStyle = 'red';
    ctx.beginPath();
    ctx.arc(-40, 20, 80, 0, Math.PI * 2, true);
    ctx.closePath();
    ctx.fill();

    ctx.globalCompositeOperation = 'source-in';

    ctx.fillStyle = 'orange';
    ctx.beginPath();
    ctx.arc(40, 20, 80, 0, Math.PI * 2, true);
    ctx.closePath();
    ctx.fill();
    ctx.restore();

此时得到的效果如下：

![](/sites/default/files/blogs/2017/1705/composite-1.png)

### source-over

`source-over`是`globalCompositeOperation`属性的默认值。源图形覆盖目标图形，源图形不透明的地方显示源图形，其余显示目标图形

![](/sites/default/files/blogs/2017/1705/composite-2.png)

### source-in

`source-in`：目标图形和源图形重叠且都不透明的部分才被绘制

![](/sites/default/files/blogs/2017/1705/composite-3.png)

### source-out

`source-out`：目标图形和源图形不重叠的部分会被绘制

![](/sites/default/files/blogs/2017/1705/composite-4.png)

### source-atop

`source-atop`：目标图形和源图形内容重叠的部分的目标图形会被绘制

![](/sites/default/files/blogs/2017/1705/composite-5.png)

### destination-over

`destination-over`：目标图形和源图形内容后面的目标图形会被绘制

![](/sites/default/files/blogs/2017/1705/composite-6.png)

### destination-in

`destination-in`：目标图形和源图形重叠的部分会被保留（源图形），其余显示为透明

![](/sites/default/files/blogs/2017/1705/composite-7.png)

其它的就不一一展示了。具体对应的效果可以看下面的两个DEMO：

<div  style="margin-bottom: 20px;"><iframe id="GmvweK" src="//codepen.io/airen/embed/GmvweK?height=400&amp;theme-id=0&amp;slug-hash=GmvweK&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

<div  style="margin-bottom: 20px;"><iframe id="qmXdMX" src="//codepen.io/airen/embed/qmXdMX?height=400&amp;theme-id=0&amp;slug-hash=qmXdMX&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

具体每个值对应的描述，可以[点击这里查阅](//developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation)。

## 合成图像的示例

在平时的业务中，我们常常能看到刮刮卡这样的抽奖效果。如果我们使用Canvas来做，就会用到Canvas图像的合成。

    <div id="card">
        <canvas id="canvasOne" width="500" height="300"></canvas>
    </div>

我们把奖品（如果是一个图像）当作`div#card`的背景展示。然后在Canvas中先绘制一个灰色的矩形（源图像），再通过鼠标事件（或触摸事件）来动态绘制新图像（这个就类似笔刷），把`globalCompositeOperation`属性的值设置为`destination-out`（新绘制的图形和目标canvas中已经存在的图形内容不重叠的部分的会被保留）。当笔刷擦除大于一定的比例的时候，就删除`<canvas>`元素或者使用`clearRect()`清除Canvas画布。从而展示出`div`背景：

![](/sites/default/files/blogs/2017/1705/composite-8.gif)

效果代码可以[点击这里查阅](//codepen.io/airen/full/OmjaYV/)。

<div  style="margin-bottom: 20px;"><iframe id="OmjaYV" src="//codepen.io/airen/embed/OmjaYV?height=400&amp;theme-id=0&amp;slug-hash=OmjaYV&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

如果为了更方便的控制奖品展示或者类似，可以将上面的结构做一下调整。

    <div id="card">
        <canvas id="canvasOne" width="500" height="300"></canvas>
        <div>奖品放这里</div>
    </div>

通过CSS的样式控制，把`canvas`放置在奖品元素上层。具体Canvas的效果控制和上面的示例一致。

## 总结

这篇文章我们主要介绍了Canvas的图像合成，在Canvas中可以通过两个属性`globalAlpha`和`globalCompositeOperation`来控制图像合成操作，实现图像合成效果。其实Canvas中的图像合成效果和CSS中的混合模式效果是类似的，只不过Canvas中会对图形进行合并，而CSS的混合模式只会对图形效果产生变化。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/compositing.html](//www.w3cplus.com/canvas/compositing.html)