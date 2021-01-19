# JS — 对象、类与面向对象编程

## 对象

### 理解对象

> 定义：==对象是一组无序属性的集合。==对象的每个属性或者方法都通过一个名称来标识，这个名称会映射到一个值
>
> 也可以将对象理解成一张散列表，其中的内容就是一组名/值对，值可以是数据或者函数

#### 属性的类型(Object.defineProperty)

属性分两种：数据属性与访问器属性

> 数据属性

数据属性有以下四个特性描述它们的行为

1. [[Configurable]]：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。直接在对象上定义的属性，它们的这个特性默认值为true

2. [[Enumerable]]：表示能否通过for-in循环返回属性，直接在对象上定义的属性，它们的这个特性默认值为true

3. [[Writable]]：表示能否修改属性的值，直接在对象上定义的属性，它们的这个特性默认值为true

4. [[Value]]：包含这个属性的数据值。读取属性值的时候，从这个位置读取，写入值得时候，把新值保存在这个位置。这个特性得默认值为undefined

具体使用：

```javascript
let obj = {
    name: 'leo'
};
Object.defineProperty(obj,'name',{
    writable: false,
    value: '我是默认值，不可修改'
});
obj.name = 'new leo';  // 修改属性name
console.log(obj.name);  // 我是默认值，不可修改
```

==注意：==

当一个属性被定义为不可配置之后，就不能再变回可配置了。再次调用Object.defineProperty( ) 并修改非 writable 的属性会报错

```js
let obj = {
    name: 'leo'
};
Object.defineProperty(obj,'name',{
    configurable: false,
    value: 'default value'
});
console.log(obj);
// 再次调用 报错: Uncaught TypeError
Object.defineProperty(obj,'name',{
    configurable: true,
    value: 'default value'
})
```

> 访问器属性

1. [[Configurable]]：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。直接在对象上定义的属性，这个特性的默认值为true

2. [[Enumerable]]：表示能否通过for-in循环返回属性。直接在对象上定义的属性，这个特性的默认值为true

3. [[Get]]：在读取属性时调用的函数。默认值为undefined

4. [[Set]]：在写入属性时调用的函数。默认值为undefined

```javascript
let obj = {
    // 伪私有成员year_
    year_: 2017,
    edition: 1
};
// year 为访问器属性
Object.defineProperty(obj,'year',{
    get() {
        return this.year_;
    },
    set(newValue) {
        if(newValue > 2017){
            this.year_ = newValue;
            this.edition += newValue - 2017;
        }
    }
});
// 设置访问器属性
obj.year = 2019;
console.log(obj.year_);  // 2019
console.log(obj.edition);  // 3
```

#### 合并对象(Object.assign)

通过 Object.assign(目标对象, 源对象<可以是多个>) 合并对象

* 覆盖属性
* 对象引用

```js
let dest, src, res;
dest = {
    id: 'dest'
};

src = {
    id: 'src2',
    b: 'bar'
};

res = Object.assign(dest, {
    id: 'src1',
    a: 'foo'
}, src);

// 覆盖属性
console.log(res);  // { a: "foo", b: "bar", id: "src2"}
// 返回目标对象
console.log(res === dest);   // true
// 复制对象的引用
console.log(dest.b === src.b);   // true
```

* 不能在两个对象间转移获取函数与设置函数

```javascript
let dest, src;
dest = {
    set a(val) {
        console.log(`Invoke dest setter with param ${val}`);
    }
}
src = {
    get a() {
        console.log('Invoke src getter');
        return 'foo';
    }
}
Object.assign(dest,src);
console.log(dest); // {set a(val) {...}}
```

#### 相等判断(Object.is)

```js
console.log(Object.is(1, true));  // false
console.log(Object.is({}, {}));   // false
console.log(Object.is(2, '2'));   // false

console.log(Object.is(-0, +0));   // false
console.log(Object.is(0, +0));    // true
console.log(Object.is(0, -0));    // false

console.log(Object.is(NaN, NaN)); // true
```

#### 增强的对象语法

1. 属性值的简写
2. 可计算属性

```js
let nameKey = 'name';
let ageKey = 'age';
let sayNameKey = 'sayName';

let uniToken = 0;
function getKey(key) {
    return `${key}_${++uniToken}`;
}

let person = {
    [getKey(nameKey)]: 'leo',
    [ageKey]: 19,
    [sayNameKey]() {
        // 正确打印
        // console.log(this['name_1']);
        /* 
        此时getKey(nameKey)的返回值为name_2,
        所以相当于调用的是person对象的name_2属性
      */
        console.log(this[getKey(nameKey)]);  
    }
}

person[sayNameKey]();  // undefined
console.log(person[ageKey]);  // 19
console.log(person['name_1']);  // leo
```

3. 方法名简写



### 创建对象

#### 工厂模式

```js
// 工厂模式
function createPerson(name,age) {
    let o = new Object();
    o.name = name;
    o.age = age;
    o.sayName = function() {
        console.log(this.name);
    }
    return o;
}

let person1 = createPerson('leo',19);
let person2 = createPerson('kang',20);

console.log(person1);  // {name: "leo", age: 19, sayName: ƒ}
console.log(person2);  // {name: "kang", age: 20, sayName: ƒ}
person1.sayName();   // leo
```

