# z-index的用法

### 一、z-index是什么

**定义：**一个元素在文档中的层叠顺序，用于确认元素在当前层叠上下文中的层叠级别。

**适用于：**定位元素。即定义了position为非static的元素

### 二、z-index的取值

`auto`:盒子不会创建一个新的本地堆叠上下文。在当前堆叠上下文中生成的盒子的堆叠层级和父级盒子相同。

`integer`:整型数字）是生成的盒子在当前堆叠上下文中的堆叠层级。此盒子也会创建一个堆叠层级为 0 的本地堆叠上下文。这意味着后代（元素）的 z-indexes 不与此元素的外部元素的 z-indexes 进行对比。（-1，0，1，2，3，4，5······）

### 三、z-index的简单语法

代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div{
            width: 200px;
            height: 200px;
        }
        #item1{
            background-color: aqua;
        }
        #item2{
            background-color: red;
            margin-top:-100px;
            margin-left:50px;
        }
        #item3{
            background-color: yellow;
            margin-top:-100px;
            margin-left:100px;
        }
    </style>
</head>
<body>
    <div id="item1"></div>
    <div id='item2'></div>
    <div id='item3'></div>
</body>
</html>
```

结果：

![image-20210130192503645](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210130192503645.png)



**添加z-index后**

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div {
            width: 200px;
            height: 200px;
        }
        #item1 {
            background-color: aqua;
            z-index: 999;
        }

        #item2 {
            background-color: red;
            margin-top: -100px;
            margin-left: 50px;
            z-index: 99;
        }

        #item3 {
            background-color: yellow;
            margin-top: -100px;
            margin-left: 100px;
            z-index: 9;
        }
    </style>
</head>

<body>
    <div id="item1"></div>
    <div id='item2'></div>
    <div id='item3'></div>
</body>

</html>
```

结果：

![image-20210130192611016](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210130192611016.png)

#### 图像没有改变 **为什么呢？**

#### ==因为z-index只对已经定位的元素起作用除（static）==

我们将元素设置定位

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div {
            width: 200px;
            height: 200px;
            position: relative;
        }
        #item1 {
            background-color: aqua;
            z-index: 999;
        }

        #item2 {
            background-color: red;
            margin-top: -100px;
            margin-left: 50px;
            z-index: 99;
        }

        #item3 {
            background-color: yellow;
            margin-top: -100px;
            margin-left: 100px;
            z-index: 9;
        }
    </style>
</head>

<body>
    <div id="item1"></div>
    <div id='item2'></div>
    <div id='item3'></div>
</body>

</html>
```

结果：

![image-20210130192943241](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210130192943241.png)

### 四：深入了解

​			每个元素层叠顺序由==所属的层叠上下文==和==元素本身的层叠级别==决定（每个元素仅属于一个层叠上下文）。

​			1、同一层叠上下文

​			层叠级别大的显示在上面，级别小的显示在下面；

​			层叠级别中的两个元素，依据它们在HTML文档流中的顺序，写在后面的将会覆盖前面的。

​			2、不同层叠上下文

​			元素的显示顺序依据祖先的层叠级别来决定，与自身的层叠级别无关。

**同一层叠上下文**

例：**1、有两个div盒子，a、c在一个盒子里，b在另一个盒子里，来考虑其z-index是不是正常效果。**

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div {
            width: 200px;
            height: 200px;
            position: relative;
        }
        p {
            position: absolute;
            font-size: 20px;
            width: 200px;
            height: 200px;
        }

        .a {
            background-color: pink;
            z-index: 1;
        }
        .c {
            background-color: green;
            z-index: 2;
            top: 40px;
            left: 40px;
        }
        .b{
            background-color: red;
            z-index: 3;
            top: -80px;
            left: 80px;
        }

    </style>
</head>

<body>
    <div id="item1">
        <p class="a">a</p>
        <p class="c">c</p>
    </div>
    <div id='item2'>
        <p class="b">b</p>
    </div>
</body>

</html>
```

a、b、c处于一个层叠上下文中，所以根据z-index大小来确定层级。如下图所示：

![image-20210130195954850](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210130195954850.png)

**不同层叠上下文**

**2、有两个div盒子，a、c在一个盒子里，b在另一个盒子里，来考虑其z-index是不是正常效果。**

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div {
            width: 200px;
            height: 200px;
            position: relative;
        }
        #item1{
            z-index: 2;
        }
        #item2{
            z-index: 1;
        }
        p {
            position: absolute;
            font-size: 20px;
            width: 200px;
            height: 200px;
        }

        .a {
            background-color: pink;
        }
        .c {
            background-color: green;
            top: 40px;
            left: 40px;
        }
        .b{
            background-color: red;
            z-index: 20;
            top: -80px;
            left: 80px;
        }

    </style>
</head>

<body>
    <div id="item1">
        <p class="a">a</p>
        <p class="c">c</p>
    </div>
    <div id='item2'>
        <p class="b">b</p>
    </div>
</body>

</html>
```

a、b、c处于不同的层叠上下文中，所以根据父级的z-index大小来确定层级。如下图所示：

![image-20210130200228174](C:%5CUsers%5C%E6%9D%8E%E4%B8%83%E5%A4%9C%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210130200228174.png)

### 五 0和auto

**z-index:0 的会创建一个新的层叠上下文而auto（默认值） 不会，两者在有区别（0 会在auto 上面）**(???)

