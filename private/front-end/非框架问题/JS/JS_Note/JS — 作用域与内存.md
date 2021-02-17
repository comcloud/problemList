# JS — 作用域与内存

## 传递参数

==ECMAScript 中所有函数的参数都是按值传递的==

* 原始值作为参数

```js
function addTen(num) {
  return num += 10;
}

let count = 1;
let result = addTen(count);

console.log(count);  // 1  没有变化
console.log(result); // 11
```

addTen函数的参数num在函数被调用时，复制了count变量的值，以便在函数内部使用，所以在函数内部改变此参数并不会影响函数外部的count

> 一个容易误导开发者的例子

```js
  function changeName(obj) {
    obj.name = 'mrkleo';
  }
  let person = {};
  changeName(person);
  console.log(person.name);  // mrkleo
```

从上面的例子，person作为changeName函数的实参被复制给obj变量，在函数内部obj与person指向同一个对象；即使对象是按值传进函数的，obj也会通过引用访问对象，当函数内部给obj设置了name属性，函数外部的对象也会反映这个变化，==因为obj指向的对象保存在全局作用域的堆内存上==

<img src="..\JS_img\image-20210214100518700.png" alt="image-20210214100518700" style="zoom:67%;" />

* 引用值作为参数

```javascript
function changeName(obj) {
  obj.name = 'mrkleo';
  obj = new Object();
  obj.name = 'new name';
}

let person = {};
changeName(person);  

console.log(person.name);  // mrkleo 
```

按照上面例子的分析，当执行到函数的第一行代码时，此时的person与obj指向的是同一个对象；当执行到第二行代码时，==obj变成了指向本地对象的指针（按我的理解就是指向了函数作用域的堆内存中的对象）==，==当函数执行完毕后，这个本地对象会被销毁==

<img src="..\JS_img\image-20210214101248311.png" alt="image-20210214101248311" style="zoom:67%;" />



## 执行上下文与作用域

![](..\JS_img\1013598-20160910103037879-1633973943.gif)

==内部上下文可以通过作用域链访问外部上下文中的一切，但外部上下文不能访问内部上下文的任何东西==

```js
let glo = 2;
console.log(son);   // 报错

function todotest() {
  let func = 3;

  function son_fun() {
    let son = 4;
    console.log(glo);   // 2
  }
  son_fun();
}
todotest();
```

