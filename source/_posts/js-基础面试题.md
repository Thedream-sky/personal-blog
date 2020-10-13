---
title: js 基础面试题
date: 2019-04-18 22:34:41
categories: 前端
tags: [js,面试] 
---


# Array 有多少种常用方法
## 不改变array的方法
### indexOf() 和lastIndexOf()
1) indexof() : 返回元素在数组的第一次出现的索引，从0开始。若数组不存在该元素，则返回-1。
```
var arr = [1, 2, 2];
arr.indexOf(1); //0
arr.indexOf(10); //-1
```
2) lastIndexOf(): 返回元素在数组最后一次出现的索引，如果没有出现则返回-1.
```
var arr = [1, 2, 2];
arr.lastIndexOf(2); //2
arr.lastIndexOf(10); //-1
```
<!-- more -->
### slice()方法
与字符串的substring()方法一样，截取数组的一部分，返回一个新的数组。
1) slice(start)索引从start开始截取
```
var arr = [1, 2, 2, 5, 6];
arr.slice(2) // [2, 5, 6]
```
2) slice(start,end)索引从start开始到索引end结束。通常，接受2个参数作为一个左闭右开区间，即包括开始索引位置的元素，但不包括结束索引位置的元素。
```
var arr = [1, 2, 2, 5, 6];
arr.slice(1,3) // [2, 2]
```
3) slice()没有参数，则是复制整个数组。
```
var arr = [1, 2, 2, 5, 6];
arr.slice();
```
### concat()：合并数组。
把当前的数组和另一个数组连接起来，并返回一个新的数组。
1) 方法的参数可以有多个，也可以任意任意类型，数值、字符串、布尔值、数组、对象 都可以，参数会被被添加到新的数组中。
```
var arr1 =  [1, 2, 3,4,5,6];
var arr2 = ['a','b','c'];
var arr3 = arr1.concat(arr2);
arr3;   //[1, 2, 3, 4, 5, 6, "a", "b", "c"]
```
2) 注意，如果参数是数组, 会被拉平一次，即数组会被拆开来，加入到新的数组中。具体看示例：

```
var arr1 = [1, 2, 3];
var arr2 = arr1.concat(66,'abc',true,[10,20],[30,[31,32]],{x:100});
arr2;  //[1, 2, 3, 66, "abc", true, 10, 20, 30, [31,32], {x:100}]
```
### join(): 转成字符串。
它会把当前Array的每个元素都用指定的字符串连接起来，然后返回连接后的字符串。
1)  参数是用来指定连接的字符串。见示例代码：
```
var arr = [1, 2, 3];
arr.join('*')   //"1*2*3"
```
2) 如果没有指定参数，默认是用 "," 连接。
```
var arr = [1, 2, 3];
arr.join()   //"1,2,3"
```
### toString(): 返回数组的字符串形式
```
var arr = [1, 2, 3];
arr.toString() // "1,2,3"
```
### valueOf():返回数组本身
```
var arr = [1, 2, 3];
arr.valueOf() // [1, 2, 3]
```
### map():
1) 对数组的所有成员依次调用一个函数，返回值是一个新数组。
```
arr.map(function(elem, index, arr) {
    return elem * index;
}); 
//[0, 2, 6]
```
2)  map方法接受一个函数作为参数,该函数调用时，map方法会将其传入3个参数，分别是当前成员、当前位置和数组本身(后2个参数可选)。
```
arr.map(function(elem, index, arr) {
    return elem * index;
}); 
//[0, 2, 6]
```
3) map方法还可以接受第2个参数，表示回调函数执行时this所指向的对象。
### forEach(): 
与map方法很相似，也是遍历数组的所有成员，执行某种操作。注意：forEach方法一般没有返回值
```
var arr = [1, 2, 3];
function log(element, index, array) {
    console.log('[' + index + '] = ' + element);
}
arr.forEach(log);
// [0] = 1
// [1] = 2
// [2] = 3
```
### filter(): 删选
```
var arr = [1, 2, 3, 4, 5];
arr.filter(function (elem, index, arr) {
  return index % 2 === 1;
});
//[2, 4]
```
### some()和every()
类似“断言”（assert），用来判断数组成员是否符合某种条件。

