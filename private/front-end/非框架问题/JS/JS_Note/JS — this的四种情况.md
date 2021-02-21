# JS — this的四种情况

this的情况与每种情况对应的例子

​      1.以函数形式调用时，this永远都是window

```js
  function test() {
    console.log(this);   // window对象
  }
  test(); 
```

​      2.以方法的形式调用时，this是调用方法的对象

```js
let per = {
    name: 'leo',
    // 普通写法
    skill_1: function () {
      console.log(this);   // per对象
    },
    // 对象方法的增强写法（ES6)
    skill_2() {
      console.log(this);   // per对象
    },

  }
  per.skill_1();  
  per.skill_2();
```

​      3.以构造函数的形式调用时，this是新创建的那个对象

```js
  function Person() {
    console.log(this);   // per实例对象
  }

  let per = new Person();
```

​      4.使用call和apply调用时，this是指定的那个对象

```js
  function func() {
    console.log(this);  // {name: "new boy"}
  }

  func.call({name: 'new boy'});
```

