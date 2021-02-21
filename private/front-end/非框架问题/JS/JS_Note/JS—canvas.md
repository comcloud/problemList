# canvas

## 获取canvas元素

```html
<canvas id="mycanvas" width="800" height="800">您的浏览器不支持，请更换浏览器</canvas>
```

```js
//获取节点
var mycanvas = document.getElementById("mycanvas");
//获取上下文
var ctx = mycanvas.getContext('2d');
//对于canvas节点通过.getContext可以获取绘画上下文（绘画环境），通过上下文环境可以对图画进行绘画操作
// 扩展：获取上下文类型2d、webgl2、bitmaprenderer等等
```

## 矩形

`rect()`:创建矩形

`fillRect()`:绘制‘被填充’的矩形

```js
//路径
ctx.rect(50, 50, 100, 100);
//绘制并且填充矩形
ctx.fillRect(50, 50, 50, 50);
```

![image-20210210145309128](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210145309128.png)

`strokeRect()`:绘制矩形(无填充)

`clearRect()`:在给定的矩形内清楚指定的像素

```js
//绘制图像路线 单独不起作用
//参数 起始位置x 起始位置y 宽 高
ctx.rect(50,50,100,100);

//路径初始化
//每次画一个图像都需要初始化路径
ctx.beginPath();
```

## 填充

`fill()`:填充当前绘图（路径）

```js
ctx.beginPath();
//路径
ctx.rect(50, 50, 100, 100);
//填充样式（颜色、渐变、模式）
ctx.fillStyle="green";//填充颜色
//填充
ctx.fill()
```

![image-20210210143038480](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210143038480.png)

## 描边

`stroke()`:绘制已定义的路径

```js
ctx.beginPath();
//路径
ctx.rect(50, 50, 100, 100);
//描边样式
ctx.strokeStyle = "green"//描边颜色
//描边
ctx.stroke();
```

![image-20210210143319913](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210143319913.png)

## 画线条

`moveTo()`:把路径移动到画布中的指定点，不创建线条

`lineTo()`:添加一个新点，然后在画布中创建从该点到最后指定点的线条

```js
ctx.beginPath();
//设置路径开始点
//参数 起始位置点
ctx.moveTo(350, 50);
//从之前的点画到这里
//参数 到哪个点
ctx.lineTo(450, 50);
ctx.lineTo(450, 150);
ctx.lineTo(350, 150);
ctx.lineTo(350, 50);

//设置线条宽度
ctx.lineWidth = 4;
ctx.stroke();
```

![image-20210210144818065](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210144818065.png)

```js
ctx.beginPath();
ctx.moveTo(600, 50);
ctx.lineTo(670, 150);
ctx.lineTo(530, 150);
//直接画到原来的点 路径没有闭合
ctx.lineTo(600,50);
ctx.lineWidth = 10;
ctx.stroke();
```

![image-20210210145010940](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210145010940.png)

**修改**

```js
ctx.beginPath();
ctx.moveTo(600, 50);
ctx.lineTo(670, 150);
ctx.lineTo(530, 150);
//闭合路径 最后一个点和开始的点连接在一起
ctx.closePath();
ctx.lineWidth = 10;
ctx.stroke();
```

![image-20210210145118161](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210145118161.png)

## 圆形

`arc()`:创建弧形/曲线（用于创建圆形或部分圆）

```js
ctx.beginPath();
//圆形 弧 参数 ： 中心点x  中心点y 半径r 起始弧度 结束弧度[顺时针false/逆时针true]
ctx.arc(100, 300, 80, 0, 2 * Math.PI);
ctx.arc(350, 300, 50, 2 * Math.PI / 6, 2 * Math.PI);
ctx.fillStyle = "yellow"
ctx.fill();
```

![image-20210210145727716](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210145727716.png)

## 线性渐变

`createLinearGradient()`创建线性渐变（用在画布内容上）

`addColorStop()`:规定渐变对象中的颜色和停止位置

```js
var grd = ctx.createLinearGradient(0, 0, 170, 0);
grd.addColorStop(0, "red");
grd.addColorStop(0.3, "#f71");
grd.addColorStop(0.6, "#17f");
grd.addColorStop(1, "yellow");
```

```js
ctx.font = "32px 微软雅黑";
ctx.fillStyle = grd;//设置渐变
ctx.fillText('兄弟们好', 100, 500);
```

![image-20210210150240767](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210150240767.png)

## 对齐

```js
//水平对齐
ctx.textAlign = 'center';
//垂直对齐
ctx.textBaseline = 'middle';
```

![image-20210210150401562](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210150401562.png)

## 转换

`translate()`:重新映射画布上的 (0,0) 位置

`rotate()`:旋转当前绘图

```js
 //设置画布中心点
ctx.translate(mycanvas.width/2,mycanvas.height/2);

//正三角形
ctx.beginPath();
// ctx.moveTo(0,0);
 ctx.lineTo(0,-100);

//旋转一圈是2PI 旋转1/3
ctx.rotate(2*Math.PI/3);
ctx.lineTo(0,-100);
ctx.rotate(2*Math.PI/3);
ctx.lineTo(0,-100);
ctx.closePath();
ctx.lineWidth=4;
ctx.stroke();
```

![image-20210210203458328](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210210203458328.png)

## 其他

save():保存当前环境的状态

restore():返回之前保存过的路径状态和属性

有save：

```js
        //正多边形
        function drowShape(n,pr) {
            var r=pr || 100;
            //保存绘画环境
            ctx.save();

            ctx.beginPath();
            ctx.moveTo(0, -r);
            for (var i = 1; i < n; i++) {
                ctx.rotate(2 * Math.PI / n);
                ctx.lineTo(0, -r);
            }

            ctx.closePath();
            ctx.stroke();
            //还原绘画环境
            ctx.restore();
        }
        ctx.lineWidth = 4;
        drowShape(3,30);
        drowShape(4,70);
        drowShape(5);
```

![image-20210221145934415](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210221145934415.png)

没有save:

```js
        //正多边形
        function drowShape(n,pr) {
            var r=pr || 100;
            //保存绘画环境
            // ctx.save();

            ctx.beginPath();
            ctx.moveTo(0, -r);
            for (var i = 1; i < n; i++) {
                ctx.rotate(2 * Math.PI / n);
                ctx.lineTo(0, -r);
            }

            ctx.closePath();
            ctx.stroke();
            //还原绘画环境
            ctx.restore();
        }
        ctx.lineWidth = 4;
        drowShape(3,30);
        drowShape(4,70);
        drowShape(5);
```

![image-20210221150025529](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CDesktop%5CT%5Cimage-20210221150025529.png)