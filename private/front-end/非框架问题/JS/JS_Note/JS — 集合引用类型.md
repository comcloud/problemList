# JS — 集合引用类型

## Object

创建对象的两种方式：

1. 通过new关键字 + Object构造函数

```js
  let obj = new Object();
  obj.name = 'dancer';
  obj.age = 20;
  console.log(obj);  // {name: "dancer", age: 20}
```

2. 通过对象字面量的形式（==这种方式并不会实际调用Object构造函数==）

调用对象的属性时，可以通过**点语法**与**中括号语法**来调用

```js
  let person = {
    name: 'leo',
    "age": 19,
    5: 'number type',
    ["skills"]: 'fly in the sky'
  };
  console.log(person);   // {5: "number type", name: "leo", age: 19, skills: "fly in the sky"}
  console.log(person.name);  // leo 
  console.log(person.age);  // 19

  // 点语法 与 中括号语法
  console.log(person.skills);   // fly in the sky
  console.log(person["skills"]);  // fly in the sky

  // 这里的5会自动转化为String类型
  console.log(person[5]);  // number type
  console.log(person["5"]);  // number type
```

## Array

### from()和of()

> from()用于将类数组结构转换为数组实例

* from()方法的第一个参数是传入一个可迭代对象

```js
  /* 
    因为str的原型对象中含有Symbol(Symbol.iterator)
    属性，所以str是可迭代对象
  */
  let str = new String('leo');  
  console.log(str);    // String{"leo"}
  console.log(Array.from(str));  // ["l","e","o"]
```

```js
// Map和Set都属于类数组对象(或者说可迭代对象) 
let m = new Map()
    .set('name','leo')
    .set('age',20);

  let s = new Set()
    .add('great')
    .add('amazing')
    .add('excellent');

  console.log(Array.from(m));  // [["name", "leo"],["age", 20]]
  console.log(Array.from(s));  // ["great", "amazing", "excellent"]
```

* from()还接收第二个可选的映射函数参数。这个函数可以**直接增强新数组元素中的每一个值**，而无需像调用Array.from().map()那样先创建一个中间数组。
* 还可以接收第三个可选参数，用于指定映射函数中this的值。但是**这个重写的this值在箭头函数中不适用**

> of()用于将一组参数转换为数组实例

这个方法替代了在ES6之前常用的Array.prototype.slice.call(arguments)，一种笨拙的将arguments对象转换为数组的写法

```js
  console.log(Array.of(1,2,3,4));  // [1, 2, 3, 4]
  console.log(Array.of(undefined));  // [undefined]
```

### ** 指代幂

记录一个小的知识点，**是指几次幂

```js
  let x = 3;
  x = x ** 3;
  console.log(x);   // 27
```

### 检测数组

在只有一个网页（因而只有一个全局作用域）的情况下，使用instanceof操作符足矣；但是对于网页中存在多个框架，则可能涉及不同的全局执行上下文，因此就会有两个不同版本的Array构造函数，所以我们使用Array.isArray()方法

```js
  let obj = {};
  let res_1 = Array.isArray(obj);
  // 针对存在单个全局执行上下文
  let res_2 = obj instanceof Array; 
  console.log(res_1);  // false
  console.log(res_2);  // false
```

### 迭代器方法

ES6中，Array的原型上暴露了3个用于检索数组内容的方法：keys()、values()、entries()；

keys()：返回数组索引的迭代器

values()：返回数组元素的迭代器

entries()：返回索引/值对的迭代器

```js
  let arr = ['leo','sheng','kang','lei'];
  let aKeys = Array.from(arr.keys());
  let aValues = Array.from(arr.values());
  let aEntries = Array.from(arr.entries());

  console.log(aKeys);   // [0, 1, 2, 3]
  console.log(aValues);   // ["leo", "sheng", "kang", "lei"]
  console.log(aEntries);   //  [ [0, "leo"],[1, "sheng"],[2, "kang"],[3, "lei"]]
```

使用ES6解构非常容易的拆分键/值对

```js
  let arr = ['leo','sheng','kang','lei'];
  for(let [key_id,value_con] of arr.entries()) {
    console.log(key_id,value_con);
  }

  // 0 "leo"
  // 1 "sheng"
  // 2 "kang"
  // 3 "lei"
```

### 转换方法

```js
  let arr = ['leo','sheng','kang','lei'];

  console.log(typeof arr.toString());  // string
  console.log(typeof arr.valueOf());  // object
  console.log(typeof arr.toLocaleString());  // string

  console.log(arr.toString());       // leo,sheng,kang,lei
  console.log(arr.valueOf());        // ["leo", "sheng", "kang", "lei"]
  console.log(arr.toLocaleString()); // leo,sheng,kang,lei
  console.log(arr);                  // ["leo", "sheng", "kang", "lei"]
```

### 排序方法

reverse()：反转数组

sort()：排序数组

```js
  let arr = [11,23,77,34];
  console.log(arr.reverse());   // [34, 77, 23, 11]
  console.log(arr.sort((a,b)=>a-b));  // 升序 [11, 23, 34, 77]
  console.log(arr.sort((a,b)=>b-a));  // 降序 [77, 34, 23, 11] 
```

### 迭代方法

* every()
* filter()
* forEach()
* map()
* some()

