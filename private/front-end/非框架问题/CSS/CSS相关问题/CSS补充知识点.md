# CSS补充知识点

## :root选择器

>  css样式表标准中规定了root的含义是整个文档，相对于html的标签

## var() 函数

> var() 函数用于插入自定义的属性值，如果一个属性值在多处被使用，该方法就很有用

使用实例：

```css
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        :root {
            --main-bg: blue;
            --font-color: red;
        }
        div {
            width: 200px;
            height: 200px;
            background: var(--main-bg);
            text-align: center;
        }
        span {
            color: var(--font-color);
            font-weight: bold;
        }
    </style>
</head>

    <div>
        <span>var()函数的使用</span>
    </div>

<body>
    
</body>
</html>
```

![image-20210112112944698](.\imgs\image-20210112112944698.png)

## css中::before和::after的区别

```css
/* ::after表示法是在CSS 3中引入的，::符号是指伪元素，:符号是指伪类（:hover）。
支持CSS3的浏览器同时也都支持CSS2中引入的表示法:after  */
element:after  { style properties }  /* CSS2 语法 */
element::after { style properties }  /* CSS3 语法 */
```

```css
同：
::before和::after都是某标签的兄弟节点

异：
::before 是某标签 前 的兄弟元素
::after 是某标签 后 的兄弟元素
```

![image-20210113083421751](.\imgs\image-20210113083421751.png)

> 1. **伪元素不属于文档**，所以js无法操作它
>
> 2. 伪元素属于主元素的一部分，因此**点击伪元素触发的是主元素的click事件**

​	

## block、inline、inline-block的区别

* block

> 1. block元素会独占一行，多个block元素会各自新起一行。默认情况下，block元素宽度自动填满其父元素宽度
> 2. block元素可以设置width,height属性。**块级元素即使设置了宽度,仍然是独占一行**
> 3. block元素可以设置margin和padding属性

* inline

  > 1. inline元素不会独占一行，多个相邻的行内元素会排列在同一行里，直到一行排列不下，才会新换一行，其宽度随元素的内容而变化
  > 2. inline元素设置width,height属性无效
  > 3. inline元素的margin和padding属性，水平方向的padding-left, padding-right, margin-left, margin-right都产生边距效果；
  >    但竖直方向的padding-top, padding-bottom, margin-top, margin-bottom不会产生边距效果（==水平生效，垂直无效==）

* inline-block

> 1. 简单来说就是将对象呈现为inline对象，但是对象的内容作为block对象呈现。之后的内联对象会被排列在同一行内。换句话说就是==使其既具有block的宽度高度特性又具有inline的同行特性==

举例子：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
    <style>
        * {
            padding: 0;
            margin: 0;
        }
        a {
            display: inline-block;
            width: 200px;
            height: 200px;
            padding-right: 10px;
            padding-bottom: 10px;
            margin-top: 30px;
            background: red;
        }
        span {
            display: inline-block;
            width: 200px;
            height: 200px;
            background: yellowgreen;
        }
    </style>
<body>
    <div>我是上面的块元素</div>
    <a href="http://">我是a标签1（行内元素）</a>
    <a href="http://">我是a标签2（行内元素）</a>
    <span>span（行内元素）</span>
    <div>我是下面的块元素</div>
</body>
</html>
```

效果：

![image-20210115104819407](.\imgs\image-20210115104819407.png)



## 浮动float

* float的基本属性值

| 值      | 描述                                               |
| :------ | :------------------------------------------------- |
| left    | 元素向左浮动                                       |
| right   | 元素向右浮动                                       |
| none    | 默认值。元素不浮动，并会显示在其在文本中出现的位置 |
| inherit | 规定应该从父元素继承 float 属性的值                |

* ==高度塌陷问题的解决==

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }
        .box1 {
            border: 10px solid red;
            /* 解决方式一: 为父元素添加固定高度 */
            /* height: 100px; */

            /* 解决方式二: 开启BFC属性(这是开启BFC副作用最小的方式) */
            overflow: hidden;
        }
        .box2 {
            width: 100px;
            height: 100px;
            background-color: blueviolet;
            float: left;
        }
        .box3 {
            height: 100px;
            background-color: chocolate;
        }
    </style>
</head>
<body>

    <div class="box1">
        <div class="box2"></div>
    </div>

    <div class="box3"></div>

</body>
</html>
```

总结 ：

1. 使用float来使元素脱离文档流
2. 内联元素脱离文档流以后会变成块级元素（就可以设置宽高等..）
3. 解决高度塌陷（通过在父元素中添加overflow: hidden;属性开启BFC属性）

## filter(滤镜) 属性

| Filter         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| blur(*px*)     | 给图像设置高斯模糊。"radius"一值设定高斯函数的标准差，或者是屏幕上以多少像素融在一起， 所以值越大越模糊；如果没有设定值，则默认是0；这个参数可设置css长度值，但不接受百分比值 |
| grayscale(*%*) | 将图像转换为灰度图像。值定义转换的比例。值为100%则完全转为灰度图像，值为0%图像无变化。值在0%到100%之间，则是效果的线性乘子。若未设置，值默认是0 |

filter 属性定义了元素(通常是`<img>`)的可视效果(例如：模糊与饱和度)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }
        img {
            /* 设置模糊程度 */
            /* filter: blur(10px); */
            /* 设置灰度程度 */
            filter: grayscale(70%);
        }
    </style>
</head>

<body>
    <img src="https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1651322215,2308890897&fm=11&gp=0.jpg" alt="">
</body>

</html>
```

## :nth-child(n) 选择器

==:nth-child(n) 选择器匹配属于其**父元素**的第 n个子元素，**不论元素的类型**==

n 可以是数字、关键词或公式

```html
p:nth-child(2)
{
background:#ff0000;
}

<body>

<h1>这是标题</h1>
<p>haorooms第一个段落。</p>
<p>haorooms第二个段落。</p>
<p>haorooms第三个段落。</p>
<p>haorooms第四个段落。</p>

</body>
```

上面这段代码请问那个段落颜色是红色的？

答案是“haorooms第一个段落。”颜色变成了红色！

## white-space 属性

| 值       | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| normal   | 默认。空白会被浏览器忽略                                     |
| pre      | 空白会被浏览器保留。其行为方式类似 HTML 中的 <pre> 标签      |
| nowrap   | 文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止 |
| pre-wrap | 保留空白符序列，但是正常地进行换行                           |
| pre-line | 合并空白符序列，但是保留换行符                               |
| inherit  | 规定应该从父元素继承 white-space 属性的值                    |

例如：

在使用属性值，nowrap时，针对行内元素，声明在父元素中，使得子元素的行内元素在填充慢一行时，不进行换行