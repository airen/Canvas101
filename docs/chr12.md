# Canvas学习：自定义的坐标变换

相对于Web坐标系统而言[Canvas里的坐标系统](//www.w3cplus.com/canvas/canvas-coordinate-system.html)较为复杂一些，除了默认的坐标系统之外还有[坐标变换](://www.w3cplus.com/canvas/transformation-coordinates.html)概念。在上一节中，已经了解了如何使用`scale()`、`rotate()`和`translate()`方法来变换坐标系。这三个方法提供了一种简便的手段，用于操作绘图环境对象的变换矩阵（Transformation Matrix）。默认情况下，这个变换矩阵就是**[单位矩阵](//zh.wikipedia.org/wiki/%E5%96%AE%E4%BD%8D%E7%9F%A9%E9%99%A3)**（Identity Matrix），它并不会影响所要绘制的物体。当调用了`scale()`、`rotate()`或`translate()`方法之后，变换矩阵就会被修改，从而也会影响到所有后续的绘图操作。

在大多数情况之下，这三个方法就足够用了，不过，有些时候可能需要自己直接操作变换矩阵。比方说，如果要对所绘对象进行“[错切](//zh.wikipedia.org/zh-cn/%E9%94%99%E5%88%87)”（Shear），那么就没有办法通过组合运用这三个方法来达成此效果。在这种情况下，就必须直接操作变换矩阵了。这一节，我们就一起来了解Canvas中的矩阵变换。

## 矩阵变换

Canvas的矩阵变换又称为**自定义的坐标变换**。Canvas的绘图环境对象（`CanvasRenderingContext2D`）提供了两个可以直接操作变换矩阵的方法：

- **CanvasRenderingContext2D.transform**：在当前的变换矩阵之上叠加运用另外的变换效果
- **CanvasRenderingContext2D.setTransform**：将当前的变换矩阵设置为默认的单位矩阵，然后在单位矩阵之上运用用户指定的变换效果

> **要点**：多次调用`transform()`方法所造成的变换效果是累积的，而每次只要调用`setTransform()`方法，它就会将上一次的变换矩阵彻底清除。

在上一节中，我们了解到`translate()`、`rotate()`和`scale()`这三个方法都是通过操作变换矩阵来实现其功能的，也就是说，也可以直接使用`transform()`和`setTransform()`方法来操作变换矩阵，实现坐标系统的平移、旋转和缩放等效果。直接使用`transform`和`setTransform()`方法操作变换矩阵有自己的优势，也有自己的劣势。

使用`transform()`和`setTransform()`有两个好处：

- 可以实现`scale()`、`rotate()`和`translate()`方法所达到的效果，比如错切效果
- 只需调用一次`transform()`或`setTransform()`方法，就可以做出结合了缩放、旋转、平移及错切等诸多操作的效果

使用`transform()`和`setTransform()`方法的主要缺点则是，这两个方法不像`scale()`、`rotate()`和`translate()`方法那样直观。

上面的内容简单的提到`transform()`和`setTransform()`方法是操作变换矩阵，那么要彻底的理解这两个方法，就得对矩阵有所了解。为了帮助大家更好的理解这两个方法，先来了解一下矩阵相关的知识。如果你对矩阵比较了解，可以忽略这些内容，直接跳到后面你想阅读的部分。

## 矩阵基础知识

[矩阵](//zh.wikipedia.org/wiki/%E7%9F%A9%E9%98%B5)是一种非常有用的数学工具，尽管听起来可能有些吓人，不过一旦你理解了它们后，它们会变得非常有用。在讨论矩阵的过程中，我们需要使用到一些数学知识。对于一些愿意多了解这些知识的同学，我会附加一些资源给你们阅读。

在深入了解矩阵之前我们有必要先了解一些相关的概念。这一节的目标就是让大家拥有将来需要的最基础的数学背景知识。如果你发现这节十分困难，尽量尝试去理解它们，当你以后需要它们的时候回过头来复习这些概念。

### 向量

向量最基本的定义就是一个方向。或者更正式的说，向量有一个方向（Direction）和大小（Magnitude，也称之为长度）。可以把向量想像成一个藏宝图上的指示：“向左走十步，向北走三步，然后向右走五步”；“左”就是方向，“十步”就是向量的长度。那么这个藏宝图的指示一共有三个向量。向量可以在任意维度（Dimension）上，但是我们通常只使用`2~4`维。如果一个向量有两个维度，它表示一个平面的方向（想像一下2D的图像），当它有三个维度的时候，它可以表达一个3D世界的方向。

下图展示了三个向量，每个向量在2D图像中都用一个箭头`(x,y)`表示。我们在2D图片中展示这些向量，因为这样子会更直观一点。由于向量表示的是方向，起始于何处并不会改变它的值。

![](/sites/default/files/blogs/2017/1704/vectors.png)

数学家喜欢在字母上面加一横表示向量，比如说在`v`的上面加`-`。当用在公式中时它们通常是这样的：

![](/sites/default/files/blogs/2017/1704/canvas-13-1.png)

**注：**把2D向量当做`z`坐标轴为`0`的3D向量。

由于向量是一个方向，所以有些时候会很难形象地将它们用位置表示出来。为了让其更为直观，通常设定这个方向的原点为`(0,0,0)`（在2D世界中，这个原点就是`(0,0)`），然后指向一个方向，对应一个点，使其变为**位置向量（Position Vector）**。比如上图中位置向量`(3,2)`在图像中的起点会是`(0,0)`，并会指向`(3,2)`。

### 向量与标量运算

**标量(Scalar)**只是一个数字（或者说是仅有一个分量的向量）。当把一个向量加、减、乘或除一个标量，我们可以简单的把向量的每个分量分别进行该运算。对于加法来说会像这样：

![](/sites/default/files/blogs/2017/1704/canvas-13-2.png)

其中的`+`可以是`+`，`-`，`·`或`÷`，其中`·`是乘号。**注意，`-`和`÷`运算时不能颠倒（标量 -/÷向量），它为颠倒的运算是没有定义的**。

### 向量取反

对一个向量取反（Negate）会将其方向逆转。一个向东北的向量取反后就指向西南方向了。我们在一个向量的每个分量前加负号就可以实现取反了（或者说用`-1`数乘该向量）：

![](/sites/default/files/blogs/2017/1704/canvas-13-3.png)

### 向量加减

向量的加法可以被定义为是分量的(Component-wise)相加，即将一个向量中的每一个分量加上另一个向量的对应分量：

![](/sites/default/files/blogs/2017/1704/canvas-13-4.png)

向量`v = (4, 2)`和`k = (1, 2)`可以直观地表示为：

![](/sites/default/files/blogs/2017/1704/vectors_addition.png)

就像普通数字的加减一样，向量的减法等于加上第二个向量的相反向量：

![](/sites/default/files/blogs/2017/1704/canvas-13-5.png)

两个向量的相减会得到这两个向量指向位置的差。这在我们想要获取两点的差会非常有用。

![](/sites/default/files/blogs/2017/1704/vectors_subtraction.png)

### 长度

我们使用[勾股定理](//zh.wikipedia.org/wiki/%E5%8B%BE%E8%82%A1%E5%AE%9A%E7%90%86)(Pythagoras Theorem)来获取向量的长度(Length)/大小(Magnitude)。如果你把向量的`x`与`y`分量画出来，该向量会和`x`与`y`分量为边形成一个[三角形](//zh.wikipedia.org/wiki/%E4%B8%89%E8%A7%92%E5%BD%A2):

![](/sites/default/files/blogs/2017/1704/vectors_triangle.png)

因为两条边（`x`和`y`）是已知的，如果希望知道斜边`v¯`的长度，我们可以直接通过勾股定理来计算：

![](/sites/default/files/blogs/2017/1704/canvas-13-6.png)

### 向量相乘

两个向量相乘是一种很奇怪的情况。普通的乘法在向量上是没有定义的，因为它在视觉上是没有意义的。但是在相乘的时候我们有两种特定情况可以选择：一个是点乘(Dot Product)，记作`v¯⋅k¯`，另一个是叉乘(Cross Product)，记作`v¯×k¯`。

#### 点乘

两个向量的点乘等于它们的数乘结果乘以两个向量之间夹角的余弦值。可能听起来有点费解，我们来看一下公式：

![](/sites/default/files/blogs/2017/1704/canvas-13-7.png)

它们之间的夹角记作`θ`。为什么这很有用？想象如果`v¯`和`k¯`都是单位向量，它们的长度会等于`1`。这样公式会有效简化成：

![](/sites/default/files/blogs/2017/1704/canvas-13-8.png)

现在点积只定义了两个向量的夹角。你也许记得`90`度的余弦值是`0`，`0`度的余弦值是`1`。使用点乘可以很容易测试两个向量是否正交(Orthogonal)或平行（正交意味着两个向量互为直角）。如果你想要了解更多关于正弦或余弦函数的知识，我推荐你看[可汗学院](//www.khanacademy.org/math/trigonometry/basic-trigonometry/basic_trig_ratios/v/basic-trigonometry)的基础三角学视频。

所以，我们该如何计算点乘呢？点乘是通过将对应分量逐个相乘，然后再把所得积相加来计算的。两个单位向量的（你可以验证它们的长度都为1）点乘会像是这样：

![](/sites/default/files/blogs/2017/1704/canvas-13-9.png)

要计算两个单位向量间的夹角，我们可以使用[反余弦函数](//zh.wikipedia.org/wiki/%E5%8F%8D%E9%A4%98%E5%BC%A6)`cos−1` ，可得结果是`143.1`度。现在我们很快就计算出了这两个向量的夹角。点乘会在计算光照的时候非常有用。

#### 叉乘

叉乘只在3D空间中有定义，它需要两个不平行向量作为输入，生成一个正交于两个输入向量的第三个向量。如果输入的两个向量也是正交的，那么叉乘之后将会产生3个互相正交的向量。接下来的教程中这会非常有用。下面的图片展示了3D空间中叉乘的样子：

![](/sites/default/files/blogs/2017/1704/vectors_crossproduct.png)

不同于其他运算，如果你没有钻研过线性代数，可能会觉得叉乘很反直觉，所以只记住公式就没问题啦（记不住也没问题）。下面你会看到两个正交向量`A`和`B`叉积：

![](/sites/default/files/blogs/2017/1704/canvas-13-10.png)

是不是看起来毫无头绪？不过只要你按照步骤来了，你就能得到一个正交于两个输入向量的第三个向量。

## 矩阵

现在我们已经讨论了向量的全部内容，是时候看看矩阵了！简单来说矩阵就是一个矩形的数字、符号或表达式数组。矩阵中每一项叫做矩阵的元素(Element)。下面是一个`2×3`矩阵的例子：

![](/sites/default/files/blogs/2017/1704/canvas-13-11.png)

矩阵可以通过`(i, j)`进行索引，`i`是行，`j`是列，这就是上面的矩阵叫做`2×3`矩阵的原因（`3`列`2`行，也叫做矩阵的维度(Dimension)）。这与你在索引2D图像时的`(x, y)`相反，获取`4`的索引是`(2, 1)`（第二行，第一列）。

矩阵基本也就是这些了，它就是一个矩形的数学表达式阵列。和向量一样，矩阵也有非常漂亮的数学属性。矩阵有几个运算，分别是：矩阵加法、减法和乘法。

#### 矩阵的加减

矩阵与标量之间的加减定义如下：

![](/sites/default/files/blogs/2017/1704/canvas-13-12.png)

标量值要加到矩阵的每一个元素上。矩阵与标量的减法也相似：

![](/sites/default/files/blogs/2017/1704/canvas-13-13.png)

矩阵与矩阵之间的加减就是两个矩阵对应元素的加减运算，所以总体的规则和与标量运算是差不多的，只不过在相同索引下的元素才能进行运算。这也就是说加法和减法只对同维度的矩阵才是有定义的。一个`3×2`矩阵和一个`2×3`矩阵（或一个`3×3`矩阵与`4×4`矩阵）是不能进行加减的。我们看看两个`2×2`矩阵是怎样相加的：

![](/sites/default/files/blogs/2017/1704/canvas-13-14.png)

#### 矩阵的数乘

和矩阵与标量的加减一样，矩阵与标量之间的乘法也是矩阵的每一个元素分别乘以该标量。下面的例子展示了乘法的过程：

![](/sites/default/files/blogs/2017/1704/canvas-13-15.png)

现在我们也就能明白为什么这些单独的数字要叫做标量(Scalar)了。简单来说，标量就是用它的值缩放(Scale)矩阵的所有元素。前面那个例子中，所有的元素都被放大了`2`倍。

到目前为止都还好，我们的例子都不复杂。不过矩阵与矩阵的乘法就不一样了。

#### 矩阵相乘

矩阵之间的乘法不见得有多复杂，但的确很难让人适应。矩阵乘法基本上意味着遵照规定好的法则进行相乘。当然，相乘还有一些限制：

- 只有当左侧矩阵的列数与右侧矩阵的行数相等，两个矩阵才能相乘。
- 矩阵相乘不遵守交换律(Commutative)，也就是说`A⋅B≠B⋅A`。

我们先看一个两个`2×2`矩阵相乘的例子：

![](/sites/default/files/blogs/2017/1704/canvas-13-16.png)

现在你可能会在想了：天哪，刚刚到底发生了什么? 矩阵的乘法是一系列乘法和加法组合的结果，它使用到了左侧矩阵的行和右侧矩阵的列。我们可以看下面的图片：

![](/sites/default/files/blogs/2017/1704/matrix_multiplication.png)

我们首先把左侧矩阵的行和右侧矩阵的列拿出来。这些挑出来行和列将决定我们该计算结果`2x2`矩阵的哪个输出值。如果取的是左矩阵的第一行，输出值就会出现在结果矩阵的第一行。接下来再取一列，如果我们取的是右矩阵的第一列，最终值则会出现在结果矩阵的第一列。这正是红框里的情况。如果想计算结果矩阵右下角的值，我们要用第一个矩阵的第二行和第二个矩阵的第二列。

计算一项的结果值的方式是先计算左侧矩阵对应行和右侧矩阵对应列的第一个元素之积，然后是第二个，第三个，第四个等等，然后把所有的乘积相加，这就是结果了。现在我们就能解释为什么左侧矩阵的列数必须和右侧矩阵的行数相等了，如果不相等这一步的运算就无法完成了！

结果矩阵的维度是`(n, m)`，`n`等于左侧矩阵的行数，`m`等于右侧矩阵的列数。

如果在脑子里想象出这一乘法有些困难，别担心。不断地动手计算，如果遇到困难再回头看这页的内容。随着时间流逝，矩阵乘法对你来说会变成很自然的事。

我们用一个更大的例子来结束对矩阵相乘的讨论。试着使用颜色来寻找规律。作为一个有用的练习，你可以试着自己解答一下这个乘法问题，再将你的结果和图中的这个进行对比（如果用笔计算，你很快就能掌握它们）。

![](/sites/default/files/blogs/2017/1704/canvas-13-17.png)

可以看到，矩阵相乘非常繁琐而容易出错（这也是我们通常让计算机做这件事的原因），而且当矩阵变大以后很快就会出现问题。如果你仍然希望了解更多，或对矩阵的数学性质感到好奇，我强烈推荐你看看[可汗学院](//www.khanacademy.org/math/algebra2/algebra-matrices)的矩阵教程。

不管怎样，现在我们知道如何进行矩阵相乘了，我们可以开始学习好东西了。

#### 矩阵与向量相乘

目前为止，通过这些教程我们已经相当了解向量了。我们用向量来表示位置，表示颜色，甚至是纹理坐标。让我们更深入了解一下向量，它其实就是一个`N×1`矩阵，`N`表示向量分量的个数（也叫`N`维(N-dimensional)向量）。如果你仔细思考一下就会明白。向量和矩阵一样都是一个数字序列，但它只有`1`列。那么，这个新的定义对我们有什么帮助呢？如果我们有一个M×N矩阵，我们可以用这个矩阵乘以我们的`N×1`向量，因为这个矩阵的列数等于向量的行数，所以它们就能相乘。

但是为什么我们会关心矩阵能否乘以一个向量？好吧，正巧，很多有趣的2D/3D变换都可以放在一个矩阵中，用这个矩阵乘以我们的向量将变换(Transform)这个向量。如果你仍然有些困惑，我们来看一些例子，你很快就能明白了。

#### 单位矩阵

最简单的变换矩阵就是[单位矩阵](//zh.wikipedia.org/wiki/%E5%96%AE%E4%BD%8D%E7%9F%A9%E9%99%A3)(Identity Matrix)。单位矩阵是一个除了对角线以外都是`0`的`N×N`矩阵。在下式中可以看到，这种变换矩阵使一个向量完全不变：

![](/sites/default/files/blogs/2017/1704/canvas-13-18.png)

向量看起来完全没变。从乘法法则来看就很容易理解来：第一个结果元素是矩阵的第一行的每个元素乘以向量的每个对应元素。因为每行的元素除了第一个都是`0`，可得：`1⋅1+0⋅2+0⋅3+0⋅4=1`，向量的其他`3`个元素同理。

## 理解Canvas中的transform、setTransform

为了能更好的理解Canvas中的`transform()`和`setTransform()`方法，我们花了很大的篇幅介绍了一些基础知识。如果你理解了上述的内容，接下来的内容就能更好的理解。那么我们接下来就开始来理解这两个方法。

- `CanvasRenderingContext2D.transform()`是Canvas 2D API使用矩阵多次叠加当前变换的方法，矩阵由方法的参数进行描述。可以实现缩放、旋转、移动和倾斜等效果。
- `CanvasRenderingContext2D.setTransform()`是Canvas 2D API使用单位矩阵重新设置（覆盖）当前的变换并调用变换的方法，此变换由方法的变量进行描述。

这两个方法都接受六个参数：

	ctx.transform(a, b, c, d, e, f);
	ctx.setTransform(a, b, c, d, e, f);

其中这六个参数可以使用一个变换矩阵来描述：

![](/sites/default/files/blogs/2017/1704/canvas-13-19.png)

其具体代表的是：

- `a`: 表示水平缩放`scaleX`
- `b`: 表示水平倾斜`skewX`
- `c`: 表示垂直倾斜`skewY`
- `d`: 表示垂直缩放`scaleY`
- `e`: 表示水平移动`translateX`
- `f`: 表示垂直移动`translateY`

该方法使用一个新的变化矩阵与当前变换矩阵进行乘法运算，该变换矩阵的形式如下：

![](/sites/default/files/blogs/2017/1704/canvas-13-20.png)

你可以忽略最后一行，因为你不需要也不能修改它的值。最重要的是第一行和第二行，其中包含的数字值对应画布中使用的`a~f`。如上图所示，每一个数字值都对应一种特定的变形。

在[坐标变换](//www.w3cplus.com/canvas/transformation-coordinates.html)一切中，我们得知：**除平移`translate()`之外，旋转`rotate()`、缩放`scale()`都可以围绕一个中心点来进行，如果不指定，在默认情况下是围绕`(0,0)`原点进行相应的变换**。

而前面也说过，`transform()`或`setTransform()`可以帮助我们实现`translate()`、`scale()`和`rotate()`等变形。那接下来我们来看看如何实现。

### 平移

通过前面的知识，我们知道，不管`transform()`还是`setTransform()`都是矩阵变换。那先来看看平移对应的阵。

假定有一个点的坐标是`P(x0,y0)`，将移动到`P(x,y)`位置处，再假定在`x`轴和`y`轴方向移动的大小分别为：

![](/sites/default/files/blogs/2017/1704/canvas-13-21.png)

如下图所示：

![](/sites/default/files/blogs/2017/1704/canvas-13-22.png)

不难知道：

![](/sites/default/files/blogs/2017/1704/canvas-13-23.png)

如果用矩阵来表示的话，就可以写成：

![](/sites/default/files/blogs/2017/1704/canvas-13-24.png)

前面也介绍了矩阵怎么相乘。

理论有了，咱们来写实例。先来看`translate()`平移的效果：

	ctx.fillStyle = '#f36';
    ctx.translate(100, 100);
    ctx.fillRect(0,0,100,100);

效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-13-25.png)](//codepen.io/airen/full/ZKYzpe/)

接下来我们换成`transform()`或`setTransform()`方法来实现：

	ctx.fillStyle = '#f36';
    ctx.transform(1, 0, 0, 1, 100, 100);
    ctx.fillRect(0,0,100,100);

根据前面的矩阵，可以很轻易得到`ctx.transform(1,0,0,1,100,100)`。效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-13-25.png)](//codepen.io/airen/full/oWgvZP/)

效果和前面的是一样的，也就是说：`ctx.transform(1, 0, 0, 1, dx, dy);`和`ctx.translate(dx, dy)`是等效的。

**注：**其中`ctx.transform(1, 0, 0, 1, dx, dy)`等同于`ctx.transform(0, 1, 1, 0, dx, dy)`。

上面看到的效果是`transform()`实现的平移，其实也可以直接将`transform()`替换成`setTransform()`。

### 缩放

理论上而言，一个点是不存在什么缩放变换的，但考虑到所有图像都是由点组成，因此，如果图像在`x`轴和`y`轴方向分别放大`k1`和`k2`倍的话，那么图像中的所有点的`x`坐标和`y`坐标均会分别放大`k1`和`k2`倍。

用公式表示就是：`(x y)` 代表原坐标的点，`(x'，y')`代表新坐标的点。

![](/sites/default/files/blogs/2017/1704/canvas-13-26.png)

如果用矩阵来表示就是：

![](/sites/default/files/blogs/2017/1704/canvas-13-27.png)

我们分别进行两个测试，`X`方向测试矩阵如下：

![](/sites/default/files/blogs/2017/1704/canvas-13-28.png)

	ctx.fillStyle = '#f36';
    ctx.transform(.5, 0, 0, 1, 0, 0);
    ctx.fillRect(0,0,100,100);

效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-13-29.png)](//codepen.io/airen/full/LyEYVo/)

可以看到，正方形在`X`方向上进行了缩放，变成了之前的`0.5`倍。再进行`Y`方向的矩阵测试：

![](/sites/default/files/blogs/2017/1704/canvas-13-30.png)

	ctx.fillStyle = '#f36';
    ctx.transform(1, 0, 0, .5, 0, 0);
    ctx.fillRect(0,0,100,100);

效果如下：

[![](/sites/default/files/blogs/2017/1704/canvas-13-31.png)](//codepen.io/airen/full/MmYWeG/)

没有问题，`Y`方向变成原来的`0.5`倍。

**注：**从上面的示例可以看出来，不管是`X`轴还是`Y`的缩放都是基于原点进行的。如果需要基于元素自身中心点做缩放，那需要在`transform()`或`setTransform()`之前先做`translate()`操作。当然也可以使用`transform()`或`setTransform()`来替代`translate()`。

### 旋转

前面我们看到了怎么通过`transform()`和`setTransform()`方法来实现`translate()`和`scale()`的效果，接下来看怎么实现`rotate()`效果。因为旋转涉及到角度的问题，这里有一个数学知识很有必要先进行了解。

假定有一个点`P(x0,y0)`相对坐标原点顺时针旋转`θ`到达点`P(x,y)`，同时假定`P`点离坐标原点的距离为`r`，如下图所示：

![](/sites/default/files/blogs/2017/1704/canvas-13-32.png)

那么要计算出`P`点的坐标，就需要运用到两角和公式：

    sin(α + θ) = sin(α)*cos(θ) + cos(α)*sin(θ)
    cos(α + θ) = cos(α)*cos(θ) - sin(α)*sin(θ)

除了有两角和公式之外，还有对应的两角差公式：

    sin(α - θ) = sin(α)*cos(θ) - cos(α)*sin(θ)
    cos(α - θ) = cos(α)*cos(θ) + sin(α)*sin(θ)

根据上图我们可以推导出，`P0`点以半径`r`旋转一定的角度`α`，然后位置在`(x0,y0)`位置处，并且从`(x0,y0)`处继续围绕原点旋转`θ`度到达`P`点`(x,y)`处。根据前面的两角和公式，可以计算出`P`点`(x,y)`的值：

    x = r * cos(α + θ) = r*cos(α)*cos(θ) - r*sin(α)*sin(θ)
    y = r * sin(α + θ) = r*sin(α)*cos(θ) + r*cos(α)*sin(θ)

而`x0 = r*cos(α); y0=r*sin(θ)`，那么：

    x = r * cos(α + θ) = r*cos(α)*cos(θ) - r*sin(α)*sin(θ) = x0*cos(θ) - y0*cos(θ)
    y = r * sin(α + θ) = r*sin(α)*cos(θ) + r*cos(α)*sin(θ) = y0*cos(θ) + x0*sin(θ)

如果用矩阵，就可以表示为：

![](/sites/default/files/blogs/2017/1704/canvas-13-33.png)

我们来进行一个`90`度旋转的测试，`cos(90)=0,sin(90)=1`,所以矩阵应该写成:

![](/sites/default/files/blogs/2017/1704/canvas-13-34.png)

将上面的矩阵通过`transform()`来表示就是`transform(0,1,-1,0,0,0)`。来看在Canvas中的效果：

    ctx.fillStyle = '#f36';
    ctx.transform(0,1,-1,0,0,0);
    ctx.fillRect(0,0,200,100);

如果就这样的话，大家在Canvas的画布中看不到任何的图形，有可能还以为是出错了，其实并非如此。为什么呢？因为如果从`(0，0)`点绘制一个旋转的图，那么肯定已经转到屏幕外面了，所以我们给它加一个偏移试试（位移前面介绍过了）：

    ctx.fillStyle = '#f36';
    ctx.transform(0,1,-1,0,200,50);
    ctx.fillRect(0,0,200,100);

这下效果就出来了：

[![](/sites/default/files/blogs/2017/1704/canvas-13-35.png)](//codepen.io/airen/full/NjPgwa/)

#### 斜切

斜切变换Skew在数学上又称为Shear Mapping或者Transvection，它是一种比较特殊的线性变换。大家不知道是否还记得，在上一节或者说本节内容前面，我们提到Canvas自定的变换有`rotate()`、`scale()`和`translate()`，就是没有看到`skew()`这样的方法。虽然没有自带`skew()`这样的方法，但值得庆幸的是，可以通过`transform()`或者说`setTransform()`方法来实现。接下来我们来看看怎么实现斜切变换。

斜切变换的效果就是让所有点的`x`坐标（或者`y`坐标）保持不变，而对应的`y`坐标（或者`x`坐标）按比例发生平移，且平移的大小和该点到`x`轴（或`y`轴）的垂直距离成正比。斜切变换，属于面积变换，即一个形状在斜切变换的前后，其面积是相等的。

比如下图，各点的`y`坐标保持不变，但其`x`坐标则按比例发生了平移。这种情况将发生水平斜切：

![](/sites/default/files/blogs/2017/1704/canvas-13-36.png)

下图各点的`x`坐标保持不变，但其`y`坐标则按比例发生了平移。这种情况将发生垂直斜切：

![](/sites/default/files/blogs/2017/1704/canvas-13-37.png)

与旋转变换相比，旋转变换是旋转坐标系中的点，而斜切内里是转坐标轴，坐标轴旋转之后，要保持各点的坐标值不变，所有各点的位置就变了，如下图：

![](/sites/default/files/blogs/2017/1704/canvas-13-38.png)

变换后，点在新坐标系中的位置不变，在原坐标系中的位置是：

    f(x) = x * tan(α)
    f(y) = y * tan(α)

计算如下：

![](/sites/default/files/blogs/2017/1704/canvas-13-39.png)

回到Canvas当中来，假定有一个点`P0(x0,y0)`经过斜切变换后得到`P(x,y)`，对于水平斜切，它们之关的关系是：

    x = x0 + k*y0
    y = y0

用矩阵表示就是：

![](/sites/default/files/blogs/2017/1704/canvas-13-40.png)

扩展到`3 x 3`的矩阵就是下面这样的形式：

![](/sites/default/files/blogs/2017/1704/canvas-13-41.png)

同理，对于垂直斜切，可以有：

![](/sites/default/files/blogs/2017/1704/canvas-13-42.png)

在数学上严格的斜切变换就是上面这样，但在Canvas中除了有上面说的情况之外，还可以同时进行水平、垂直斜切，那么矩阵就变成：

![](/sites/default/files/blogs/2017/1704/canvas-13-43.png)

根据上面的总结，我们使用`transform()`或`setTransform()`可以实现斜切效果：

    // 水平斜切
    ctx.transform(1, 0, k, 1, 0, 0) 或者 ctx.setTransform(1, 0, k, 1, 0, 0)
    // 垂直斜切
    ctx.transform(1, k, 0, 1, 0, 0) 或者 ctx.setTransform(1, k, 0, 1, 0, 0)
    // 水平和垂直方向斜切
    ctx.transform(1, k1, k2, 1, 0, 0) 或者 ctx.setTransform(1, k1, k2, 1, 0, 0)

注意，其中`k`或者`k1`和`k2`是弧度值，来看个实例：

    ctx.fillStyle = '#f36';
    ctx.transform(1, Math.PI * 30 / 180, Math.PI * 30 / 180, 1, 0, 0);
    ctx.fillRect(0,0,100,100);

上面的示例相当于CSS中的`transform: skew(30deg, 30deg)`:

[![](/sites/default/files/blogs/2017/1704/canvas-13-44.png)](//codepen.io/airen/full/MmYoxZ/)





<div class="blog-author media"><a class="media-object" href="//weibo.com/w3cplus" target="_blank"><img src="/sites/default/files/blogs/author/airen.jpg"></a><div class="media-body"><h3 class="media-heading"><a href="//weibo.com/w3cplus" target="_blank">大漠</a></h3><div class="media-des">常用昵称“大漠”，W3CPlus创始人，目前就职于手淘。对HTML5、CSS3和Sass等前端脚本语言有非常深入的认识和丰富的实践经验，尤其专注对CSS3的研究，是国内最早研究和使用CSS3技术的一批人。CSS3、Sass和Drupal中国布道者。2014年出版《<a href="//www.w3cplus.com/book-comment.html" target="_blank">图解CSS3：核心技术与案例实战</a>》。</div></div></div>

如需转载，烦请注明出处：[http://www.w3cplus.com/canvas/transformation-coordinates.html](//www.w3cplus.com/canvas/transformation-coordinates.html)