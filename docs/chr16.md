# Canvas学习：裁切clip()

上一节中学习了[Canvas的图像合成](//www.w3cplus.com/canvas/compositing.html)，通过图像合成的技术可以轻易的实现类似于[刮刮卡的效果](//codepen.io/airen/full/OmjaYV/)，其实除了实现刮刮卡的效果之外，还可以使用这种技术制作类似于探照灯的效果。

<div  style="margin-bottom: 20px;"><iframe id="qmVwML" src="//codepen.io/airen/embed/qmVwML?height=400&amp;theme-id=0&amp;slug-hash=qmVwML&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

前面也说过了，Canvas中的图像合成非常类似于CSS中的混合模式，也类似于一些设计软件中的图层合层。但就上面的探照灯的效果，其又有点像是CSS的遮罩。其实在Canvas中，除了使用图像合成之外能实现上面的探照灯的效果之外，还可以使用Canvas的裁切`clip()`方法。那么这一节，我们就来简单的了解Canvas的`clip()`的相关知识点。

## Canvas中的剪切

接下来我们要聊的不是图像的合成，而是Canvas中的另一个有用的功能：**剪切区域**。它是Canvas之中由路径所定义的一块区域，浏览器会将所有的绘图操作都限制在本区域内执行。在默认情况下，剪辑区域的大小与Canvas画布大小一致。除非你通过创建路径并调用Canvas绘图环境对象的`clip()`方法来显式的设定剪辑区域，否则默认的剪辑区域不会影响Canvas之中所绘制的内容。然而，一旦设置好剪辑区域，那么你在Canvas之中绘制的所有内容都将局限在该区域内。这也意味着**在剪辑区域以外进行绘制是没有任何效果的**。

简单点讲，Canvas的裁切路径和普通的Canvas图形差不多，不同的是它的作用是遮罩，用来隐藏没有遮罩的部分。如下图所示，红边五角星就是裁切路径，所有在路径以外的部分都不会在Canvas上绘制出来。

![](/sites/default/files/blogs/2017/1705/clipping_five_star.png)

从上图所示的效果来看，跟上一节介绍的`globalCompositeOperation`中`source-in`和`source-atop`差不多的效果。最重要的区别是裁切路径不会在Canvas上绘制东西，而且它永远不受新图形的影响。这些特性使得它在特定区域里绘制图形时相当好用。

## clip()使用

上面也说了，Canvas中的`clip()`方法是裁切区可用于限制图像描绘的区域，具体的用法：

- 使用Canvas的绘制函数比如，`rect()`、`arc()`之类的方法选择好绘图区域
- 使用`clip()`函数将该区域（由`rect()`、`arc()`方法指定的绘图区域）设定为裁选区

**设定裁选区之后，无论在Canvas上绘制什么，只有落在裁选区内的那部分才能得以显示，其余都会被遮蔽掉**。

先来看一个示例，绘制一个四个圆而且未使用裁选区：

    ctx.save();
    
    // 绘制第一个圆
    
    ctx.beginPath();
    ctx.fillStyle = 'tomato';
    ctx.arc(cx, cy, radius, 0, Math.PI * 2, false);
    ctx.fill();
    
    // 绘制第二个圆
    ctx.beginPath();
    ctx.fillStyle = '#333';
    ctx.arc(cx + 100, cy, radius, 0, Math.PI * 2, false);
    ctx.fill();
    
    // 绘制第三个圆
    ctx.beginPath();
    ctx.fillStyle = 'cornsilk';
    ctx.arc(cx, cy + 100, radius, 0, Math.PI * 2, false);
    ctx.fill();
    
    // 绘制第四个圆
    ctx.beginPath();
    ctx.strokeStyle = '#ccc';
    ctx.lineWidth = 10;
    ctx.arc(cx, cy, radius, 0, Math.PI * 2, false);
    ctx.stroke();
    
    ctx.restore();

看到的效果如下：

![](/sites/default/files/blogs/2017/1705/canvas-clip-1.png)

接下来我们分别来看看加上`clip()`的效果，先来看看在第三个圆后面添加：

    // 绘制第三个圆
    ctx.beginPath();
    ctx.fillStyle = 'cornsilk';
    ctx.arc(cx, cy + 100, radius, 0, Math.PI * 2, false);
    ctx.fill();
    ctx.clip();

![](/sites/default/files/blogs/2017/1705/canvas-clip-2.png)

从效果图上，可以看出第四个圆（灰色的，只有边框的圆）只有部分绘制出来。接着往下，把`clip()`往上移，放到第二个圆的后面：

    // 绘制第二个圆
    ctx.beginPath();
    ctx.fillStyle = '#333';
    ctx.arc(cx + 100, cy, radius, 0, Math.PI * 2, false);
    ctx.fill();
    ctx.clip();

原理同样的，第二个圆成为裁剪区域，第三个圆和第四个圆与第二个圆有交集的地方才会绘制出来，所以看到的效果如下：

![](/sites/default/files/blogs/2017/1705/canvas-clip-3.png)

继续按类似的方法操作，把`clip()`放置在第一个圆的后面：

    ctx.beginPath();
    ctx.fillStyle = 'tomato';
    ctx.arc(cx, cy, radius, 0, Math.PI * 2, false);
    ctx.fill();
    ctx.clip();

最终的效果，或许你已经可以猜得到了：

![](/sites/default/files/blogs/2017/1705/canvas-clip-4.png)

## 取消裁切区

当使用裁切区`clip()`进行绘图后，可能需要取消该裁选区或者重新定义裁切区。在Canvas中，可以通过[`save()`函数和`restore()`函数](//www.w3cplus.com/canvas/canvas-states.html)来实现——**在构建裁切区之前保存状态，完成裁切区内的绘图之后进行状态读取**。

同样拿上面的示例来举例，依旧在第三个圆后面做`clip()`，并且同时做`restore()`：

    // 绘制第三个圆
    ctx.beginPath();
    ctx.fillStyle = 'cornsilk';
    ctx.arc(cx, cy + 100, radius, 0, Math.PI * 2, false);
    ctx.fill();
    ctx.clip();
    ctx.restore();
    
    // 绘制第四个圆
    ctx.beginPath();
    ctx.strokeStyle = '#ccc';
    ctx.lineWidth = 10;
    ctx.arc(cx, cy, radius, 0, Math.PI * 2, false);
    ctx.stroke();

![](/sites/default/files/blogs/2017/1705/canvas-clip-5.png)

和前面的示例效果不一样，第四个灰色的边框圆，他并没有仅在第三个圆（裁切区）绘制，而是整个绘制出来了。接着往下看，把`clip()`往上提，提到第二个圆之后，而`restore()`位置不变：

![](/sites/default/files/blogs/2017/1705/canvas-clip-6.png)

如果把`clip()`移动第一个圆之后，然后`restore()`继续放置在第三个圆之后，看到的效果如下：

![](/sites/default/files/blogs/2017/1705/canvas-clip-4.png)

## 制作探照灯

通过前面的介绍，我们了解了：**Canvas中的`clip()`方法用于从原始画布中剪切任意形状和尺寸。一旦剪切了某个区域，则所有之后的绘图都会被限制在被剪切的区域内（不能访问画布上的其他区域）。也可以在使用`clip()`方法前通过使用`save()`方法对当前画布区域进行保存，并在以后的任意时间通过`restore()`方法对其进行恢复**。

根据这个原理，我们就可以很轻松的实现下个探照灯的效果。

- 使用`arc()`绘制一个圆形的区域，然后在其后调用`clip()`，设置剪切区域
- 使用`drawImage()`在画布中绘制一个图像
- 动态改变`arc()`的位置，从而看到一个类似探照灯的效果

具体的实现可以看下面的代码：

    var canvas = document.getElementById('canvasOne');
    var ctx = canvas.getContext('2d');

    var w = canvas.width = window.innerWidth;
    var h = canvas.height = window.innerHeight;

    var image = new Image();

    image.src = 'https://farm3.staticflickr.com/2908/32764885503_1a04915b11_k.jpg';

    image.onload = function() {
        ctx.drawImage(image, 0, 0);
    }

    // 设置探照灯对象模型
    // @param (x, y): 表示圆心坐标
    // @param radius: 圆心半径
    // @param vx, vy: 水平和垂直方向的速度，通过他们控制速度大小
    
    var spotlight = {
        x: w / 2,
        y: h / 2,
        radius: 100,
        vx: Math.random() * 5 + 10,
        vy: Math.random() * 5 + 15
    };

    // 通过setInterval来更新模型的位置
    // 每40ms更新一次
    setInterval(function() {
        draw();
        update(w, h);
    }, 40);

    function draw() {
        ctx.clearRect(0, 0, w, h);

        ctx.save();

        ctx.beginPath();
        ctx.fillStyle = '#000';
        ctx.fillRect(0, 0, w, h);
        ctx.fill();

        ctx.save();
        ctx.beginPath();
        ctx.arc(spotlight.x, spotlight.y, spotlight.radius, 0, Math.PI * 2, false);
        ctx.fill();

        // 将上面的区域作为剪辑区域
        ctx.clip();

        // 由于使用clip()，画布背景图片会出现在clip()区域内
        ctx.drawImage(image, 0, 0);

        ctx.restore();
    }

    // 小球运动模型
    function update(w, h) {
        spotlight.x += spotlight.vx;
        spotlight.y += spotlight.vy;

        // 如果小球超出了左边的边界，则速度反向，x 点变为圆的半径
        if (spotlight.x - spotlight.radius <= 0) {
            spotlight.vx = -spotlight.vx;
            spotlight.x = spotlight.radius;
        }

        // 如果小球超出了右边的边界，则速度反向，x点变为画布宽度-圆的半径
        if (spotlight.x + spotlight.radius >= w) {
            spotlight.vx = -spotlight.vx;
            spotlight.x = w - spotlight.radius;
        }

        // Y轴方向处理
        if (spotlight.y - spotlight.radius <= 0) {
            spotlight.vy = -spotlight.vy;
            spotlight.y = spotlight.radius;
        }

        if (spotlight.y + spotlight.radius >= h) {
            spotlight.vy = -spotlight.vy;
            spotlight.y = h - spotlight.radius;
        }
    }

最后效果如下：

<div  style="margin-bottom: 20px;"><iframe id="WjXqgq" src="//codepen.io/airen/embed/WjXqgq?height=400&amp;theme-id=0&amp;slug-hash=WjXqgq&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

这个探照灯的效果是使用`clip()`方法来实现的，如果你感兴趣的话，可以把这个效果的实现原理与[前面提供的示例](//codepen.io/airen/full/qmVwML/)（图像合成制作探照灯效果）对比，看看这两者制作方案有何不同之处。

## 总结

这篇文章主要介绍了Canvas中的`clip()`方法的特性。**`clip()`方法将剪切区域设置为当前剪切区域与当前路径的交集。在第一次调用`clip()`方法之前，剪切区域与整个Canvas画布大小一致。因为`clip()`方法会将剪切区域设置为当前剪切区域与当前路径的交集，所以对该方法的调用一般都是嵌入`save()`和`restore()`方法之间的。否则，剪切区域将会越变越小，这通常不是我们想要的效果**。合理的运用好`clip()`和`save()`以及`restore()`方法我们就可制作出不同的效果，比如文章中介绍的探照灯的效果。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/clip.html](//www.w3cplus.com/canvas/clip.html)