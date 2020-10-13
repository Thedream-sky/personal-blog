---
title: 手写call、apply和bind
date: 2019-05-03 01:24:58
categories: 前端
tags: [js, 运行原理, 面试]
---

call, apply, bind这三个改变this指向的函数经常被用到，无论是开发还是面试。了解其实现原理，是一名合格的前端程序员必备技能。
* 如果还不知道call, apply, bind的基本用法，请先自行百度
<!-- more -->
## 动手需知
在自行实现这几个方法前，你得知道几个基本的知识点：
* arguments是什么？
借用MDN的说法：[arguments对象是所有（非箭头）函数中都可用的局部变量。你可以使用arguments对象在函数中引用函数的参数。此对象包含传递给函数的每个参数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/arguments)
* slice的用法：slice(index)代表从下标index开始截取数组。（其他用法自行查询去）
那么接下来就是见证奇迹的时刻了。

## call的实现
call的关键两点： 1、改变上下文指向； 2、传入多个参数。
```
Function.propoType.myCall = function(content){
  // call方法只能是方法才能调用
  if(typeof this !== "function"){
   throw new TypeError（"Error"）;
  }
  // 如果没有指定上下文，默认是全局对象window
  // 不要写成this.window。当this指向发生变化，可能找不到全局对象window
  content = content || window;
  let fn = Symbol(content);          // 避免属性重复
  let args = [...arguments].slice(1)
  //改变this指向
  content[fn] = this; //给content添加一个方法 指向this
  let result = content[fn](...args);  //传入参数
  delete content[fn];                     // 删除fn 方法，一个中间变量用完删除
  return result;
} 
```
## apply 的实现
apply的关键两点：1、改变上下文指向； 2、传入参数数组。
在实现原理上基本和call差不多，主要是参数的区别。
```
Function.propoType.myApply = function(content){
  // apply方法只能是方法才能调用
  if(typeof this !== "function"){
   throw new TypeError（"Error"）;
  }
  // 如果没有指定上下文，默认是全局对象window
  // 不要写成this.window。当this指向发生变化，可能找不到全局对象window
  content = content || window;
  let fn = Symbol(content);          // 避免属性重复
  let args = [...arguments][1];      // 记住传入的是数组！！！
 //改变this指向
  content[fn] = this; //给content添加一个方法 指向this
  let result ; 
  if(args){
     result = content[fn](...args);  //传入参数
  }else{
    result = content[fn]();  //传入参数
  }
  delete content[fn];                     // 删除fn 方法，一个中间变量用完删除
  return result;
} 
```
## bind 的实现
bind 的关键几点：1、改变上下文指向；2、传入多个参数；3、返回的是函数的方法名，不会立即执行。
```
Function.propoType.myBind = function(content){
    self = this;
  // bind方法只能是方法才能调用
  if(typeof this !== "function"){
   throw new TypeError（"Error"）;
  }
  // 可以支持柯里化传参，保存参数
  args = [...arguments].slice(1);
  function Fn (){
 // 考虑需要绑定的函数可能是构造函数，this不会被绑定，但是参数仍然会传递。
      if(this instanceof Fn){
          return self(...args.concat(...arguments));//构造函数直接传参
      }else{
          return self.apply(content,args.concat(...arguments));// apply传的是数组
     }
  }
  return Fn;
}
```

执行：
```
let Person = {
    name: 'Tom',
    say(x, y) {
        //console.log(this)
        console.log(`我叫${this.name}${x}${y}`)
    }
}
Person1 = {
    name: 'Tom1'
}
Person.say.myCall(Person1, ' call你干嘛', ' 哈哈哈');
Person.say.myApply(Person1, [' apply你干嘛', ' 哈哈哈',]);
Person.say.myBind(Person1, ' bind你干嘛', ' 哈哈哈kk')()
```
执行结果：
![结果](https://xunmengren-blog.oss-cn-beijing.aliyuncs.com/20200311161926.png)
结果完美呈现，以上便是call, apply, bind的简单实现。