1) 接受一个函数作为参数，所有数组成员依次执行该函数，返回一个布尔值。该函数接受三个参数，依次是当前位置的成员、当前位置的序号和整个数组。
2) some方法是只要有一个数组成员的返回值是true，则整个some方法的返回值就是true，否则false。
```
var arr = [1, 2, 3, 4];
arr.some(function (elem, index, arr) {
  return elem >= 3;
});
// true
```
3) every方法则是所有数组成员的返回值都是true，才返回true，否则false。
```
var arr = [1, 2, 3, 4];
arr.every(function (elem, index, arr) {
  return elem >= 3;
});
// false
```
4) 注意，对于空数组，some方法返回false，every方法返回true
### reduce()和reduceRight(): 
依次处理数组的每个成员，最终累计为一个值。
1) reduce是从左到右处理（从第一个成员到最后一个成员）
```
arr = [1, 2, 3]
arr.reduce(function(x, y){
  console.log(x, y)
  return x + y;
});
// 1 2
// 3 3
// 6
```
2) reduceRight则是从右到左处理（从最后一个成员到第一个成员）
```
rr.reduceRight(function(x, y){
  console.log(x, y)
  return x + y;
});
// 3 2
// 5 1
// 6
```
## 改变原数组的方法
### push():
向数组的末尾添加若干元素。返回值是改变后的数组长度。
```
var arr = [1, 2];
arr.push(3) ;// 3
arr; //  [1, 2, 3]
arr.push('b','c'); //5
arr; //[1, 2, 3, "b", "c"]
arr.push([10,20]); //6
arr; //[1, 2, 3, "b", "c", [10,20]]
```
### pop()
删除数组最后一个元素。返回值是删除的元素。
```
var arr =[1, 2, 3, "b", "c", [10,20]];
arr.pop(); //[10, 20]
arr;  // [1, 2, 3, "b", "c"]
```
### unshift()
向数组头部添加若干元素。返回值是改变后的数组长度。
```
var arr = [1, 2];
arr.unshift(3,4 );  //4
arr;  // [3, 4, 1, 2]
```
### shift()
删除数组第一个元素。返回值是删除的元素
```
var arr = ['a', 'b', 1, 2];
arr.shift(); //'a'
arr;  //['b', 1, 2]
```
### sort()
 数组排序。
1) 默认是将所有元素转换成字符串，再按字符串Unicode码点排序。返回值是新的数组。
```
var arr = [1, 2, 12, 'a', 'b', 'ab', 'A', 'B']
arr.sort();  //[1, 12, 2, "A", "B", "a", "ab", "b"] 注意：12排在了2的前面
```
2) 如果元素都是数字，要按从小到大排序，可以传入一个回调函数作为参数。
```
var arr = [1, 2, 12, 100]

arr.sort(function(a,b){
    return a-b;
});
// [1, 2, 12, 100]
```
### reverse(): 
颠倒数组中元素的位置
```
var arr = [1, 2, 12, 'a', 'b', 'ab', 'A', 'B'];
arr.reverse();
//["B", "A", "ab", "b", "a", 12, 2, 1]
```
### array.splice()
array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
* 参数
start 为开始的索引，deletecount 表示要移除的数组元素的个数。item 为要添加进数组的元素

如果 deleteCount 大于 start 之后的元素的总数，则从 start 后面的元素都将被删除（含第 start 位）。
如果 deleteCount 被省略了，或者它的值大于等于array.length - start(也就是说，如果它大于或者等于start之后的所有元素的数量)，那么start之后数组的所有元素都会被删除。
如果 deleteCount 是 0 或者负数，则不移除元素。这种情况下，至少应添加一个新元素
1) 只删除,不添加。可以传入2个参数：
```
var arr = ['Alibaba', 'Tencent', 'Baidu', 'XiaoMi', '360'];

// 从索引2开始删除3个元素
arr.splice(2, 3); // 返回删除的元素 ['Baidu', 'XiaoMi', '360']
arr; // ['Alibaba', 'Tencent']
```
2) 只添加,不删除。第2个参数设为0，即不删除元素。
```
arr.splice(2, 0, 'Toutiao', 'Meituan', 'Didi'); // 返回[],因为没有删除任何元素
arr; //["Alibaba", "Tencent", "Toutiao", "Meituan", "Didi"]
```
3)  先删除若干元素，然后在删除的位置上在添加若干个元素。
```
var  arr =["Alibaba", "Tencent", "Toutiao", "Meituan", "Didi"]
arr.splice(2,2,'Apple','Google');  //["Toutiao", "Meituan"]
arr; //["Alibaba", "Tencent", "Apple", "Google", "Didi"]
```
# 作用域与执行上下文的区别

