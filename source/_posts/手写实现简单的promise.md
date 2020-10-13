---
title: 手写实现简单的promise
date: 2019-04-28 20:14:50
categories: 前端
tags: [js, promise, 面试]
---
手写promise几乎每家大厂的必备考题，几次面试都被这个问题坑了，于是花了些时间特意研究了一下，下面是promise实现的思考过程。大家如果不嫌弃，还请往下看：
## promise的介绍
所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。
<!-- more -->
例如以下一个promise的例子：
```
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```
可以看出一个promise的构造函数包含两个方法resolve、reject,同时根据promise+规范可知promise包含三个状态：
* pending: 初始状态，既不是成功，也不是失败状态。
* fulfilled: 意味着操作成功完成。
* rejected: 意味着操作失败。
那么我们可以可以根据这三种不同状态去实现resolve、reject，以及实现then方法，那么一个简单的promise雏形就出来了。下面来实现它：
## promise构造函数：
首先可以根据上面的推测写个构造函数如下：
```
/**
 * 创建三变量记录表示状态
 * 用that保存this，避免后期闭包导致this的指向不对
 * value 变量用于保存 resolve 或者 reject 中传入的值
 * resolvedCallbacks 和 rejectedCallbacks 用于保存 then 中的回调，
 * 因为当执行完 Promise 时状态可能还是等待中，这时候应该把 then 中的回调保存起来用于状态改变时使用
 */

const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

function myPromise(fn){
  const that = this;
  that.value = null;
  that.status = PENDING; //默认状态
  that.fulfilledCallbacks = [];
  that.rejectedCallbacks = [];
  function resolve(value){
    if(that.status === PENDING ){
      }
  }
 function reject(value){
    if(that.status === PENDING ){
      }
  }
    
  // 执行回调函数
   try{
        fn(resolve, reject)
    }catch (e) {
        reject(e);
    }
}
```
于是思考当在resolve里该干些什么？resolve即执行状态，首先status状态值得变吧，改成fulfilled状态，同时将传入的value值保存起来，以便下面的then会用到，最后得执行回调里面的方法实现回调调用。reject同理，于是按这个思路，就有了：
```
/**
 * 创建三变量记录表示状态
 * 用that保存this，避免后期闭包导致this的指向不对
 * value 变量用于保存 resolve 或者 reject 中传入的值
 * resolvedCallbacks 和 rejectedCallbacks 用于保存 then 中的回调，
 * 因为当执行完 Promise 时状态可能还是等待中，这时候应该把 then 中的回调保存起来用于状态改变时使用
 */

const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

function myPromise(fn){
  const that = this;
  that.value = null;
  that.status = PENDING; //默认状态
  that.fulfilledCallbacks = [];
  that.rejectedCallbacks = [];
  function resolve(value){
    if(that.status === PENDING ){
        that.status = FULFILLED;
        that.value = value;
        //执行回调方法
        that.fulfilledCallbacks.forEach(myFn => myFn(that.value))
      }
  }
 function reject(value){
    if(that.status === PENDING ){
        that.status = REJECTED;
        that.value = value;
        //执行回调方法
        that.rejectedCallbacks.forEach(myFn => myFn(that.value))
      }
  }
    
  // 执行回调函数
   try{
        fn(resolve, reject)
    }catch (e) {
        reject(e);
    }
}
```
于是promise的构造函数就简陋的完成了，接下来实现then不就大工完成了吗？是不是有些小兴奋~~~
## promise中then的实现
考虑到所有的实例都要用到then方法，在then得放在promise的原型链上。当状态是PENDING状态时，该做什么？不执行回调，那就将回调方法分别放入不同的栈内，等待调用。当状态为FULFILLED或者REJECTED时，则执行响应的方法即可。于是：
```
   myPromise.prototype.then = function (onFulfilled, onRejected){
    let self = this;
    //等待状态，则添加回调函数到栈中
    if(self.status === PENDING){
        self.fulfilledCallbacks.push(()=>{
            onFulfilled(self.value);
        });
        self.rejectedCallbacks.push(()=>{
            onRejected(self.value);
        })
    }
    if(self.status === FULFILLED){
        onFulfilled(self.value);
    }

    if(self.status === REJECTED){
        onRejected(self.value)
    }
}
```
于是一个简单的promise实现如下：
```
/**
 * 创建三变量记录表示状态
 * 用that保存this，避免后期闭包导致this的指向不对
 * value 变量用于保存 resolve 或者 reject 中传入的值
 * resolvedCallbacks 和 rejectedCallbacks 用于保存 then 中的回调，
 * 因为当执行完 Promise 时状态可能还是等待中，这时候应该把 then 中的回调保存起来用于状态改变时使用
 */

const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

function myPromise(fn){
  const that = this;
  that.value = null;
  that.status = PENDING; //默认状态
  that.fulfilledCallbacks = [];
  that.rejectedCallbacks = [];
  function resolve(value){
    if(that.status === PENDING ){
        that.status = FULFILLED;
        that.value = value;
        //执行回调方法
        that.fulfilledCallbacks.forEach(myFn => myFn(that.value))
      }
  }
 function reject(value){
    if(that.status === PENDING ){
        that.status = REJECTED;
        that.value = value;
        //执行回调方法
        that.rejectedCallbacks.forEach(myFn => myFn(that.value))
      }
  }
    
  // 执行回调函数
   try{
        fn(resolve, reject)
    }catch (e) {
        reject(e);
    }
}
 myPromise.prototype.then = function (onFulfilled, onRejected){
    let self = this;
    //等待状态，则添加回调函数到栈中
    if(self.status === PENDING){
        self.fulfilledCallbacks.push(()=>{
            onFulfilled(self.value);
        });
        self.rejectedCallbacks.push(()=>{
            onRejected(self.value);
        })
    }
    if(self.status === FULFILLED){
        onFulfilled(self.value);
    }

    if(self.status === REJECTED){
        onRejected(self.value)
    }
}

let p = new myPromise((resolve, reject)=>{
    console.log('hello');
    resolve(5);
});
p.then((res)=>{
    console.log(res);
})
p.then(()=>{
    console.log('jj');
})
```
结果如下：
![](https://xunmengren-blog.oss-cn-beijing.aliyuncs.com/20200311161547.png)
于是一个简单的promise大工告成！

可是有没有发现then里并没有返回一个promise,并不符合规范，所以可以对promise进行部分优化。
## 改造promise
根据promise+规范改造promise，传给then的应该是个promise,如下：
resolve 和reject的改造：
* 对于 resolve 函数来说，首先需要判断传入的值是否为 Promise 类型
* 为了保证函数执行顺序，需要将两个函数体代码使用 setTimeout 包裹起来
```
 function resolve(value) {
        if(value instanceof myPromise){
            return value.then(resolve, reject);
        }
        setTimeout(()=>{
            that.status = FULFILLED;
            that.value = value;
            //执行会回调方法
            that.fulfilledCallbacks.forEach(myFn => myFn(that.value))
        },0)
    }

    function reject(value) {
        setTimeout(()=>{
            that.status = REJECTED;
            that.value = value;
            //执行会回调方法
            that.rejectedCallbacks.forEach(myFn => myFn(that.value))
        }, 0);
    }
```
接下来改造then方法：
首先我们需要新增一个变量 promise2，因为每个 then 函数都需要返回一个新的 Promise 对象，该变量用于保存新的返回对象，于是：
```
myPromise.prototype.then = function (onFulfilled, onRejected) {
    let self = this;
    let promise2 = null;
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v;
    onRejected = typeof onRejected === 'function' ? onRejected : r => {throw r}
    //等待状态，则添加回调函数到栈中
    if(self.status === PENDING){
        return (promise2 = new myPromise((resolve, reject)=>{
            self.fulfilledCallbacks.push(()=>{
                try {
                    let x = onFulfilled(self.value);
                    resolutionProduce(promise2, x, resolve, reject);
                }catch (e) {
                    reject(e)
                }
            });
            self.rejectedCallbacks.push(()=>{
                try {
                    let x = onRejected(self.value);
                    resolutionProduce(promise2, x, resolve, reject);
                }catch (e) {
                    reject(e);
                }
                onRejected(self.value);
            });
        }));
    }
    if(self.status === FULFILLED){
        return(promise2 = new myPromise((resolve, reject)=>{
            setTimeout(()=>{
                try {
                    let x = onFulfilled(self.value);
                    resolutionProduce(promise2, x, resolve, reject);
                }catch (e) {
                    reject(e);
                }
            }, 0)
        }));
    }

    if(self.status === REJECTED){
        return (promise2 = new myPromise((resolve, reject)=>{
            setTimeout(()=>{
                try {
                    let x = onRejected(self.value);
                    resolutionProduce(promise2, x, resolve, reject)
                }catch (e) {
                    reject(e);
                }
            },0)
        }));
    }
}
```
思路跟之前基本一致，只是说把之前返回值改成promise,同时捕获异常，在status状态为FULFILLED或者REJECTED的时候执行得加上异步setTimeout包裹。
接下来完成最核心的resolutionProduce函数：
* 首先规范规定得保证当前的x不能与promise2一致，否则将执行无意义的相同操作，导致循环引用的发生。
例如：
```
let p = new myPromise((resolve, reject) => {
  resolve(1)
})
let p1 = p.then(value => {
  return p1
})
```
* 然后需要判断 x 的类型
```
if (x instanceof MyPromise) {
    x.then(function(value) {
        resolutionProcedure(promise2, value, resolve, reject)
    }, reject)
}
```
这里的代码是完全按照规范实现的。如果 x 为 Promise 的话，需要判断以下几个情况：

1) 如果 x 处于等待态，Promise 需保持为等待态直至 x 被执行或拒绝
2) 如果 x 处于其他状态，则用相同的值处理 Promise


