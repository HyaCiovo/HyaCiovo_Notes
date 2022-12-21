# 56JavaScript

[AMD、CMD、CommonJS、UMD、ESM（ JS模块化规范）](https://www.cnblogs.com/mingweiyard/p/13891510.html)

## 1、js基本数据类型有:

1.常用的基本数据类型包括undefined、null、number、boolean、string;

2.引用数据类型也就是对象类型,比如Object、array、function、data等。

比较两个变量时，对于基本数据类型，比较的就是值，
对于引用数据类型比较的是地址，地址相同才相同。





## 2、存储方式

1、栈（操作系统）：由操作系统自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。栈使用的是一级缓存， 他们通常都是被调用时处于存储空间中，调用完毕立即释放。
2、堆（操作系统）： 一般由程序员分配释放， 若程序员不释放，程序结束时可能由OS回收，分配方式倒是类似于链表。





## 3、**闭包**

好处

①保护函数内的变量安全 ，实现封装，防止变量流入其他环境发生命名冲突

②在内存中维持一个变量，可以做缓存（但使用多了同时也是一项缺点，消耗内存）

③匿名自执行函数可以减少内存消耗

坏处

①其中一点上面已经有体现了，就是被引用的私有变量不能被销毁，增大了内存消耗，造成内存泄漏，解决方法是可以在使用完变量后手动为它赋值为null；

②其次由于闭包涉及跨域访问，所以会导致性能损失，我们可以通过把跨作用域变量存储在局部变量中，然后直接访问局部变量，来减轻对执行速度的影响。





## 4、继承方法有哪些

### 1. 原型链继承

​	1.引用类型的属性被所有实例共享

​	2.在创建 Child 的实例时，不能向Parent传参

```js
function Parent () {
    this.names = ['kevin', 'daisy'];
}
function Child () {}

Child.prototype = new Parent();

var child1 = new Child();

child1.names.push('yayu');

console.log(child1.names); // ["kevin", "daisy", "yayu"]

var child2 = new Child();

console.log(child2.names); // ["kevin", "daisy", "yayu"]
```

### 2. 借用构造函数(经典继承)

​	优点：	1.避免了引用类型的属性被所有实例共享		2.可以在 Child 中向 Parent 传参

​	缺点：	方法都在构造函数中定义，每次创建实例都会创建一遍方法。

```js
function Parent (name) {
    this.name = name;
}

function Child (name) {
    Parent.call(this, name);
}

var child1 = new Child('kevin');

console.log(child1.name); // kevin

var child2 = new Child('daisy');

console.log(child2.name); // daisy
```

### 3. 组合继承

​	优点：融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式。

### 4. 原型式继承

​	ES5 Object.create 的模拟实现，将传入的对象作为创建的对象的原型

​	缺点：包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样。

### 5. 寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。

缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法。

### 6. 寄生组合式继承

​	组合继承最大的缺点是会调用两次父构造函数。

​	一次是设置子类型实例的原型的时候：

```javascript
Child.prototype = new Parent();
```

​	一次在创建子类型实例的时候：

```javascript
var child1 = new Child('kevin', '18');
```

那么我们该如何精益求精，避免这一次重复调用呢？

如果我们不使用 Child.prototype = new Parent() ，而是间接的让 Child.prototype 访问到 Parent.prototype 呢？

看看如何实现：

```js
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}

// 关键的三步
var F = function () {};

F.prototype = Parent.prototype;

Child.prototype = new F();


var child1 = new Child('kevin', '18');

console.log(child1);
```

最后我们封装一下这个继承方法：

```js
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

function prototype(child, parent) {
    var prototype = object(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}

// 当我们使用的时候：
prototype(Child, Parent);
```

​	只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。





## 5、原型链的理解

![image-20220317133333603](C:\Users\zhuji\AppData\Roaming\Typora\typora-user-images\image-20220317133333603.png)

​						图中由相互关联的原型组成的链状结构就是原型链，也就是蓝色的这条线。





## 6、事件循环

​        为了协调事件、用户交互、脚本、UI 渲染和网络处理等行为，用户引擎必须使用 event loops。Event Loop 包含两类：一类是基于 Browsing Context ，一种是基于 Worker ，二者是独立运行的。

​		所有的任务可以分为同步任务和异步任务，同步任务，顾名思义，就是立即执行的任务，同步任务一般会直接进入到主线程中执行；而异步任务，就是异步执行的任务，比如ajax网络请求，setTimeout 定时函数等都属于异步任务，异步任务会通过任务队列( Event Queue )的机制来进行协调。具体的可以用下面的图来大致说明一下：

![img](https://images2018.cnblogs.com/blog/698814/201809/698814-20180906144953689-838865376.jpg)

![image-20211213153058346](C:\Users\zhuji\AppData\Roaming\Typora\typora-user-images\image-20211213153058346.png)

​    **JavaScript 是一门单线程语言，异步操作都是放到事件循环队列里面，等待主执行栈来执行的，并没有专门的异步执行线程。**

![img](https://uploadfiles.nowcoder.com/images/20210723/717469988_1627034524037/9C70F30AB691F0F2DD51689F561C33A2)

- 微任务在DOM渲染前触发，宏任务在DOM渲染后触发

## 7、ES6新特性

1.Default Parameters（默认参数） in ES6

2.Template Literals（模板对象） in ES6

3.Multi-line Strings （多行字符串）in ES6

4.Destructuring Assignment （解构赋值）in ES6

5.Enhanced Object Literals （增强的对象字面量）in ES6

6.Arrow Functions in（箭头函数） ES6

8.Block-Scoped Constructs Let and Const（块作用域和构造let和const）

9.Classes（类）in ES6

10.Modules （模块）in ES6



## 8、call、apply、bind

const mom = {

```
name: '你妈',
buyGoods(){
   return `${this.name}去买${Array.prototype.join.call(arguments, '和')}`
}
```

}
mom.buyGoods('苹果', '香蕉', '梨子');
// "你妈去买苹果和香蕉和梨子"

const child = {

```
name: '你'
```

}
child.buyGoods('苹果', '香蕉', '梨子');
// Uncaught TypeError: child.buyGoods is not a function

mom.buyGoods.call(child, '苹果', '香蕉', '梨子');
// "你去买苹果和香蕉和梨子"

mom.buyGoods.apply(child, ['苹果', '香蕉', '梨子']);
// "你去买苹果和香蕉和梨子"

const callChildToBuyGoods = mom.buyGoods.bind(child, ['苹果', '香蕉', '梨子']);
// undefined
callChildToBuyGoods();
// "你去买苹果和香蕉和梨子"
callChildToBuyGoods();
// "你去买苹果和香蕉和梨子"

```js
猫吃鱼，狗吃肉，奥特曼打小怪兽。
有天狗想吃鱼了
猫.吃鱼.call(狗，鱼)
狗就吃到鱼了
猫成精了，想打怪兽
奥特曼.打小怪兽.call(猫，小怪兽)
猫也可以打小怪兽了
```



## 9、柯里化

curry 的这种用途可以理解为：参数复用。本质上是降低通用性，提高适用性。

### 1、好处

(1) 延迟计算

(2) 参数复用

(3) 动态生成函数

### 2、将普通函数柯里化的函数

```js
// 柯里化函数
const curry = function (fn) {
    return function nest(...args) {
        // fn.length表示函数的形参个数
        if (args.length === fn.length) {
            // 当参数接收的数量达到了函数fn的形参个数，即所有参数已经都接收完毕则进行最终的调用
            return fn(...args);
        } else {
            // 参数还未完全接收完毕，递归返回judge，将新的参数传入
            return function (arg) {
                return nest(...args, arg);
            }
        }
    }
}
```

```js
function addNum(a, b, c) {
    return a + b + c;
}

const addCurry = curry(addNum);

addCurry(1)(2)(3);// 6
```

## 10、Promise

[Promise - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Promise 必须为以下三种状态之一：等待态（Pending）、执行态（Fulfilled）和拒绝态（Rejected）。一旦Promise 被 resolve 或 reject，不能再迁移至其他任何状态（即状态 immutable）。

基本过程：

1. 初始化 Promise 状态（pending）
2. 立即执行 Promise 中传入的 fn 函数，将Promise 内部 resolve、reject 函数作为参数传递给 fn ，按事件机制时机处理
3. 执行 then(..) 注册回调处理数组（then 方法可被同一个 promise 调用多次）
4. Promise里的关键是要保证，then方法传入的参数 onFulfilled 和 onRejected，必须在then方法被调用的那一轮事件循环之后的新执行栈中执行。

**真正的链式Promise是指在当前promise达到fulfilled状态后，即开始进行下一个promise.**

































 
