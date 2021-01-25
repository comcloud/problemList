# CSS相关问题

## transition 和 animation 的区别

> transition关注的是CSS property的变化，property值和时间的关系是一个三次贝塞尔曲线。
>
> animation作用于元素本身而不是样式属性，可以使用关键帧的概念，应该说可以实现更自由的动画效果。
>
> CSS transition 强调的是单一动画属性的过度效果,其过程是简单的，由开始到结束的过程，中间不存在可能的动画转折，只有0到1，比喻：渐隐，渐显;
>
> CSS animation 强调的是多种动画属性的结合，按时间轴线出现周折性动画变换的动画过程，其过程是复杂的，由开始——>结束的过程中，存在可能的动画转折，其过程可能是开始—0—1—2—3>结束的过程
>
> 综合来说：CSS animation可以包括CSS transition 的动画形式

## 如何让去除 inline-block 元素间间距

初始代码

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="../css/change.css">
</head>
<body>

    <div class="box"></div>
    <div class="box"></div>
    <div class="box"></div>

</body>
</html>
```



> 方法一：（移除标签间的空格）

```html
<!-- 移除标签间的空格 --> 
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>

<div class="box"></div><div class="box"></div><div class="box"></div>
```

> 方法二：（使用margin负值）

```css
* {
  padding: 0;
  margin: 0;
}

.box {
  /* 省略的代码 */
  margin-right: -5px;
}
```

> 方法三：（给==父元素节点==添加 font-size:0;）

```css
body {
  font-size: 0;
}
```

> 方法四：（给==父元素节点==添加 letter-spacing 的负值）

```css
body {
  letter-spacing: -5px;
}
```

> 方法五：（给==父元素节点==添加 word-spacing 的负值）

```css
body {
  word-spacing: -5px;
}
```

## **如果需要手动写动画，你认为最小时间间隔是多久，为什么？（阿里）**

因为一般的屏幕刷新率为60Hz，指在1秒屏幕刷新60次，所以最短的动画时间间隔为1/60*1000ms=16.7ms

## 使用 rem 布局的优缺点？

```bash
优点：
在屏幕分辨率千差万别的时代，只要将rem与屏幕分辨率关联起来就可以实现页面的整体缩放，使得在设备上的展现都统一起来了。
而且现在浏览器基本都已经支持rem了，兼容性也非常的好。

缺点：
（1）在奇葩的dpr设备上表现效果不太好，比如一些华为的高端机型用rem布局会出现错乱。
（2）使用iframe引用也会出现问题。
（3）rem在多屏幕尺寸适配上与当前两大平台的设计哲学不一致。即大屏的出现到底是为了看得又大又清楚，还是为了看的更多的问
题。
```

## 画一条0.5px的线

```bash
# PC端
采用transform:scale(0.5)的方式

采用border-image的方式

# 移端开发
<meta name="viewport" content="width=device-width, initial-scale=0.5">
```

## margin:auto 的填充规则？

```bash
margin的'auto'可不是摆设，是具有强烈的计算意味的关键字，用来计算元素对应方向应该获得的剩余间距大小。但是触发mar
gin:auto计算有一个前提条件，就是width或height为auto时，元素是具有对应方向的自动填充特性的。

（1）如果一侧定值，一侧auto，则auto为剩余空间大小。
（2）如果两侧均是auto，则平分剩余空间。
```

## letter-spacing 与字符间距？

```bash
letter-spacing可以用来控制字符之间的间距，这里说的“字符”包括英文字母、汉字以及空格等。

letter-spacing具有以下一些特性。

（1）继承性。
（2）默认值是normal而不是0。虽然说正常情况下，normal的计算值就是0，但两者还是有差别的，在有些场景下，letter-spacing会调整normal的计算值以实现更好的版面布局。
（3）支持负值，且值足够大的时候，会让字符形成重叠，甚至反向排列。
（4）和text-indent属性一样，无论值多大或多小，第一行一定会保留至少一个字符。
（5）支持小数值，即使0.1px也是支持的。
（6）暂不支持百分比值。
```

## word-spacing 与单词间距？

```bash
letter-spacing作用于所有字符，但word-spacing仅作用于空格字符。换句话说，word-spacing的作用就是增加空格的间隙
宽度。
```

## white-space 与换行和空格的控制？

```bash
white-space属性声明了如何处理元素内的空白字符，这类空白字符包括Space（空格）键、Enter（回车）键、Tab（制表符）
键产生的空白。因此，white-space可以决定图文内容是否在一行显示（回车空格是否生效），是否显示大段连续空白（空格是否
生效）等。

其属性值包括下面这些。
•normal：合并空白字符和换行符。
•pre：空白字符不合并，并且内容只在有换行符的地方换行。
•nowrap：该值和normal一样会合并空白字符，但不允许文本环绕。
•pre-wrap：空白字符不合并，并且内容只在有换行符的地方换行，同时允许文本环绕。
•pre-line：合并空白字符，但只在有换行符的地方换行，允许文本环绕。
```