虽然解决了创建多个类似对象的问题，但是没有解决对象标识问题（即新创建的对象是什么类型）

#### 构造函数模式

```js
// 构造函数模式
function Person(name,age) {
    this.name = name;
    this.age = age;
    this.sayName = function() {
        console.log(this.name);
    }
}
// 通过new构造函数创建实例对象
let person1 = new Person('Sheng',21);
let person2 = new Person('Sao',22);

console.log(person1);  // Person {name: "Sheng", age: 21, sayName: ƒ}
console.log(person2);  // Person {name: "Sao", age: 22, sayName: ƒ}
person1.sayName();  // Sheng
```

> 构造函数也是函数

```js
function Person(name,age) {
    this.name = name;
    this.age = age;
    this.sayName = function() {
        console.log(this.name);
    }
}
// 直接调用Person函数
/*
   此时调用Person函数，实际上是在
   调用window对象的Person方法，
   所以里面的this指代的是window对象
*/
Person('mrkleo',66);
console.log(window.name); // mrkleo
console.log(window.age);  // 66
window.sayName();  // mrkleo
```

> 构造函数的问题

```js
function Person(name,age) {
    this.name = name;
    this.age = age;
    // 逻辑等价于上面的方式
    this.sayName = new Function("console.log(this.name)");
}
```

所以每次实例化对象都会创建一次sayName方法，但是

```js
console.log(person1.sayName == person2.sayName);  // true
```

所以没必要在每次创建实例对象时，都创建一次sayName

#### 原型模式

上面两种方式都存在一定的问题，所以采用原型模式

> 理解原型

![image-20210118172002024](..\JS_img\image-20210118172002024.png)

图中的[[Prototype]]就是每个对象上暴露的`__proto__`属性

==实例与构造函数原型之间有直接联系，但实例与构造函数之间没有==



> 为什么最后一行代码是true?

```js
  let obj = {};
  console.log(obj.__proto__);
  console.log(Object.prototype);
  console.log(Object.prototype === obj.__proto__);  // true
```

因为：

![图片来源于王福明博客](..\JS_img\70)

由于==obj对象本质上是由Object函数创建的==，所以实例对象obj的`__proto__`属性与Object函数的prototype属性都指向Object的原型对象



> 1. ==正常的原型链都会终止于Object的原型对象==
> 2. ==Object原型的原型对象是null==

![这里写图片描述](..\JS_img\71)



> 函数也是一种对象，函数也有`__proto__`吗?

![这里写图片描述](..\JS_img\72)

Function也是一个函数，函数是一种对象，也有`__proto__`属性。既然是函数，那么它一定是被Function创建。所以Function是被自身创建的，所以它的隐式proto属性指向了自身的原型对象

==函数的隐式proto属性都指向于Function.prototype对象==



> `Function.prototype`指向的对象，它的`__proto__`是不是也指向`Object.prototype`？

答案是of course！因为Function.prototype指向的原型对象也是一个被Object创建的普通对象，故of course！

![这里写图片描述](..\JS_img\73)



> 总结上述内容

![这里写图片描述](..\JS_img\74)

#### 原型的动态性

```js
  function Person() {};

  let friend = new Person();
  Person.prototype.sayHi = function() {
    console.log('Hello Man');
  };

  friend.sayHi();  // Hello Man
```

![image-20210119100725986](..\JS_img\image-20210119100725986.png)

此时未重写原型对象，可以通过实例访问到原型中的方法，请分析下面的代码为何报错？

```js
  function Person() {};

  let friend = new Person();
  Person.prototype = {
    constructor: Person,
    name: 'leo',
    age: 20,
    sayName() {
      console.log(this.name);
    }
  }

  friend.sayName();  // 报错
```

通过模型图来解释更为直观

![image-20210119100825528](..\JS_img\image-20210119100825528.png)

通过图我们可以看到当Person的原型对象被重写时，==实例的`__proto__`属性任然是指向原来的原型对象，因为实例的`__proto__`指针是在调用构造函数时自动赋值的==，所以此时通过实例friend调用不到重写原型对象中的sayName方法

### 继承

很多面向对象的语言都支持两种继承：接口继承和实现继承，但是在ES中==实现继承是唯一的支持方式==，而这主要是通过原型链来实现的

#### 原型链

为了理解什么是原型链，定义如下代码：

```js
  function Father() {
    this.fatherage = 45;
  };
  Father.prototype.getFatherAge = function() {
    return this.fatherage;
  }
  function Son() {
    this.sonage = 20;
  }

  // 继承 Father
  /* 
    Son.prototype对象成为了Father的实例
  */
  Son.prototype = new Father();

  Son.prototype.getSonAge = function() {
    return this.sonage;
  }

  let son_instance = new Son();
  console.log(son_instance.getFatherAge()); // 45
  console.log(Son.prototype.constructor);  // Father()构造函数
  console.log(son_instance.__proto__.__proto__.__proto__);  // Object.Prototype
  console.log(son_instance.__proto__.__proto__.__proto__.constructor);  // Object()构造函数
```

我们可以通过代码发现，我构建的son_instance是Son的实例，但是我却可以通过son_instance调用Father原型对象中的方法，这究竟是如何实现的呢？以及后面几行代码为何打印出这些?

看懂这张图，你就能理解上面的问题：

![image-20210119204509948](..\JS_img\image-20210119204509948.png)



