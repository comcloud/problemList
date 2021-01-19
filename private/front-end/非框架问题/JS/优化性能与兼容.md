## 优化性能 兼容

### 对象检测

​	意义：检测浏览器对js的支持程度。

​	==解决方案==：将被检测的方法打包在一个`if`语句里，更具表达式的求职结果是true（方法存在），还是false(方法不存在)来决定采取怎样的行动。

例如：有一个使用了`getElementById()`方法的函数，就可以在调用此方法之前检查浏览器是否支持这个方法

```js
function muFunction(){
	if(document.getElementById){
		statements using getElementById
	}
}
```

如果浏览器不支持`getElementById()`方法，它永远也不会执行使用此方法的语句

==这个解决方案的不足是==，编写出来的函数会增加一对花括号。如果在函数中需要检验多个DOM方法/属性是否存在，函数中的语句就会被深埋在一层层的花括号中。这样的代码很难理解和阅读。

​	==改进方案==：把测试条件改为“如果为false”,使用'逻辑非'操作符

```js
if(!docment.getElementById) return false;
```

这样可以让js有良好的兼容性。

### 性能优化

#### 	减少DOM访问

​	实例代码:

```js
if(document.getElementsByTagName('a').length>0){
	var links=document.getElementsByTagName('a');
	for(var i=0;i<links.length;i++){
		//对每个连接进行处理
	}
}
```

此代码搜索了两次`a`标签，不管什么时候，只要是查询DOM中的某些元素，浏览器都会搜索整个DOM树，更好的方法是把第一次搜索的结果存在变量中

```js
var links=document.getElementsByTagName('a');
if(links.length>0){
	for(var i=0;i<links.length;i++){
		//对每个连接进行处理
	}
}
```

这样代码的功能没有变，但搜索DOM的次数减少了一次

#### 合并放置脚本

​	一般我们都是用外部脚本文件

```js
<script src="script/function.js"></script>
```

​	但是如下情况，最好不要出现

```js
<script src="script/functionA.js"></script>
<script src="script/functionB.js"></script>
<script src="script/functionC.js"></script>
<script src="script/functionD.js"></script>
```

可以将以上四个文件合并到一个文件中，这样做是为了，==减少加载页面时发送的请求数量==

脚本放置的位置，对页面的初次加载时间有很大影响

传统将js放在文档的head区域，这样会导致浏览器无法并行加载其他的文件，这样就会导致初次打开网页时会出现一段时间的空白

可以将js放置在文档的末尾，`</body>`标签之前，这样，在加载js时也可以对文档进行操作

### 平稳退化

如果某浏览器不支持js，或者js被禁用，那么人们在访问网站是会遇到各种各样的麻烦，导致人们不想访问这个网站

但正确使用js，就可以让访问者在不支持js的情况下顺利浏览网站——最基本的操作仍能顺利完成

例子:

```css
<li>
	<a href="javascript:showPic('images/coffee.jpg');return false;"></a>
</li>
```

```css
<li>
	<a href="#" onclick=showPic('images/coffee.jpg');return false;"></a>
</li>
```

如果在禁用js或是不支持Js的时候，上面的代码在浏览器中毫无用处

解决方案：

```css
<li>
	<a href="images/coffee.jpg" onclick="showPic(this);return false;"></a>
</li>
```

即使js已经被禁用，这个链接也是可用的，不会导致图片加载不出来。