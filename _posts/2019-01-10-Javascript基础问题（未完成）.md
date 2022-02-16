---
layout: mypost
title: Javascript基础问题（未完成）
categories: [JS]
---

## 1. 类型判断
- `undefined == null`

提到了TypeScript，建议阅读lodash的源代码。
```
(venv) ➜  ~ node
> undefined==null
true
> undefined===null
false
> parseInt(undefined)
NaN
> parseInt(null)
NaN
> null+1
1
> undefined+1
NaN
> parseFloat(null)
NaN
> Number("11")
11
> Number(null)
0
> Number(undefined)
NaN
> 
```
[阮一峰 --- undefined与null的区别](http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html)

**null表示"没有对象"，即该处不应该有值。**
1. 作为函数的参数，表示该函数的参数不是对象。
2. 作为对象原型链的终点。
```javascript
Object.getPrototypeOf(Object.prototype)
// null
```
**undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。**
1. 变量被声明了，但没有赋值时，就等于undefined。
2. 调用函数时，应该提供的参数没有提供，该参数等于undefined。
3. 对象没有赋值的属性，该属性的值为undefined。
4. 函数没有返回值时，默认返回undefined。
```javascript
var i;
i // undefined

function f(x){console.log(x)}
f() // undefined

var  o = new Object();
o.p // undefined

var x = f();
x // undefined
```

**!!! Array.isArray(jsons) typeOf(jsons)=="array" !!!**


## 2. 作用域
- **通过`var`定义的变量，它的作用域是在function或任何外部已经被声明的function，是全域的。**
- **通过`let`定义的变量，它的作用域是在一个块（block）。**
```javascript
function varvslet() {
  console.log(i); // i 是 undefined 的，因为变量提升
  // console.log(j); // ReferenceError: j 没有被定义

  for( var i = 0; i < 3; i++ ) {
    console.log(i); // 0, 1, 2
  };

  console.log(i); // 3
  // console.log(j); // ReferenceError: j 没有被定义

  for( let j = 0; j < 3; j++ ) {
    console.log(j);
  };

  console.log(i); // 3
  // console.log(j); // ReferenceError: j 没有被定义
}
```
### 详细的区别
-  变量提升

`let`不會被提升到整个块的作用域。相比之下，`var`可以被提升。
```javascript
{
  console.log(c); // undefined。因为变量提升
  var c = 2;
}

{
  console.log(b); // ReferenceError: b 没有被定义
  let b = 3;
}
```
-  循环中的闭包

`let`在每次循环可以重新被 bind，确保在它之前结束的循环被重新赋值，所以在闭包中它被用來避免一些问题。
```javascript
for (var i = 0; i < 5; ++i) {
  setTimeout(function () {
    console.log(i); // 输出 '5' 五次
  }, 100);  
}

// 使用 let 替换 var

// print 1, 2, 3, 4, 5
for (let i = 0; i < 5; ++i) {
  setTimeout(function () {
    console.log(i); // 输出 0, 1, 2, 3, 4
  }, 100);  
}
```

