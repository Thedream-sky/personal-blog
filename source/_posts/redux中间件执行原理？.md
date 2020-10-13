---
title: redux中间件执行原理？
date: 2019-05-12 18:57:36
categories: 前端
tags: [redux,面试]
---

学习过react的同学肯定都用过redux。了解redux数据流机制的action->dispatch->store->reduce->页面交互其实很好理解，可是当我们要用到异步请求或者打印日志之类的副操作的时候，我们无法避免的会用到中间件Middleware。中间件都是怎么执行以及如何有序的串在一起很令人迷惑，下面我将尝试解释这个过程：

<!-- more -->
## 演变过程
假如你有个需求需要在分发dispatch前后打印不同状态值，你肯定会这么想：
```
console.log('dispatching', action)
store.dispatch(action)
console.log('next state', store.getState())
```
封装成函数：
```
function dispatchAndLog(store, action) {
  console.log('dispatching', action)
  store.dispatch(action)
  console.log('next state', store.getState())
}
//执行
dispatchAndLog(store,  action)
```
不妨改造store的dispatch更为直接些：
```
let next = store.dispatch
store.dispatch = function dispatchAndLog(action) {
  console.log('dispatching', action)
  let result = next(action)
  console.log('next state', store.getState())
  return result
}
```
Redux把这个封装的入口写成了一个函数，就叫applyMiddleware。由此我们明白了applyMiddleware的功能：改造dispatch函数，产生真假dispatch，而中间件就是运行在假真（dispatchAndLog假和next真）之间的代码。

这里我们要对applyMiddleware进行一个准确的定义，它只是一个用来加工dispatch的工厂，而要加工什么样的dispatch出来，则需要我们传入对应的中间件函数（比如上例中的dispatchAndLog），下面我们构造一个精简版的applyMiddleware：
```
function applyMiddleware(middleware){
  let next = store.dispatch;
  store.dispatch = middleware(store)(next);  // 这里传入store，是因为中间件中有可能会用到getState获取数据，比如打印当前用户等需求 
 }
}
applyMiddleware(dispatchAndLog)
```
## 中间串连

中间件的功能各不相同，它们都要融入到dispatch中，在派发action的时候，按照顺序一个个的执行，这是一个费脑经的事情。在上例中middleware会返回新的dispatch，我们需要做的也就是将产生的新的dispatch传递个下个中间件即可。如下：
```
function applyMiddleware(middleware，middleware2){
  let next = store.dispatch;
  store.dispatch = middleware2(middleware(store)(next));  
  // 这里传入store，是因为中间件中有可能会用到getState获取数据，比如打印当前用户等需求 
 }
}
applyMiddleware(dispatchAndLog，dispatchmethed)
```
以此类推有大量的中间件的时候我们可以这么干：
```
function applyMiddleware（...middlewares）{
    middlewares = middlewares.slice();
    middlewares.reverse();  //执行顺序是后到前，所以得倒置
    let dispatch = store.dispatch
    //循环嵌套
    middlewares.foreach(function(middleware){
        dispatch = middleware(store)(dispatch);
    });
    return  Object.assign({}, store, { dispatch }); //合并dispatch到redux里。
}
```
请注意这里是一个循环嵌套的一个过程，所以中间的顺序决定着中间件的执行顺序。

总结两点：

1) 中间件在执行完副操作会并会返回新的dispatch
2) applyMiddleware本质是一个循环嵌套调用的过程。

参考：https://zhuanlan.zhihu.com/p/34651008