1) 作用域分全局作用域和函数作用域，由于js没有块级作用域（es6里规定了块级作用域，详情可自行查看），函数作用域可以用于隔离变量，不同作用域下同名变量不会有冲突的。作用域只是一个“地盘”，作用域是一个抽象的概念，其中没有变量。要通过作用域对应的执行上下文环境来获取变量的值。作用域中变量的值是在执行过程中产生的确定的，而作用域却是在函数创建时就确定了。
2) 执行全局代码时，会产生一个执行上下文环境，每次调用函数都又会产生执行上下文环境。当函数调用完成时，这个上下文环境以及其中的数据都会被消除（当然了闭包并不会乖乖就范），处于活动状态的执行上下文环境只有一个。
参考推荐： http://www.cnblogs.com/wangfupeng1988/p/3977924.html


# this指向
参考：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this（MDN）
函数的调用方式决定了this的值。记住调用方式决定了this的值。this不能在执行期间被赋值，并且在每次函数被调用时this的值也可能会不同。

## this 的定义：当前执行代码的环境对象
## this 值的几种区分情况：
### 全局环境
在非严格模式下，在全局执行环境中（在任何函数体外部）this 都指向全局对象，严格模式this全局默认指向undefined。
```
// 在浏览器中, window 对象同时也是全局对象：
console.log(this === window); // true

a = 37;
console.log(window.a); // 37

this.b = "MDN";
console.log(window.b)  // "MDN"
console.log(b)         // "MDN"
```
### 函数（运行内）环境
#### 简单调用：
* 非严格模式下，this默认指向全局对象

```
function f1(){
  return this;
}
//在浏览器中：
f1() === window;   //在浏览器中，全局对象是window

//在Node中：
f1() === global;
```
 
* 严格模式下，this将会默认为undefined。

```
function f2(){
  "use strict"; // 这里是严格模式
  return this;
}

f2() === undefined; // true
```


  
#### call/apply方法调用，改变this的指向
call和apply 都可以传递参数，call 是传递多个参数，而apply则传数组传递参数。
```
// 将一个对象作为call和apply的第一个参数，this会被绑定到这个对象。
var obj = {a: 'Custom'};

// 这个属性是在global对象定义的。
var a = 'Global';

function whatsThis(arg) {
  return this.a;  // this的值取决于函数的调用方式
}

whatsThis();          // 'Global'
whatsThis.call(obj);  // 'Custom'
whatsThis.apply(obj); // 'Custom'
```
#### bind方法，改变this的指向
ECMAScript 5 引入了 Function.prototype.bind，调用f.bind(someObject)会创建一个与f具有相同函数体和作用域的函数，但是在这个新函数中，this将永久地被绑定到了bind的第一个参数（注意永久绑定到第一个参数上，也就是不管绑定多少次bind，都是指向第一个参数），无论这个函数是如何被调用的。
```
function f(){
  return this.a;
}

var g = f.bind({a:"azerty"});
console.log(g()); // azerty

var h = g.bind({a:'yoo'}); // bind只生效一次！
console.log(h()); // azerty

var o = {a:37, f:f, g:g, h:h};
console.log(o.f(), o.g(), o.h()); // 37, azerty, azerty
```
#### 箭头函数，改变this指向
在箭头函数中，this与封闭词法环境的this保持一致。在全局代码中，它将被设置为全局对象：