[转载自 http://www.jstips.co/zh_cn/javascript/keyword-var-vs-let/](http://www.jstips.co/zh_cn/javascript/keyword-var-vs-let/)

## 3. 引用传递
- js 中什么类型是引用传递, 什么类型是值传递?
- 如何将值类型的变量以引用的方式传递?

**不是按值传递也不是按址传递，而是call by sharing。**
```javascript
var a = 1;
function foo(x) {
    x = 2;
}
foo(a);
console.log(a); // 仍为1, 未受x = 2赋值所影响

var obj = {x : 1};
function foo(o) {
    o.x = 3;
}
foo(obj);
console.log(obj.x); // 3, 被修改了!

var obj = {x : 1};
function foo(o) {
    o = 100;
}
foo(obj);
console.log(obj.x); // 仍然是1, obj并未被修改为100.
```
可以这么理解：js中，只有一种规则：按值传递，基于值的复制。

也就是说，传的是基本类型，基本类型是`immutable`的, 那我复制一份，就是一个副本，两份数据互不影响。

但当我传的是一个对象，对象是`mutable`的，那我复制一份只是复制了其引用值，或者说是其指针（未查证），那么这样两个指针指向的是同一个对象，那我在对象内部操作，当然会影响到对象本身。但当我将引用值副本指向了别的变量，那原引用值指向的对象是不会变的。

##### Javascript基础类型：
`Undefined`、`Null`、`Boolean`、`Number`、`String`、`Symbol`
##### Javascript引用类型：
引用类型，统称为 Object 类型。细分的话，有：`Object`、`Array` 、`Date`、`RegExp`、`Function`等。

基础类型存在栈内存`(stack)`，引用类型存在堆内存`(heap)`

==， 两边值类型不同的时候，要先进行类型转换，再比较。 

===，不做类型转换，类型不同的一定不等。 

做类型转换时过于复杂，贴一些文档：

- [js 中 == 和 === 的区别](https://juejin.im/entry/584918612f301e005716add6)
- [从 []==![] 为 true 来剖析 JavaScript 各种蛋疼的类型转换](https://github.com/jawil/blog/issues/1)
- [通过一张简单的图，让你彻底地、永久地搞懂JS的==运算](http://www.admin10000.com/document/9242.html)

一些好玩的网站：
- [What the f*ck JavaScript?](https://github.com/denysdovhan/wtfjs)
- [Floating Point Math](http://0.30000000000000004.com)
- [JavaScript Puzzlers!](http://javascript-puzzlers.herokuapp.com/)

一些过程中发现的其他网站，收藏下：
- [栈内存和堆内存的一点小结](https://www.jianshu.com/p/39c6ecc1b184)
- [从一道面试题说起—js隐式转换踩坑合集](https://juejin.im/post/5bc5c752f265da0a9a399a62)
- [如何通过饿了么 Node.js 面试(解答)](https://github.com/xiaomuzhu/ElemeFE-node-interview)

## 4. 内存释放
#### 先看3个例子：
```javascript
let arr = [];
while(true)
  arr.push(1);
```
```javascript
let arr = [];
while(true)
  arr.push();
```
```javascript
let arr = [];
while(true)
  arr.push(new Buffer(1000));
```
以上三者有啥区别，哪种会爆内存哪种不会爆？

答：1、3都会爆内存

`区别很大，内存泄露的速度比直接push     Number类型的慢很多，因为ES定义的Number类型，实质上是遵循IEEE-2008的64位存储，也就是说，Number类型的1与buffer类型的1，完全不一样，前者在编译器中是00000000000000000000000000（63个0）1，占了64位；但是后者就只是1，只占了1位。当push buffer崩溃时，也会崩溃，只不过比Number类型的慢一些。运行起来后，占用的物理内存先增加，到了接近8G的时候开始占用虚拟内存，增加到12.2G左右会持续一段时间，差不多10秒中以后，进程崩溃`

`你的这段话有个很重要的点没有提到，node（v8引擎）有默认的内存限制，这是导致arr.push(1)为什么很快就报错停止的原因，而 Buffer 对象会调用c++底层API向操作系统申请内存，所以可以超过1g限制，操作系统会使用内存置换策略（就是把短期内不用内存和磁盘置换，具体算法看系统怎么使用）。`

来源：知乎
[如何爆掉V8的内存？](https://www.zhihu.com/question/68594568/answer/265200281)

PS: 只找到这么两条回答，但是并没有解释为啥速度差别这么大，按照我的理解，Number是4个byte，Buffer是1000个byte，只是存的地方不一样，前者是在heap，后者是在external，难道后者在arr数组中存的只是指针？

另外，2中不会爆内存，因为没有往arr中push东西，arr只有在push时才申请内存 **（这句话不知道对不对）** ，但是会产生`进程阻塞`。


#### 下一个问题是关于闭包的：
```javascript
function out() {
  const bigData = new Buffer(100);
  inner = function () {
    void bigData;
  }
}
```
闭包会引用到父级函数中的变量，如果闭包未释放，就会导致内存泄漏。上面例子是 inner 直接挂在了 root 上，从而导致内存泄漏（bigData 不会释放）。

详见：[如何分析 Node.js 中的内存泄漏](https://zhuanlan.zhihu.com/p/25736931)

关于闭包的：[让你分分钟理解 JavaScript 闭包](https://www.cnblogs.com/onepixel/p/5062456.html)

##  5. ES6 新特性
#### 1. `let`和`var`区别
1. let声明的变量拥有块级作用域
```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i);
    }, 500);
} // 5,5,5,5,5

for (let i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i);
    }, 500);
} // 0,1,2,3,4
```
2. let不存在变量提升
3. let存在暂时性死区（TDZ）
> ES6明确规定，如果区块内存在`let`和`const`命令，则这个区块对这些命令声明的变量从一开始就形成封闭作用域，只要在声明之前就使用这些变量，就会报错。
```javascript
if (true) {
    // TDZ 开始
    tmp = 'abc'; // ReferenceError
    console.log(tmp); // ReferenceError
    
    let tmp; // TDZ 结束
    console.log(tmp); //undefined
    
    tmp = 123;
    console.log(tmp); // 123
}
```
4. 不允许在相同作用域下重复声明
```javascript
function () {
    let a = 10;
    var a = 1; // 报错
}
```
来源：
- [var、let、const 区别？](https://www.jianshu.com/p/4e9cd99ecbf5)
- [《ECMAScript 6 入门》](http://es6.ruanyifeng.com/#docs/let)
#### 2.`箭头函数`与`function`的区别
箭头函数特性：
- 函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象。
- 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
- 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用`rest`参数代替（rest是个啥暂时不清楚）。
- 不可以使用`yield`命令，因此箭头函数不能用作`Generator`函数。

#### 3. `const`定义的`Array`中间元素能否被修改? 如果可以, 那 `const`修饰对象有什么意义?
- 可以改，const规定的只是栈内存上的变量名和值之间的指向不可变，但对于复合类型，比如数组，栈内存上的值只是一个堆内存上的地址，实际的数据存在堆内存上，那么当然可以被修改的。
> 如果真的想将对象冻结，应该使用Object.freeze方法。

#### 4. 闭包是什么？
- [学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
#### 5. `Class`和`Symbol`？
#### 6. `...`运算符？
