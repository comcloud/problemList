# 变量提升与执行上下文

## 变量提升

### 变量声明提升

​	通过`var`定义（声明）的变量，在定义语句之前就可以访问到，其值为`undefined`

```js
consloe.log(a)//undefined
var a=1
```

### 函数声明提升

​	通过`function`声明的函数，在语句之前就可以直接调用，值为对象

而通过表达式声明的函数`var fn=function(){}`不会进行变量提升，在语句之前访问会报错。

```js
consloe.log(fn)
//ƒ fn(){
//       console.log('fn')
//   }
function fn(){
	consloe.log('fn')
}
```

### 例子

```js
 	 var a=3;
        function fn(){
            console.log(a)
            var a=4;
        }
        fn();//undefined

        console.log(b)//undefined  变量提升

        fn2();//函数提升

        // fn3()//不能 变量提升

      var b=3;
        function fn2(){
            console.log('fn2')
        }
        var fn3=function(){

        } 
```

![image-20210228150724496](..\JS_img\image-20210228150724496.png)

## 执行上下文

代码分类：

- 全局代码
- 局部代码

### 全局执行上下文

- 在执行全局代码前将window确定为全局执行上下文
- 对全局数据进行预处理
	- var 定义的全局变量-->undefined,添加为window的属性
	- function声明的全局函数-->赋值（fun），添加为window的方法
	- this-->赋值（window）

```js
//全局执行上下文
console.log(a1,window.a1);//undefined undefined
a2();//a2()
console.log(this)//Window{}

var a1=3;
function a2(){
     console.log('a2()')
}
console.log(a1)//3
```

### 函数执行上下文

- 在调用函数，准备==执行函数体之前==，创建对应的函数执行上下文对象
- 对局部数据进行预处理
	- 形参变量-->赋值（实参）-->添加为执行上下文的属性
	- var 定义的局部变量-->undefined,添加为执行上下文的属性
	- function声明的函数-->赋值（fun），添加为执行上下文的方法
	- this-->赋值（调用函数的对象）
	- arguments-->赋值（实参列表），添加为执行上下文的属性

```js
//函数执行上下文
function fn(a1){
    console.log(a1)//2
    console.log(a2)//undefined
    a3()//a3()
    console.log(this)//window
    console.log(arguments)//伪数组(2,3)
}
fn(2,3)
var a2=3;
function a3(){
    console.log('a3()')
}
```

### 执行上下文栈

1. 在全局代码执行前,js引擎就会创建一个栈来存储管理所有的执行上下文对象
2. 在全局执行上下文（window）确定后，将其添加到栈中（压栈）
3. 在函数执行上下文创建后，将其添加到栈中（压栈）
4. 在当前函数执行完后，将栈顶的对象移除（出栈）
5. 当所有的代码执行完后，栈中只剩下window

```js
                        //1.进入全局执行上下文
var a=10;
var bar=function(x){
    var b=5;
    foo(x+6)            //3.进入foo执行上下文
}
var foo=function(y){
    var c=5
    console.log(a+c+y)
}
bar(10)                 //2.进入bar函数执行上下文
```

![image-20210228162035311](..\JS_img\image-20210228162035311.png)

## 变量提升覆盖规则

### 同名函数

```js
// 同名函数声明
function foo() { console.log(1) }
function foo() { console.log(2) } //覆盖第一行
foo() //2
```

### 同名变量

```js
// 同名变量
var foo = function () { console.log(1) }
var foo = function () { console.log(2) }
foo()//2
```

会被解析为：

```js
var foo
var foo //被忽略
foo = function () { console.log(1) }
foo = function () { console.log(2) }
```

### 同名变量与函数

```js
// 同名变量和函数声明
function foo() {
      console.log(1)
}
var foo; //被忽略
foo(); //1
```

