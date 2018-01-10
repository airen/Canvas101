###《每周一点canvas动画》——用户交互
> [每周一点canvas动画代码文件](https://github.com/supperjet/H5-Animation)

用户交互也许是我们学习canvas动画中首先需要掌握的部分。毕竟，如果没有交互或者向动画中做一些动态的输入，那么这跟看电影有什么区别呢？用户交互基于事件，一般来说包括：`鼠标事件`，`触摸事件`和`键盘事件`。

####1、事件和事件执行
在理解事件之前，你需要明白什么是`listener`和`handler`。
listener(即监听器)决定当一个事件发生时是否做出反应。handler(即执行者)是一个函数，当事件发生时被调用。好了，扯了这么多直接上代码：
```
    element.addEventListener(type, handler[, useCapture]);

    type: 事件类型
    handler: 事件执行函数
    useCapture: 可选，为布尔值false/true, 表示在冒泡/捕获阶段执行

```
通过方法`addEventListener`来为某一元素添加事件，具体到我们的canvas上是什么样的呢？加入我们现在想要在canvas上绑定一个`mousedown`事件，具体代码如下：
```
    canvas.addEventListener('mousedown', function(event){
        console.log("Mouse pressed on element");
    }, false)

```
这样我们就为canvas绑定了鼠标点击事件，当在canvas上按下鼠标是就会在控制台看到打印出 "Mouse pressed on element"。

那么既然有添加事件(`addEventListener`)，就有移除事件(`removeEventListener`)，使用方式与添加事件几乎完全一样：
```
	element.removeEventListener(type, handler[, useCapture]);
	type: 事件类型
    handler: 事件执行函数
    useCapture: 可选，为布尔值false/true, 表示在冒泡/捕获阶段执行
```
唯一需要注意的是`handler`,即移除事件的函数，这里只能写函数名，而不能像添加事件一样将整个功能函数全部写入。也就是说，在添加某个事件的时候，我们可以将需要执行的函数写在事件监听之外并命名，这样如果你想要在后续的代码中移除该事件，直接将函数名传入移除事件的`handler`中即可。

现在让我们来实验下先为canvas添加一个事件，再将其移除
```
<body>
  <canvas id="canvas" width="500" height="500"></canvas>
  <script></script>
   <script>
       window.onload = function(){
           var canvas = document.getElementById('canvas');
           
           //定义的执行函数add
           function add(event){
               console.log("mouse down");
           }
           canvas.addEventListener('mousedown', add, false);
           
           //移除事件mousedown
           canvas.removeEventListener('mousedown', add, false)
       }
   </script>
</body>

```
现在你可以看看控制台是否还能打印出“mouse down”！

####2.鼠标事件
鼠标事件一共可以分为：
- mousedown
- mouseup
- click
- dbclick
- mousewheel
- mouseover
- mouseout

每一个鼠标事件都包含两个属性来决定当前鼠标的位置：`pageX`和`pageY`。通过`pageX`和`pageY`，还有canvas元素的偏移位置，我们就能够计算出鼠标具体是在canvas元素的什么位置。为了考虑不同浏览器的兼容性，以防万一你可以使用`clientX`和`clientY`。在这里，我们创建一个js文件，名为`**utils.js**`,这个文件是我们的一个工具函数，里面会逐渐加入一些我们重复使用的方法，那么现在我们向我们的工具函数中添加第一个方法`captureMouse`,具体代码如下：
```
utils.js文件

    //将utils定义为window对象下的一个属性，属性值为对象
    window.utils = {};

    //在utils对象上定义捕获坐标的方法
    window.utils.captureMouse = function(element){
    		//定义一个名为mouse的对象
            var mouse = {x:0,y:0};
            
			//为元素绑定mousemove事件
            element.addEventListener('mousemove',function(event){
                var x,y;
                
                //获取鼠标位于当前屏幕的位置， 并作兼容处理
                if(event.pageX||event.pageY){
                    x = event.pageX;
                    y = event.pageY;
                }else{
                    x = event.clientX + document.body.scrollLeft +document.documentElement.scrollLeft;
                    y = event.clientY + document.body.scrollTop +document.documentElement.scrollTop;
                }
                //将当前的坐标值减去元素的偏移位置，即为鼠标位于当前canvas的位置
                x -= element.offsetLeft;
                y -= element.offsetTop;

                mouse.x = x;
                mouse.y = y;
            },false);
             //返回值为mouse对象
             return mouse;
        }
```
这个方法将DOM元素作为参数传入，这样我们只要将canvas传入就可以获取到鼠标在当前canvas的位置。具体代码如下：
```
    <canvas id="canvas" width='500' height="500" style="background:#000">
           <p>you browser not support canvas!<p>
       </canvas>
       <script src='../js/utils.js'></script>
       <script>
           window.onload = function(){
              var canvas = document.getElementById('canvas'),
                  //将canvas传入，该方法会返回一个包含属性x和y的对象
                  mouse = utils.captureMouse(canvas);

              //为canvas绑定mousedown事件，当鼠标按下的时候打印出当前鼠标相对于canvas的坐标值
              canvas.addEventListener('mousedown',function(event){
                console.log("x:" +mouse.x +",y:" + mouse.y);
              });
       </script>

```
Have a try!!!看看能否成功。

##### getBoundingClientRect（）
其实，关于canvas的鼠标位置获取的方法还可以应用它自身的一个方法`getBoundingClientRect`，这里做一个介绍，你可以使用，但本系列文章主要使用上面这种更具广泛性的方法。具体代码可以参考如下：
```
        canvas.addEventListener('mousedown',function(event){
                       //event兼容处理
                       var event = event || window.event;
                       //兼容处理，获取当前鼠标相对屏幕的坐标
                       var winX = event.clientX+document.body.scrollLeft +document.documentElement.scrollLeft || event.pageX;
                       var winY = event.clientY+document.body.scrollTop +document.documentElement.scrollTop || event.pageY;
                       
					   //定义一个对象
                       var can = {x:0, y:0};
                       //调用getBoundingClientRect方法，该方法返回一个对象，包含canvas的left、 top、 width、 height等值
                       
                       var canBox = canvas.getBoundingClientRect();
                       
                   //（winX - canBox.left）：与上面的含义一样，是减去canvas的偏移量
                   //（canvas.width/canBox.width）：一般来说canvas.width和canBox.width是一样的，也就是说这两个的比值为1.但不排除你会为canvas设置边框，这是实际的坐标位置就会有所变化，比如canvas.width = 500, 你可能设置了一个1px的边框，那么canBox.width = 502, 所以比值就不为1了。这样做只是让数据更精确。
                   
                       can.x = (winX - canBox.left)*(canvas.width/canBox.width);
                       can.y = (winY - canBox.top)*(canvas.height/canBox.height);
                       
                       //输出
                       console.log(can.x，can.y);
                   },false);

```
###3、键盘事件
键盘事件就两个：
- keydown
- keyup

我们同样可以向绑定鼠标事件那样为canvas绑定键盘事件。好吧！现在我们来看看，如何将一个键盘事件绑定到window(为什么不直接绑定到canvas上呢？想想)上：
```
<body >
    <p>任意按下按键</p>
   <script>
       window.onload = function(){
       
   		   //定义键盘事件
           function onKeyboard(event){
              switch (event.keyCode){
                  case 38:
                      console.log('up!');
                      break;
                  case 40:
                      console.log('down!');
                      break;
                  case 37:
                      console.log('left!');
                      break;
                  case 39:
                      console.log('right!');
                      break;
                  default:
                      console.log(event.keyCode);
           }
        }
        //为window对象绑定键盘事件
        window.addEventListener('keydown',onKeyboard,false)；
       }
    </script>
</body>

```
试一试，当按下鼠标的方向键是是否在控制台打印出了相应的信息！

###4、触摸事件
触摸事件包括以下3种：
- touchstart
- touchend
- touchmove

触摸实践中，手指就充当了鼠标的作用。同样我们最为关心的是当前触摸的位置。和`captureMouse`方法一样，这里在我们的工具函数文件中，需要添加一新的方法来捕获触摸的位置，名为`captureTouch`,现在在utils.js文件中添加如下方法：
```
utils.js文件

    window.utils.captureTouch = function (element) {
      var touch = {
      				x: null,
                    y: null,
                    isPressed: false,
                    event: null
                    }；
      var body_scrollLeft = document.body.scrollLeft,
          element_scrollLeft = document.documentElement.scrollLeft,
          body_scrollTop = document.body.scrollTop,
          element_scrollTop = document.documentElement.scrollTop,
          offsetLeft = element.offsetLeft,
          offsetTop = element.offsetTop;
          
	 // 绑定touchstart事件
      element.addEventListener('touchstart', function (event) {
        touch.isPressed = true;
        touch.event = event;
      }, false);
      
	 // 绑定touchend事件
      element.addEventListener('touchend', function (event) {
        touch.isPressed = false;
        touch.x = null;
        touch.y = null;
        touch.event = event;
      }, false);
      
	 //绑定touchmove事件
      element.addEventListener('touchmove', function (event) {
        var x, y,
            touch_event = event.touches[0]; //第一次touch

        if (touch_event.pageX || touch_event.pageY) {
          x = touch_event.pageX;
          y = touch_event.pageY;
        } else {
          x = touch_event.clientX + body_scrollLeft + element_scrollLeft;
          y = touch_event.clientY + body_scrollTop + element_scrollTop;
        }
        //剪去偏移量
        x -= offsetLeft;
        y -= offsetTop;

        touch.x = x;
        touch.y = y;
        touch.event = event;
      }, false);
	  //返回touch对象
      return touch;
    };

```

###总结
这一节主要介绍用户与canvas交互的各种事件，重要的是你应该在你自己的工具函数文件中包含了以下两个方法：`utils.captureTouch`和`utils.captureMouse`这两个方法都是为了获取当前相对于canvas元素的位置。我们将在后面的章节中频繁使用。当然，除了这两个方法，由于我们使用的`requestAnimationFrame`方法同样也涉及到兼容性的问题，我们将它一同添加到`utils.js`中，具体代码请查看`utils.js`文件。
下一节，三角函数坐标旋转敬请期待！！！
