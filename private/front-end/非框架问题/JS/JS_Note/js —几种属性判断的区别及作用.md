# typeof ，instanceof，in， hasOwnProperty的区别及作用

## typeof

​	`typeof`操作符用于返回正在使用值的类型。

## instanceof

​	通过使用`instanceof`操作符，可以确定一个**对象**是否是特定**构造函数**的**实例**，返回`true`或`false`。

```js
class father{

    } 
class sons extends father{

    }
var son1=new sons();
console.log(son1 instanceof father);//true

function fa(){

    }
function son(){

    }
son.prototype=new fa();
var s1=new son();
console.log(s1 instanceof fa);//ture
```

使用 `obj instanceOf Class` 检查 `Class.prototype` 是否等于 `obj` 的原型链中的原型之一。

换句话说就是，一个接一个地比较：
```js
obj.__proto__ === Class.prototype? 
obj.__proto__.__proto__ === Class.prototype? 
obj.__proto__.__proto__.__proto__ === Class.prototype? ... // 如果任意一个的答案为 true，则返回 true // 否则，如果我们已经检查到了原型链的尾端，则返回 false
```

例子：

```js
var a=new Array();
console.log(a.__proto__);
console.log(Array.prototype);
console.log(a instanceof Array);//true
```

## in

`in`操作符可以检查一个对象的属性，包括来自原型链的属性

语法：`"key" in object`

```js
let user = { name: 'asda',age:123 };
//age是自己的属性，toString是它从原型链上继承下来的属性
console.log('name' in user);//true
console.log('toString' in user);//true
console.log('name' in Object);//true
console.log('age' in Object);//false
// 为什么这里的name是true,age是false，因为Object中自己有name属性，而没有age属性
console.log(Object.hasOwnProperty('name')); // true
console.log(Object.hasOwnProperty('age')); // false
```

## hasOwnProperty

判断是否为自身的属性，不是原型的属性

```js
function obj0() {
      this.name = 'vx',
      this.age = '18'
   };
obj0.prototype.gender = 'male';
let obj1 = new obj0();

// 打印所有可枚举属性
for (let key in obj1) {
 console.log(key); // name age gender 从原型链上继承下来的属性也会被打印出来
}
// 过滤掉原型链上的属性
for (let key in obj1) {
    if (obj1.hasOwnProperty(key)) {
          console.log(key); // name age
     }
}
```

