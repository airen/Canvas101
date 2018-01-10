# Canvas学习：绘制文本

前面的课程我们学习的都是如何在Canvas中绘制图形，但很多时候，除了绘制图形之外还有很多情景要同文本打交道。不过在Canvas中到目前为止只提供了一些必备的基本功能，例如文本的描边与填充，向Canvas之中放置文本，以及用像素为单位来计算任意字符串的宽度等。接下来的内容，我们就来了解在Canvas中怎么绘制文本以及一些基本的操作。

Canvas的绘图环境对象提供了两种方法来渲染文本：

- `fillText(text, x, y, [maxWidth])`：绘制填充文本
- `strokeText(text, x, y, [maxWidth])`：绘制描边文本

在Canvas中同样提供了类似CSS的一些`font`属性，用来修饰在Canvas中绘制的文本，比如:

- `font = value`：用来修饰绘制文本的样式，类似于CSS中的`font`
- `textAlign = value`：文本对齐设置
- `textBaseline = value`： 文本基线对齐设置
- `direction = value`： 文本方向的设置

除此之外，很多时候我们还需要对已绘制文本宽度的度量，在Canvas中提供了一个`measureText(text)`方法，该方法所返回的对象中，包含一个名为`width`的属性，它的值代表你传递给该方法的文本所占据的宽度。

在接下来的内容，我们将详细的针对上述内容做相关的阐述。

## 文本的填充和描边

在Canvas中的`CanvasRenderingContext2D`对象提供了两个方法来绘制文本：`fillText()`和`strokeText()`方法。先来看`fillText()`方法。

`fillText()`方法用来绘制填充文本，其语法如下：

    ctx.fillText(text, x, y, [maxWidth])

`fillText()`方法接受四个参数：

- `text`：需要绘制的文本内容
- `x`：指定绘制文本在Canvas画布中起始位置的`x`轴坐标点
- `y`：指定绘制文本在Canvas画布中起始位置的`y`轴坐标点
- `maxWidth`：指定绘制文本的最大宽度

来看个简单的示例：

    var text = 'Hello! W3cplus.com !'; // 需要绘制的文本内容
    ctx.fillStyle = '#f90'; // 文本颜色
    ctx.textAlign = 'center'; // 文本对齐方式  
    ctx.font = '48px Airal'; // 文本字号、字体
    
    ctx.fillText(text, w / 2, h / 2);