```
var globalObject = this;
var foo = (() => this);
console.log(foo() === globalObject); // true
```
重难点：
```
// 创建一个含有bar方法的obj对象，
// bar返回一个函数，
// 这个函数返回this，
// 这个返回的函数是以箭头函数创建的，
// 所以它的this被永久绑定到了它外层函数的this。
// bar的值可以在调用中设置，这反过来又设置了返回函数的值。
var obj = {
  bar: function() {
    var x = (() => this);
    return x;
  }
};

// 作为obj对象的一个方法来调用bar，把它的this绑定到obj。
// 将返回的函数的引用赋值给fn。
var fn = obj.bar();

// 直接调用fn而不设置this，
// 通常(即不使用箭头函数的情况)默认为全局对象
// 若在严格模式则为undefined
console.log(fn() === obj); // true

// 但是注意，如果你只是引用obj的方法，
// 而没有调用它
var fn2 = obj.bar;
// 那么调用箭头函数后，this指向window，因为它从 bar 继承了this。
console.log(fn2()() == window); // true
```
特别注意的是，当方法是对象里的属性时，如果调用的不是方法 obj.bar()这种形式，而是obj.bar这种形式，后面再去调用的时候，前者的this指向当前对象obj,而后者指向全局对象。根本原因是由于执行的上下文不一样导致的，希望细细品味。
#### 作为对象的方法
当函数作为对象里的方法被调用时，它们的 this 是调用该函数的对象。
```
var o = {
  prop: 37,
  f: function() {
    return this.prop;
  }
};

console.log(o.f()); // logs 37
```
请注意，这样的行为，根本不受函数定义方式或位置的影响。
```
var o = {prop: 37};

function independent() {
  return this.prop;
}

o.f = independent;

console.log(o.f()); // logs 37
```
同样，this 的绑定只受最靠近的成员引用的影响。在下面的这个例子中，我们把一个方法g当作对象o.b的函数调用。在这次执行期间，函数中的this将指向o.b。事实证明，这与他是对象 o 的成员没有多大关系，最靠近的引用才是最重要的。
```
o.b = {g: independent, prop: 42};
console.log(o.b.g()); // 42
```
即this指向最后一个调用方法的对象

#### 原型链中的 this
对于在对象原型链上某处定义的方法，同样的概念也适用。如果该方法存在于一个对象的原型链上，那么this指向的是调用这个方法的对象，就像该方法在对象上一样。
```
var o = {
  f: function() { 
    return this.a + this.b; 
  }
};
var p = Object.create(o);
p.a = 1;
p.b = 4;

console.log(p.f()); // 5
```
此处p继承自o,但是调用f()方法的是p,则this指向p
#### getter 与 setter 中的 this
再次，相同的概念也适用于当函数在一个 getter 或者 setter 中被调用。用作 getter 或 setter 的函数都会把 this 绑定到设置或获取属性的对象。
```
function sum() {
  return this.a + this.b + this.c;
}

var o = {
  a: 1,
  b: 2,
  c: 3,
  get average() {
    return (this.a + this.b + this.c) / 3;
  }
};

Object.defineProperty(o, 'sum', {
    get: sum, enumerable: true, configurable: true});

console.log(o.average, o.sum); // logs 2, 6
```
#### 作为构造函数
当一个函数用作构造函数时（使用new关键字），它的this被绑定到正在构造的新对象。

```
/*
 * 构造函数这样工作:
 *
 * function MyConstructor(){
 *   // 函数实体写在这里
 *   // 根据需要在this上创建属性，然后赋值给它们，比如：
 *   this.fum = "nom";
 *   // 等等...
 *
 *   // 如果函数具有返回对象的return语句，
 *   // 则该对象将是 new 表达式的结果。 
 *   // 否则，表达式的结果是当前绑定到 this 的对象。
 *   //（即通常看到的常见情况）。
 * }
 */

function C(){
  this.a = 37;
}

var o = new C();
console.log(o.a); // logs 37


function C2(){
  this.a = 37;
  return {a:38};
}

o = new C2();
console.log(o.a); // logs 38
```
虽然构造器返回的默认值是this所指的那个对象，但它仍可以手动返回其他的对象（如果返回值不是一个对象，则返回this对象）。
# 浅拷贝与深拷贝
对象类型在赋值的过程中其实是复制了地址，从而会导致改变了一方其他也都被改变的情况。通常在开发中我们不希望出现这样的问题，我们可以使用浅拷贝来解决这个情况。
```
let a = {
  age: 1
}
let b = a
a.age = 2
console.log(b.age) // 2
```
## 浅拷贝
### Object.assign实现浅拷贝
很多人认为这个函数是用来深拷贝的。其实并不是，Object.assign 只会拷贝所有的属性值到新的对象中，如果属性值是对象的话，拷贝的是地址，所以并不是深拷贝。