> every() 与 some()

```js
  let arr = [1,2,3,4];
  // 遍历数组中每一个元素，如果都>2，则返回true，否则返回false
  let res = arr.every((value,index,array)=>{
    return value > 2;
  });
  console.log(res);   // false

  // 遍历数组中每一个元素，如果有至少一个>2，则返回true，否则返回false
  let back = arr.some((value,index,array)=>{
    return value > 2;
  });
  console.log(back);  // true
```

## Map

### 基本API

可以给Map构造函数传入一个可迭代对象

```js
  const m = new Map([
    ['key1','val_1'],
    ['key2','val_2'],
  ]);
  console.log(m.size);  // 2
```

对于基本属性与方法的使用：get()、has()、delete()、clear()

```js
  const m = new Map();
  m.set('name','leo')
    .set('age',20)
    .set('skill','front');
  
  console.log(m.has('name'));   // true
  console.log(m.get('age'));    // 20

  // 删除集合中元素
  m.delete('skill');

  console.log(m.size);  // 2 

  // 清除整个Map集合
  m.clear();

  console.log(m.size);  // 0
```

* ==与Object只能使用数组、字符串或符号作为键不同，Map可以使用任何JavaScript数据类型作为键==

```js
  const m = new Map();
  
  let boolKey = true;
  let objKey = {};
  let funcKey = function() {};

  m.set(boolKey,'bool_val')
   .set(objKey,'obj_val')
   .set(funcKey,'func_val');

  for(let key of m.keys()) {
    console.log(key);
  }
  // true
  // {}
  // f() {}

  for(let val of m.values()) {
    console.log(val);
  }
  // bool_val
  // obj_val
  // func_val
```

> 对Map集合中的同一键值key重复赋值

```js
  const m = new Map();
  m.set('name','leo')
    .set('age',20)
    .set('skill','front');
  
  console.log(m.size);

  m.set('name','new_leo');

  for(let val of m.values()) {
    console.log(val);
  }
  // new_leo
  // 20
  // front
```

结果：==对Map集合中的同一键值key重复赋值会覆盖之前的结果==

### Object与Map的比较

在JS中实现 "键值对" 式存储可以使用Object方便高效的完成，也就是使用对象的属性作为键，再使用属性来引用值。

作为ES6的新增特性，Map是一种新的集合类型，Map的大多数特性可以通过Object实现，但是二者还是存在一些细微的差异。

* 内存占用

在给定固定大小的内存，Map大约可以比Object多存储50%的键值对（==Map更好==）

* 插入性能

当代码涉及大量插入操作时，Map更优（==Map更好==）

* 查找速度

当代码涉及大量查找操作时，Object更优（==Object更好==）

* 删除性能

当代码涉及大量删除操作时，Map更优（==Map更好==）



## Set

Set在很多方面都像是加强的Map

### 基本API

创建对象的同时并初始化实例，可以通过给Set构造函数传入一个可迭代对象

```js
  const s = new Set(['leo','brose','jordan']);
  console.log(s.size);  // 3
```

add()：增加值

has()：查询

delete()：删除单个元素

clear()：清除整个集合元素

size：获取元素数量

```js
  const s = new Set(['leo','brose','jordan']);

  s.clear();

  console.log(s.size);  // 0

  s.add('kang')
   .add('sheng')
   .add(12);

  console.log(s.has('kang'));  // true 
  
  s.delete('sheng');

  console.log(s.size);  // 2

  s.add('kang');   // 此时集合中已经在这个值

  console.log(s.size);  // 2  表明Set集合不会存储添加相同的值
```

这里需要注意的是：==Set集合是不会存储重复的值==

### 顺序与迭代

Set会维护插入时顺序，因此支持按顺序迭代

```js
  const s = new Set(['leo','brose','jordan']);

  // 这里的迭代对象不论是s.values()还是s.keys(),返回结果相同
  for(let ele of s.values()) {
    console.log(ele);
  }
  // leo
  // brose
  // jordan

  for(let ent of s.entries()) {
    console.log(ent);
  }
  // ["leo", "leo"]  
  // ["brose", "brose"]
  // ["jordan", "jordan"]
```

## 迭代器方法与迭代方法

### 迭代器

<img src="..\JS_img\image-20201125152606923.png" alt="image-20201125152606923" style="zoom:67%;" />

<img src="..\JS_img\image-20201125153048804.png" alt="image-20201125153048804" style="zoom:67%;" />

#### 迭代器方法

使用者：迭代器（数组或者类数组对象<比如Set、Map集合>）

返回值：迭代器

### 迭代方法

使用者：主要是数组；Set与Map只可以使用forEach()

### Map与Set对于迭代方法的使用

通过查看Set与Map原型对象的属性可以知道，==Set与Map都只可以使用forEach()这一个迭代方法==

```js
  let s = new Set([12,23,11]);
  console.log("forEach" in s);  // true
  console.log("every" in s);  // false
  console.log("some" in s);  // false
  console.log("map" in s);  // false
  console.log("filter" in s);  // false

  let m = new Map();
  console.log("forEach" in m);  // true
  console.log("every" in m);  // false
  console.log("some" in m);  // false
  console.log("map" in m);  // false
  console.log("filter" in m);  // false
```

