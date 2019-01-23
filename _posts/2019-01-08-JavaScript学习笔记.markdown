---
layout: post
title:  "JavaScript学习笔记"
date:   2019-01-08 10:26:24
categories: JavaScript
---
## 基本语法

### call apply bind区别
见 https://www.youtube.com/watch?v=c0mLRpw-9rI

`aMethod.call aMethod.apply aMethod.bind` ，这三者都是针对function的。

call 和 apply 比较类似，`aMethod.call(obj, param1, param2), aMethod.apply(obj,[param1, param2])`, 只是传参数的一点区别。obj是调用者，结果类似于obj.aMethod(param1, param2) .

bind会生成一个名为bound的function，供调用，这个fucntion中有targetFunction和targetObject属性。

如`bound = aMethod.bind(obj)`后，bound就是新生成的function，bound(param1, param2)用于实际执行。

所以我说这三者差别不大，obj是主人，aMethod是obj的干活的工具，params是工具的参数。

### JavaScript prototype
代码一：
```javascript
let = Dog = function () {};
Dog.prototype = { a: 'a', b: 'b' };
Dog.prototype.c = 'c'
let dog = new Dog;
dog.__proto__ === Dog.prototype// true
dog.a // a
dog.c // c
dog.prototype = {hi: 'hi'} // 这个没有什么意义，因为dog只是一个对象，不是function(或者说类)
```

代码二：
```javascript
function Dog() {}

let dog = new Dog;
dog.wang = "WangWangWang!"

Object.create2 = function (model) {
  let F = function (m) {};
  F.prototype = model;
  return new F(model);
}

let pup = Object.create2(dog); // 这个create2就基本上是ES6中Object.create(dog) 的实现了。
console.log(pup.wang); // OK,返回'WangWangWang!'

let pup2 = {};
pup2.prototype = dog; // 不行，不能直接让一个对象直接成为另一个对象的prototype。必须是function的prototype，function可以理解为类，其用于继承的属性在prototype中。或者用pup2.__prototype = dog. 或者Object.setPrototypeOf(pup2, dog)
console.log(pup2.wang) //返回undefined。
Object.setPrototypeOf(pup2, dog)
console.log(pup2.wang); // 旺出来了。这是正解。 
```
更多用法见：http://es6.ruanyifeng.com/#docs/object-methods#__proto__%E5%B1%9E%E6%80%A7

### 箭头函数
http://es6.ruanyifeng.com/#docs/function#%E4%B8%8D%E9%80%82%E7%94%A8%E5%9C%BA%E5%90%88

不适合场景：

1 定义函数的方法;  

2 动态的对象，比如当前的按钮。

箭头函数本身没有this,它用的this是指向函数定义生效时所在的对象的this(通常是外层对象). 普通函数有this，它指向当前对象的this.

比如： 
```javascript
(function () { return this.x }).bind({ x: 'inner' })() // 返回值是'inner'，但
(() => this.x ).bind({ x: 'inner' })() // 返回undefined。
```