```angular2
let a = {
  age: 1
}
let b = Object.assign({}, a)
a.age = 2
console.log(b.age) // 1

```

### ...扩展运算符来实现浅拷贝
如下例：
```
let a = {
  age: 1
}
let b = { ...a }
a.age = 2
console.log(b.age) // 1
```

通常浅拷贝就能解决大部分问题了，但是当我们遇到如下情况就可能需要使用到深拷贝了

```angular2
let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = { ...a }
a.jobs.first = 'native'
console.log(b.jobs.first) // native
```  
        
浅拷贝只解决了第一层的问题，如果接下去的值中还有对象的话，那么就又回到最开始的话题了，两者享有相同的地址。要解决这个问题，我们就得使用深拷贝了。

## 深拷贝
这个问题通常可以通过 JSON.parse(JSON.stringify(object)) 来解决。

```angular2
let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = JSON.parse(JSON.stringify(a))
a.jobs.first = 'native'
console.log(b.jobs.first) // FE
```

但是该方法也是有局限性的：
* 会忽略 undefined
* 会忽略 symbol
* 不能序列化函数
* 不能解决循环引用的对象

```
let obj = {
  a: 1,
  b: {
    c: 2,
    d: 3,
  },
}
obj.c = obj.b
obj.e = obj.a
obj.b.c = obj.c
obj.b.d = obj.b
obj.b.e = obj.b.c
let newObj = JSON.parse(JSON.stringify(obj))
console.log(newObj)
```
如果你有这么一个循环引用对象，你会发现并不能通过该方法实现深拷贝（）
![image.png](https://upload-images.jianshu.io/upload_images/17345926-4b58879a8138aeb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在遇到函数、 undefined 或者 symbol 的时候，该对象也不能正常的序列化
```
let a = {
  age: undefined,
  sex: Symbol('male'),
  jobs: function() {},
  name: 'yck'
}
let b = JSON.parse(JSON.stringify(a))
console.log(b) // {name: "yck"}
```
上述情况中，该方法会忽略掉函数和 undefined 。
深度拷贝的实现方法：
* 手写简单的深度拷贝

```
function deepClone(obj) {
  function isObject(o) {
    return (typeof o === 'object' || typeof o === 'function') && o !== null
  }

  if (!isObject(obj)) {
    throw new Error('非对象')
  }

  let isArray = Array.isArray(obj)
  let newObj = isArray ? [...obj] : { ...obj }
  Reflect.ownKeys(newObj).forEach(key => {
    newObj[key] = isObject(obj[key]) ? deepClone(obj[key]) : obj[key]
  })

  return newObj
}

let obj = {
  a: [1, 2, 3],
  b: {
    c: 2,
    d: 3
  }
}
let newObj = deepClone(obj)
newObj.b.c = 1
console.log(obj.b.c) // 2
```
* 使用Lodash库的clone 与 cloneDeep方法。

# find与filter的区别
* find：（但只是返回）符合条件的第一项（单个元素）。当找到符合回调函数过滤条件的第一个元素时，它会立即停止往下的搜寻。不再遍历整个数组。
* filter： 返回的是数组，返回所有符合要求的数组，会遍历整个数组。
# js 作用域
* 作用域是指程序源代码中定义变量的区域。
* 作用域规定了如何查找变量，也就是确定当前执行代码对变量的访问权限。
* JavaScript 采用词法作用域(lexical scoping)，也就是静态作用域。
## 静态作用域与动态作用域
* 因为 JavaScript 采用的是词法作用域，函数的作用域在函数定义的时候就决定了。
* 而与词法作用域相对的是动态作用域，函数的作用域是在函数调用的时候才决定的。

```
var value = 1;

function foo() {
    console.log(value);
}

function bar() {
    var value = 2;
    foo();
}

bar();  // 1
```

结果是1, 可能有人就会问了，为啥不是2啊，怎么是1.那是因为javascript采用的词法作用域。则执行这个过程是这样的：
执行 foo 函数，先从 foo 函数内部查找是否有局部变量 value，如果没有，就根据书写的位置，查找上面一层的代码，也就是 value 等于 1，所以结果会打印 1。如果是动态作用域，那就是2了。
查找顺序：1、就近查找，同一作用域找不到，往上一层找。2、就根据书写的位置，查找上面一层的代码。

再看个例子：

```
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();
```

```
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```

结果都是'local scope',遵循上面的查找规则就行。记住千万不要和上下文混淆，可能会是完全不一样的结果。
# 数组的扁平化（原始的方式）
## 数组扁平化概念
数组扁平化是指将一个多维数组变为一维数组,例如：

```
[1, [2, 3, [4, 5]]]  ------>    [1, 2, 3, 4, 5]
```
## 解决方案
1) reduce
遍历数组每一项，若值为数组则递归遍历，否则concat。