效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-14-1.png)](//codepen.io/airen/full/mmPJqB/)

*可以[点击这里](//codepen.io/airen/full/mmPJqB)在CodePen上查看Demo效果*。

在绘制文本时，同样可以使用`fillStyle`属性来设置填充文本的颜色。另外，上面的示例，我们只用了其中三个参数，并没有使用`maxWidth`参数。在`fillText()`方法中，这个参数是可选参数。这个参数的主要功能是用来控制绘制文本的最大宽度。如果绘制的文本内容超过了`maxWidth`的宽度的时候，将会压缩文本，让绘制的文本宽度和`maxWidth`值一样。

来个小示例：

    var text = 'Hello! W3cplus.com !';
    ctx.fillStyle = '#f90';
    ctx.textAlign = 'center';
    
    ctx.font = '48px Airal';
    
    ctx.fillText(text, w / 2, h / 2 - 50, 200);
    ctx.fillText(text, w / 2, h / 2 + 50);

效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-14-2.png)](//codepen.io/airen/full/JNXdVW/)

*可以[点击这里](//codepen.io/airen/full/JNXdVW)在CodePen上查看Demo效果*。

上示中，绘制的第一个文本，设置了`maxWidth`，而第二个文本并未设置`maxWidth`。通过`ctx.measureText(text).width)`可以得出未设置`maxWidth`的值是`474`，也就是说文本`Hello! W3cplus.com!`从`474`压缩到了`200`。

上面介绍的是`fillText()`绘制填充文本，在Canvas中还可以通过`strokeText()`方法来绘制描边文本。该方法具有的参数和使用方法与`fillText()`一样，只是最终在Canvas中渲染的效果不一样。比如将上示中的`fillText()`换成`strokeText()`，同时将`fillStyle`换成`strokeStyle`：

    var text = 'Hello! W3cplus.com !';
    ctx.strokeStyle = '#f90';
    ctx.textAlign = 'center';
    
    ctx.font = '48px Airal';
    
    ctx.strokeText(text, w / 2, h / 2 - 50, 200);
    ctx.strokeText(text, w / 2, h / 2 + 50);

最终效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-14-3.png)](//codepen.io/airen/full/EmKVbv)

*可以[点击这里](//codepen.io/airen/full/EmKVbv)在CodePen上查看Demo效果*。

在实际中，我们可以同时使用`fillText()`和`strokeText()`方法绘制一个具有填充的边框的文本，这也是我们最常见的描边文本，但它们的起始位置应该在同一个点：

    var text = 'Hello! W3cplus.com !';
    ctx.strokeStyle = 'blue';
    ctx.fillStyle = 'red';
    ctx.lineWidth = 2;
    ctx.textAlign = 'center';    
    ctx.font = '48px Airal';
    
    ctx.fillText(text, w / 2, h / 2);
    ctx.strokeText(text, w / 2, h / 2);

最终效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-14-4.png)](//codepen.io/airen/full/MmyaBJ/)

*可以[点击这里](//codepen.io/airen/full/MmyaBJ/)在CodePen上查看Demo效果*。

为了便于更灵活的使用`fillText()`和`strokeText()`绘制文本，可以将其封装成一个函数，比如`drawText()`：

    // @param {Object} ctx - CanvasRenderingContext2D
    // @param {String} text - 绘制文本的内容
    // @param {Number} x - 绘制文本起始点x轴坐标
    // @param {Number} y - 绘制文本起始点y轴坐标
    // @param {Boolean} isFill - 是否填充，true绘制填充文本，false绘制描边文本
    // @param {Boolean} isMaxWidth - 是否设置文本最大宽度, true设置最大宽度， false不设置最大宽度
    // @param {String} color - 绘制文本颜色
    // @param {Number} maxWidth - 文本最大宽度
    function drawText(ctx, text, x, y, isFill, isMaxWidth, color, maxWidth) {
        if (isFill) {
        ctx.fillStyle = color;
            if (isMaxWidth) {
            ctx.fillText(text, x, y, maxWidth);
            } else {
            ctx.fillText(text, x, y);
            }
        } else {
        ctx.strokeStyle = color;
        if (isMaxWidth) {
            ctx.strokeText(text, x, y, maxWidth);
        } else {
            ctx.strokeText(text, x, y);
        }
        }
    }

使用的时候，像下面这样调用即可：

    drawText(ctx, 'W3cplus.com', w / 2, 40, true, false, 'blue');
    drawText(ctx, 'W3cplus.com', w / 2, 80, true, true, 'blue', 100);

<div  style="margin-bottom: 20px;"><iframe id="RVrPdB" src="//codepen.io/airen/embed/RVrPdB?height=400&amp;theme-id=0&amp;slug-hash=RVrPdB&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 设置文本样式

在上面的示例中，我们看到了代码中有`ctx.font`属性的使用。其实在Canvas中，可以通过绘图环境对象的`font`属性，来设置绘制在Canvas之中的文本所采用的字型。该属性是一个CSS3格式的字型字符串，它和[CSS `font`](//developer.mozilla.org/zh-CN/docs/Web/CSS/font)属性相同的语法，默认的字型是`10px sans-serif`。

Canvas中`font`属性的各个分量如下表所示：

| 字型属性分量 | 描述 |
| ---------- | --- |
| `font-style` | 可以取如下三个值：`normal`、`italic`和`oblique` |
| `font-variant` | 可以取这两个值：`normal`和`small-caps` |
| `font-weight` | 决定该字型的字符笔画粗细，可取如下值：`normal`、`lighter`、`bold`和`bolder`，也可以采用对应的数值，比如`normal`对应的是`400` |
| `font-size` | 字型的大小，可以使用关键词`xx-small`、`x-small`、`small`、`medium`、`large`、`x-large`、`xx-large`、`smaller`、`larger`；也可以使用具体的带有长度单位的数值，比如`20px` |
| `line-height` | 浏览器会将该属性强制设置为其默认值`normal`，如果你设置了该值，浏览器会忽略你所设定的值 |
| `font-family` | 设置字体 |

> **通过CSS3与Canvas来指定字型属性时的区别：**绘制环境对象的`font`属性也支持CSS3格式的字型语法，除了样式语法所特有的属性，例如`inherit`或`initial`等。如果你不巧刚好用到了`inherit`或`initial`的话，那么浏览器在执行到那行代码时会悄然地失败，并不抛出任何异常，同时也不会将该值设定给`font`属性。通过Canvas来设置字型属性与通过CSS3来设置相比，还有一个区别：在Canvas中设置`line-height`属性时，浏览器将忽略其值，因为规范要求浏览器必须将该值设置为`normal`。

## 文本定位（对齐方式）

这里所说的文本定位，其实指的是对齐方式，在CSS中我们可以使用`text-align`设置文本水平对齐方式，`vertical-align`设置文本垂直方向的对齐方式。在Canvas中也有对应的属性。

通过前面的知识我们得知，在Canvas中使用`fillText()`或`fillText()`绘制文本时，需要指定所绘文本的`x`与`y`坐标，然而，浏览器具体会将文本绘制在何处（文本定位），则要看`textAlign`与`textBaseline`这两个绘图环境对象的属性。

### textAlign

**`CanvasRenderingContext2D.textAlign`**是Canvas中绘制文本时文本的对齐方式的属性。其对齐是基于`fillText()`或`strokeText()`方法的`x`值。`textAlign`属性有点类似于CSS中的`text-align`，用来设置文本水平对齐方式。其主要包括：

    ctx.textAlign = "left" || "right" || "center" || "start" || "end";

- `left`：文本左对齐
- `right`：文本右对齐
- `center`：文本居中对齐
- `start`：文本对齐界线开始的地方（左对齐指本地从左向右，右对齐指本地从右向左）
- `end`：文本对齐界线结束的地方（左对齐指本地从左向右，右对齐指本地从右向左）

默认值是`start`。在Canvas中使用`textAlign`时同样会受`direction`属性值的影响。当Canvas的`direction`的值为`ltr`时，也就是说浏览器是按照由左至右的方向来显示文本时，`textAlign`的`left`的效果与`start`相同，而`right`的效果则与`end`相同。同理，如果`direction`的值为`rtl`时，也就是说浏览器是从右至左来显示文本的，那么`textAlign`的`right`的效果则与`start`一致，而`left`则与`end`一致。下例演示了`textAlign`每个值在浏览器中渲染的效果（其中`direction`的值为默认值`ltr`）：

    var text = ['left', 'right', 'center', 'start', 'end'];
    ctx.fillStyle = '#f36';
    
    ctx.font = '32px Airal';
    
    for (var i = 0; i < text.length; i++) {
      ctx.textAlign = text[i];
      ctx.fillText('textAlign:' + text[i], w / 2, (i + 1) * 50);
    }

效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-14-5.png)](//codepen.io/airen/full/KmVdJm/)

*可以[点击这里](//codepen.io/airen/full/KmVdJm/)在CodePen上查看Demo效果*。

示例中绘制的文本，其起始点是画布的中心位置`w / 2`，也就是上图中的黑色竖线。`left`则让文本左侧在`x`点处（在竖线右侧），`right`则文本右侧在`x`点处（在竖线左侧），`center`则文本中间点在`x`点处（在竖线中间）。`start`和`left`等同，`end`和`right`等同。

> 这里的`textAlign='center'`比较特殊。`textAlign`的值为`center`时候文本的居中是基于你在`fillText`的时候所给的`x`的值，也就是说文本一半在`x`的左边，一半在`x`的右边（上图展示看得更清楚些）。所以，如果你想让文本在整个Canvas居中，就需要将`fillText`的`x`值设置成`canvas`的宽度的一半。

### textBaseline

`CanvasRenderingContext2D.textBaseline`是Canvas中描述绘制文本时，当前文本基线的属性，类似CSS中的`vertical-align`属性，在介绍`textBaseline`属性之前，先来下图，下图展示了`textBaseline`属性支持的[不同的基线情况](//www.whatwg.org/)：

![](/sites/default/files/blogs/2017/1704/baselines.png)

上图是不是非常熟悉呀，那我们回到Canvas的`textBaseline`属性的使用：

    ctx.textBaseline = "top" || "hanging" || "middle" || "alphabetic" || "ideographic" || "bottom";

- `top`：文本基线在文本块的顶部
- `hanging`：文本基线是悬挂基线
- `middle`：文本基线在文本块的中间
- `alphabetic`：文本基线是标准的字母基线
- `ideographic`：文字基线是表意字基线，如果字符本身超出了`alphabetic`基线，那么`ideograhpic`基线位置在字符本身的底部
- `bottom`：文本基线在文本块的底部，与`ideographic`基线的区别在于`ideographic`基线不需要考虑下行字母

`textBaseline`的默认值是`alphabetic`，该值用于绘制由基于拉丁字母的语言所组成的字符串，`ideographic`值则用于绘制日文或中文字符串，`hanging`值用于绘制各种印度语字符串，`top`、`bottom`和`middle`这三个值与特定的语言不相关，它们代表文本周围的边界框之内的某个位置，这个边界框也叫做“字符方框”。

同样来看一个示例，用效果来帮助我们理解`textBaseline`各个值的效果。

    var text = ['top', 'hanging', 'middle', 'alphabetic', 'ideographic', 'bottom'];
    ctx.fillStyle = '#f36';
    
    ctx.font = '24px Airal';
    
    for (var i = 0; i < text.length; i++) {
      ctx.textBaseline = text[i];
      ctx.fillText(text[i], (i + 1) * 150, h / 2);
    }

效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-14-6.png)](//codepen.io/airen/full/dWGGyB/)

*可以[点击这里](//codepen.io/airen/full/dWGGyB)在CodePen上查看Demo效果*。

## 文本度量

只要你做的事情与文本有关，你就得设法获取某个字符串的像素宽度和高度。在Canvas中提供了`measureText()`方法，这个方法返回一个`TextMetrics`对象，这个对象中包含了一个名为`width`的属性，这个属性就是字符串的宽度。

    var text = ctx.measureText("foo"); // TextMetrics object
    text.width; // 16;

在使用`measureText()`方法时，常见的错误就是在调用完该方法之后，才去设置字型。请注意：**`measureText()`方法是根据当前的字型来计算字符串宽度的，因此，如果你在调用`measureText()`方法之后才去改变字型，那么该方法所返回的宽度并不能反映出以那种字型来度量的实际文本宽度。**

上述的内容就是有关于Canvas中绘制文本所涉及到的一些方法和属性。接下来我们来看两个简单的示例。

## 示例

前面的示例，我们看到的是通过`fillText()`或`strokeText()`方法绘制填充文本或描边文本，可以说是最简单的绘制文本效果，但实际当中，我们要的效果不仅仅是这些普通的效果。比如我们需要一个圆形的文本效果，3D的文本效果等。那咱们就借助以前所学的一些基础知识来实现这两种效果。

### 环形文本

直接上代码，这里封装了一个简单的函数，比如`drawCircleText()`，并且根据绘制弧形文本所需要的参数传给这个函数，具体代码如下：

    // @param {Object} ctx - CanvasRenderingContext2D
    // @param {String} text - 需要绘制制的文本
    // @param {Number} x - 绘制文本起始点x轴坐标点
    // @param {Number} y - 绘制文本起始点y轴坐标点
    // @param {Number} radius - 圆形半径, 弧度值
    // @param {Number} startAngle - 开始的角度值
    // @param {Boolean} isFill - 是否填充，true填充，false描边
    // @param {String} color - 文本颜色
    function drawCircleText(ctx, text, x, y, radius, startAngle, isFill, color) {
        // 将startAngle角度转换成弧度
        var startRad = Math.PI * startAngle / 180;
        // 所绘文本的长度
        var len = text.length;
        // 每个字符所对应的弧度
        var numRadsPerLetter = Math.PI * 2 / len;
        
        ctx.save();
        // 将坐标原点移到(x, y)
        ctx.translate(x, y);
        // 将坐标旋转startRad
        ctx.rotate(startRad);
        
        // 对字符做操作
        for (var i = 0; i < len; i++) {
        ctx.save();
        // 每个字符旋转
        ctx.rotate(i * numRadsPerLetter);
        
        // isFill为true填充, false为描边
        if (isFill) {
            ctx.fillStyle = color;
            ctx.fillText(text[i], 0, -radius);
        } else {
            ctx.strokeStyle = color;
            ctx.strokeText(text[i], 0, -radius);
        }
        ctx.restore();
        }
        ctx.restore();
    }

在使用的时候，只需要调用这个函数，并传入相应的值：

    ctx.font = 'bold 40px Arial';
    drawCircleText(ctx, 'W3CPLUC.COM!', w / 2, h / 2, 100, 0, true, 'orange');

效果如下：

<div  style="margin-bottom: 20px;"><iframe id="jmWWdp" src="//codepen.io/airen/embed/jmWWdp?height=400&amp;theme-id=0&amp;slug-hash=jmWWdp&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

### 3D文本效果

3D文本效果在CSS中通过`text-shadow`来实现，具体怎么实现这里就不多说了，必竟我们这里是聊Canvas。其实在Canvas中实现的原理也有点类似，将会使用到`shadowBluer`、`shadowColor`等阴影属性（这个我们前面没有接触过，但后面我们也会深入学习），和前面的圆形文本一样，同样将绘制3D文本的效果封装成一个简单的函数，比如`draw3DText()`，具体的代码如下：

    // @param {Object} ctx - CanvasRenderingContext2D
    // @param {String} text - 绘制的文本内容
    // @param {Number} x - 文本起始点x轴坐标
    // @param {Number} y - 文本起始点y轴坐标
    // @param {Number} textDepth - 阴影的深度
    // @param {Boolean} isFill - 文本是否填充，true为填充，false为描边
    // @param {String} color - 文本颜色
    // @param {String} shadowColor - 阴影颜色
    // @param {Number} shadowBlur - 阴影距离
    // @param {Number} interval - 间距
    function draw3DText(ctx, text, x, y, textDepth,isFill, color, shadowColor, shadowBlur, interval) {
        var i;
        for (i = 0; i < textDepth; i++) {
        if(isFill) {
            ctx.fillText(text, x - i, y - i);
        } else {
            ctx.strokeText(text, x - i, y - i);
        }
        }
        ctx.shadowColor = shadowColor;
        ctx.shadowBlur = shadowBlur;
        ctx.shadowOffsetX = textDepth + interval;
        ctx.shadowOffsetY = textDepth + interval;
        
        if (isFill) {
        ctx.fillStyle = color;
        ctx.fillText(text, x - i, y - i);
        } else {
        ctx.strokeStyle = color;
        ctx.strokeText(text, x - i, y - i);
        }
        
    }

调用也非常的简单：

    ctx.font = '60px Verdana';
    ctx.textAlign = 'center';
    ctx.textBaseline= 'middle';
        
    draw3DText(ctx, 'W3cplus', w / 2, h / 2, 6, true, '#0094ed', '#000', 12, 2);

得到的效果如下：

<div  style="margin-bottom: 20px;"><iframe id="mmVVVe" src="//codepen.io/airen/embed/mmVVVe?height=400&amp;theme-id=0&amp;slug-hash=mmVVVe&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

这个示例通过将`fillText()`或者`strokeText()`的`(x, y)`设置为`canvas`宽度和高度的一半`(w / 2, h / 2)`再配合`ctx.textAlign = 'center'`和`ctx.textBaseline= 'middle'`可以实现所绘文本在画布的中间，也就是实现了我们常说的水平垂直居中的效果。

另外，把上面两外效果结合起来，就可以实现圆形的3D文本效果，要是再添加一点动画功能，效果就不一样了：

<div  style="margin-bottom: 20px;"><iframe id="VbebgQ" src="//codepen.io/airen/embed/VbebgQ?height=400&amp;theme-id=0&amp;slug-hash=VbebgQ&amp;default-tab=result&amp;user=airen" scrolling="no" frameborder="0" height="400" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe></div>

## 总结

这篇文章我们主要介绍了在Canvas中绘制文本的一些基础知识，在Canvas中可以通过`ctx.fillText()`绘制填充文本，`ctx.strokeText()`可以绘制描边文本，另外通过`ctx.textAlign`和`ctx.textBaseline`设置所绘制文本的位置，并且使用`ctx.measureText('text').width`可以得到所绘制文本`text`的宽度值，虽然这个值并不精确，但在Canvas中有方法可以解决，至于怎么解决我们后续的内容将会介绍。

虽然这些功能是绘制文本的基本功能，但结合Canvas其他的功能，我们可以绘制出很多不同的文本效果，至于绘制出什么样的效果，则需要大家去思考，因为创意是自己的，有了创意，然后结合自己所掌握的Canvas知识，能做的事情就更多了。如果你有更好的创意，希望在下面的评论中与我们一起分享。

<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/drawing-text.html](//www.w3cplus.com/canvas/drawing-text.html)