```
function resolutionProduce(promise, x, resolve, reject){
 if(promise === x){
        return reject(new TypeError('Error'));
 }
 let called = false;
 if(x !== null && (typeof x === 'object' || typeof x === 'function')){
        try {
            let then = x.then;
            if(typeof then === 'function'){
                then.call(x, y=>{
                    if(called) return;
                    called = true;
                    resolutionProduce(promise2, y, resolve, reject )
                }, e =>{
                    if(e) return;
                    called = true;
                    reject(e);
                })
            }else {
                resolve(x);
            }
        }catch (e) {
            if(called) return;
            called = true;
            reject(e);
        }
    }else {
        resolve(x);
    }
}
```
* 首先创建一个变量 called 用于判断是否已经调用过函数
* 然后判断 x 是否为对象或者函数，如果都不是的话，将 x 传入 resolve 中
* 如果 x 是对象或者函数的话，先把 x.then 赋值给 then，然后判断 then 的类型，如果不是函数类型的话，就将 x 传入 resolve 中
* 如果 then 是函数类型的话，就将 x 作为函数的作用域 this 调用之，并且传递两个回调函数作为参数，第一个参数叫做 resolvePromise ，第二个参数叫做 
 rejectPromise，两个回调函数都需要判断是否已经执行过函数，然后进行相应的逻辑
* 以上代码在执行的过程中如果抛错了，将错误传入 reject 函数中

以上便是promise的简单实现，下回有时间将把catch与all、race等方法实现，敬请期待
未完待续。。。
参考: [https://juejin.im/post/5b88e06451882542d733767a](https://juejin.im/post/5b88e06451882542d733767a)


