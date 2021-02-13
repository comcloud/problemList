# js---数据类型

## 分类

###  	基本类型

​			String ：任意字符串

​     	   Number ：任意的数字

  	      Boolean ：true/false
  	
  	       undefined : undefined
  	
  	     null : null
             ES6:Symbol
             Es10:BigInt

### 	引用类型

​			Object : 任意对象

   	     Function ： 一种特别的对象{可以执行}

​    	    Array ：一种特别的对象(数值下标,内部数据有序)

## 判断类型

#### 	typeof

​      	可以判断：undefined/number/String/Boolean/function

```js
var a;
console.log(a,typeof a,typeof a === 'undefined',a===undefined);//undefined "undefined" true true
a=3;
console.log(typeof a==='number');//true
a='vx';
console.log(typeof a==='string');//true
a=true;
console.log(typeof a);//boolean
```

​      	不能判断 ：null与object object与array

```js
var a;
a=null;
console.log(typeof a,a===null);//object true

var b1={
      b2:[1,'abc',console.log],
      b3:function(){
          console.log('b3');
      }
}
//    实例对象  是不是   构造函数      
console.log(b1 instanceof Object,b1 instanceof Array);//true false
console.log(b1.b2 instanceof Array,b1.b2 instanceof Object);//true true
console.log(b1.b3 instanceof Function , b1.b3 instanceof Object);//true true
console.log(typeof b1.b3 ==='function');true
```

####     instanceof 

​      	判断对象的 具体 类型

####     ===

​      	可以判断undefined,null

## 问题

### undefined与null的区别

​      undefined代表定义未赋值

​      null代表定义并赋值了，值为null

```js
var a;
console.log(a);//undefined
a=null; //typeof object 
console.log(a,typeof a);//undefined object
```

### 什么时候给变量赋值为null

​	 初始赋值，表面将要赋值为对象

​     结束前，让对象成为垃圾对象（回收）

```js
var b=null;//初始赋值为null，表明将要赋值为对象
//确定对象就赋值
b=['vx',12];
//最后
 b=null;
//['vx',12];所在的空间被回收
```

### 严格区别变量类型与数据类型

​	**数据的类型**

​      基本类型

​      对象类型

​    **变量的类型**(变量内存值的类型)(变量本身没有类型)

​      基本类型：保存的就是基本类型的数据

​      引用类型：保存的是地址值