```
function flatten(arr) {  
    return arr.reduce((result, item)=> {
        return result.concat(Array.isArray(item) ? flatten(item) : item);
    }, []);
}
```
reduce是数组的一种方法，它接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。[]为初始值

2) toString & split
调用数组的toString方法，将数组变为字符串然后再用split分割还原为数组
```
function flatten(arr) {
    return arr.toString().split(',').map(function(item) {
        return  isNaN(Number(item))? item: Number(item); //不是数字类型的不转化
    })
} 

```
因为split分割后形成的数组的每一项值为字符串，所以需要用一个map方法遍历数组将其每一项转换为数值型

3) join & split
join() 方法用于把数组中的所有元素放入一个字符串。

```
function flatten(arr) {
    return arr.join(',').split(',').map(function(item) {
        return  isNaN(Number(item))? item: Number(item); //不是数字类型的不转化
    })
}
```
4) 递归
递归的遍历每一项，若为数组则继续遍历，否则concat

```
function flatten(arr) {
    var res = [];
    arr.map(item => {
        if(Array.isArray(item)) {
            res = res.concat(flatten(item));
        } else {
            res.push(item);
        }
    });
    return res;
}
```
5) 扩展运算符
```
function flatten(arr) {
    while(arr.some(item=>Array.isArray(item))) {
        arr = [].concat(...arr);
    }
    return arr;
}
```
若arr中含有数组则使用一次扩展运算符，直至没有为止。

# class语法糖的实现

# 高阶组件的两种实现方式
## 属性代理
## 反向继承

# 原生方法与合成方法的区别

# 箭头函数与普通函数的区别
参考地址：[https://www.jianshu.com/p/eca50cc933b7](https://www.jianshu.com/p/eca50cc933b7)
1) 首先箭头函数作为匿名函数,是不能作为构造函数的,不能使用new
2) this,普通函数构造函数的 this 指向了一个新的对象；如果函数是作为对象的方法被调用的，则其 this 指向了那个调用它的对象;但是箭头函数的则会捕获其所在上下文的 this 值，作为自己的 this 值。在使用call或apply绑定时,相当于只是传入了参数,对this没有影响
3) 箭头函数不绑定arguments,取而代之用rest参数…解决
4) 箭头函数当方法使用的时候没有定义this绑定
```
var obj = {
  i: 10,
  b: () => console.log(this.i, this),
  c: function() {
    console.log( this.i, this)
  }
}
obj.b(); // prints undefined, Window
obj.c(); // prints 10, Object {...}
```
5) 箭头函数不能当做Generator函数,不能使用yield关键字
6) 不能简单返回对象字面量
```
var func = () => {  foo: 1  };
// Calling func() returns undefined!
var func = () => {  foo: function() {}  };
// SyntaxError: function statement requires a name
//如果要返回对象字面量,用括号包裹字面量
var func = () => ({ foo: 1 });
```
7) 箭头函数不能换行
```
var func = ()          
 => 1; // SyntaxError: expected expression, got '=>'
```
# 节流、防抖具体实现（代码实现）
## 防抖
```
function debounce(fn, wait) {
  var timer = null;
  return function () {
      var context = this
      var args = arguments
      if (timer) {
          clearTimeout(timer);
          timer = null;
      }
      timer = setTimeout(function () {
          fn.apply(context, args)
      }, wait)
  }
}

var fn = function () {
  console.log('boom')
}

setInterval(debounce(fn,500),1000) // 第一次在1500ms后触发，之后每1000ms触发一次

setInterval(debounce(fn,2000),1000) // 不会触发一次（我把函数防抖看出技能读条，如果读条没完成就用技能，便会失败而且重新读条）

```
## 节流
```
ffunction throttle(fn, gapTime) {
   let _lastTime = null;
 
   return function () {
     let _nowTime = + new Date()
     if (_nowTime - _lastTime > gapTime || !_lastTime) {
       fn();
       _lastTime = _nowTime
     }
   }
 }
 
 let fn = ()=>{
   console.log('boom')
 }
 
 setInterval(throttle(fn,1000),10)
```
# javascript逻辑运算符“||”和“&&”
参考地址：https://www.cnblogs.com/pigtail/archive/2012/03/09/2387486.html

