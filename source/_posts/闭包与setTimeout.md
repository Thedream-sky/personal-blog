---
title: 闭包与setTimeout
date: 2019-05-11 00:28:01
categories: 前端
tags: [js, 面试]
---
## 闭包
### 闭包的作用：
* 闭包的本质是一个函数
* 闭包可以访问函数内部变量
* 闭包的存在会使内部变量保留在内存中
<!-- more -->
闭包的应用：
1) 模仿块级作用域
2) 存储变量（js GC原理，基于javascript中函数作用域链的特点，内部函数保留了对外部函数的活动变量的引用，所以变量不会被释放）， 用于保存比较重要或者计算耗费很大的值。
3) 封装私有变量（不直接访问变量）

在了解闭包的作用之前，我们先了解一下 Javascript 中的 GC 机制:

在 Javascript 中，<font style="color: red">如果一个对象不再被引用，那么这个对象就会被 GC 回收，否则这个对象一直会保存在内存中。</font>

在下述例子中，B 定义在 A 中，因此 B 依赖于 A ,而外部变量 C 又引用了 B , 所以A间接的被 C 引用。

也就是说，A 不会被 GC 回收，会一直保存在内存中。：
例子：
```
function A() {
    var count = 0;
    function B() {
       count ++;
       console.log(count);
    }
    return B;
}
var C = A();
C();// 1
C();// 2
C();// 3
```
() 等价于 （A（））（）,为了访问函数内部的函数，并且隐藏内部的函数信息得到需要的结果。
高级写法：匿名函数的写法
```
(function (s){...})(s)等价于

function A(){
function B(s){
...
}
return B;
};
//相对于
function(s){...} === B === A();
B(s) === (function(s){...})(s)= (A())(s);
```
这里很明显可以看到外面可以访问函数内部的变量，这是闭包的一个很重要的功能。

什么是闭包？所谓闭包就是利用函数的局部作用域的特性（函数内部的函数可以访问函数的变量，而内部函数里的变量函数无法访问）来实现外部函数通过闭包获取函数内部变量的一个过程，同时也不需要暴露内部的具体情况。而利用GC特性，我们通过闭包可以保存一些需要长时间保持的数据。

## setTimeout
可能有人会说，切,setTimeout有啥可说啊~~~不好意思，可能本人见识短浅，setTimeout这里还是有几点可以说到说到。

在讲setTimeout之前，先看个经典的例子：
```
for(var i=0; i<=5; i++){
    setTimeout(function(){
        console.log(i);
    }, i*1000);
}
```
大家应该都知道，由于js的运行机制setTimeout属于异步任务。setTimeout 会挂起微任务，由于var不存在块级作用域，所以最终的i都是同一个i，i === 5循环结束，i+1 === 6，然后打印。所以结果是6，6，6，6，6，6。
那么如何乖乖的让这个打印出0到5呢？目前我想到了三种情况：

1) 闭包： 既然var没有块级作用域，那就用闭包模拟块级作用域。(实质是利用函数的局部作用域)
```
for(var i=0; i<=5; i++){
    (function(j){
        setTimeout(function(){
            console.log(j);
        }, i*1000);
    })(i)
}
```
2) let： let的块级作用域。
```
for(let i=0; i<=5; i++){
    setTimeout(function(){
        console.log(i);
    }, i*1000);
}
```
3)setTimeout传参：可能有些人知道，可能有的人并不知道。
```
for(var i=0; i<=5; i++){
    setTimeout(function(j){
        console.log(j);
    }, i*1000, i);
}
```
其实setTimeout内部同样是用到了闭包传参给回调函数，所以，我们可以使用setTimeout传参实现这个。

## 引申一下：

setTimeout是不是只能传一个参数，NO、NO、NO，肯定不是，用脚想想都不可能（考虑到实用性，通用性），那么怎么传？不买关子了，看例子：
```
 var i = 1;     
 setTimeout(function(j,k,l){
        console.log(j,k,l);
    }, i*1000, i,2*i, 3*i);
```
打印结果：
![](https://user-gold-cdn.xitu.io/2019/5/11/16aa27c9e4a47e63?w=456&h=84&f=png&s=6603)
我们可以大胆的猜测，setTimeout从第三个参数开始，可以传多个参数给回调函数。

不放心的话，可以去MDN里仔细的了解一下：https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout

如果觉得这么一个的写传入的参数，或者是参数的个数不明确，该怎么传？此时es6里的扩展运算符...可以排上用途了：
```
 var i = 1;     
 setTimeout(function(...args){
        console.log(args[0],args[1], args[2]);
    }, i*1000, ...[i, 2*i, 3*i]);
```
以上便是关于闭包与setTimeout的一点点总结，欢迎大家积极支持不对和不完整的地方。（轻点喷，谢谢啦！）

参考地址：
 https://www.cnblogs.com/onepixel/p/5062456.html
 https://segmentfault.com/a/1190000008681174 //闭包用途（重要）