1) 只要“||”前面为false，无论“||”后面是true还是false，结果都返回“||”后面的值。

2) 只要“||”前面为true，无论“||”后面是true还是false，结果都返回“||”前面的值。

3) 只要“&&”前面是false，无论“&&”后面是true还是false，结果都将返“&&”前面的值;

4) 只要“&&”前面是true，无论“&&”后面是true还是false，结果都将返“&&”后面的值;
例如：

```
1&&7 // 7
-1&&9 // 9
0&&9 // 0
0&&8 // 0

1||9 // 1
1||3 // 1
0||9 // 9
0||3 // 3
```
# 实例的构造函数 constructor 与原型的关系
```
function A () {};
function B () {};
B.prototype = new A();
var a = new A();
var b = new B();
console.log(a.constructor); // A
console.log(b.constructor); // A
```
当实例在自身找不到 constructor，会向原型寻找 constructor。那为什么b的constructor不是B，而是指向A呢？我们可以这么理解，原型被改变，原型的constructor指向原型本身的构造函数，也就是说constructor属于原型，而不是原型对象的属性。

# 事件委托
事件委托，通俗地来讲，就是把一个元素响应事件（click、keydown......）的函数委托到另一个元素；
比如一个消息列表，有n多条回话，同时还是动态。你需要点击item响应跳转不同的回话。如果给每条元素添加监听事件，势必会消耗大量的性能。这时候就需要事件委托来做这件事。

```
document.getElementById('wraper').addEventListener('click', function (e) {
  var target = e.target
  if (target.nodeName.toLocaleLowerCase() === 'li') {
    console.log(target.innerHTML);
  }
});

```
如上代码中wraper是父组件，将事件委托给它，我们只需要判断e.target响应的对象进行处理就行。事件委托就是这个意思。

# let与var的区别
先来看两个例子：
```
var a = 100;
function test(){
    console.log(a);
    var a = 10;
    console.log(a);
}
test() // undefined 10
```

在看这个：
```
var a = 100;
function test(){
    console.log(a);
    let a = 10;
    console.log(a);
}
test() // 报错 
```
在第二个例子里，由于let不存在状态提升，所以导致a在函数作用域里是没有被声明的，所以报错。在这两例中，全局a只有在函数作用域里没有a的声明时，全局a才有效。

# react 的 setState 哪些情况下是同步的。
* 原生事件里使用 setState,能拿到最新的值
* setTimeout里使用setState，能够拿到最新的值。 

参考： https://juejin.im/post/5b45c57c51882519790c7441#heading-3



# 移动端点击事件300ms的延迟如何解决。为什么会有这种情况。
# react 16.4之后生命周期有哪些变化
# setState 同步有哪些情况
# fetch 如何实现时间等待超时
使用promise的race方法。
```angular2
Promise.race([
    fetch(URL),
    new Promise(function(resolve,reject){
        setTimeout(()=> reject(new Error('request timeout')),2000)
    })])
    .then((data)=>{
        //请求成功
    }).catch(()=>{
        //请求失败
});
```
当超过20000秒的时候，fetch还没返回响应结果，下面的promise会比它快，进而报错

# 状态组件和无状态组件的优缺点

# html 列表优化
# 深拷贝与浅拷贝的区别，如何深拷贝
# 真实一像素
持续更新中。。。。







