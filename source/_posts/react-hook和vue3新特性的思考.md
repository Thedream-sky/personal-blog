---
title: react-hook和vue3新特性的思考
date: 2020-09-20 23:07:35
categories: 前端
tags: [react, js]
---
## 前言
react 作为主流前端框架之一，在很多地方有着它过人之处，值得去研究和学习。正好前段时间在学习react-hook新出特性，谈谈自己的一些思考，在此做个笔记。
<!-- more -->

## react-hook是什么
引用官网的一句话：'它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。'什么意思呢？即是使用react推崇的函数组件编程思想同时还能使用拥有自己的state状态，感觉是函数组件的升级版。

## react-hook几个核心的api
参考官网： https://react.docschina.org/docs/hooks-reference.html
### useState
例如：
```
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```
使得函数组件拥有自己的state。
### useEffect
在实际开发中除了操作state，不可避免的需要一些副操作，例如网络请求、io操作等等。例如下面统计上线人数
```
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    fetch('', params).then((res)=>{
      setCount(()=> count++)  
    })
  });

  return (
    <div>
      <p>当前人数 {count} </p>
    </div>
  );
}
```
### useContext
context的钩子，用于组件之间共享数据。例如：
```
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = React.createContext(themes.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
```
### useReducer
useState的增强版，可以使用于一些特殊的情况。（和redux里的reduce有些类似）
```
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```
### useMemo
memo的钩子函数，用于判断传入state的依赖是否发生变化，在渲染阶段作出改变,例如。

```
import React, { memo, useState, useMemo } from "react";
function App() {
    const [value, setValue] = useState(0);
    const increase = useMemo(() => {
        if(value > 2) return value + 1;
    }, [value]);
    return (
        <div>
            <button
                type="button"
                onClick={() => {
                    setValue(value + 1);
                }}
            >
                value:{value},increase:{increase || 0}
            </button>
        </div>
    );
}
```
### useRef
ref 的钩子函数,拿到元素本身的ref。例如：
```
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}

```
## 为什么需要react-hook
在谈论这个问题的时候，我们应该去思考原有的react有什么缺陷或者不足的地方，下面是我在学习过程发现的一些问题。
### react组件过于臃肿，实现不方便。
看个例子：
```
import React, { Component } from "react";

export default class Button extends Component {
  constructor() {
    super();
    this.state = { buttonText: "Click me, please" };
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    this.setState(() => {
      return { buttonText: "Thanks, been clicked!" };
    });
  }
  render() {
    const { buttonText } = this.state;
    return <button onClick={this.handleClick}>{buttonText}</button>;
  }
}
```
在上个例子中，为了改变一个按钮状态，需要这么多过程来配合它，是不是显得有些笨重。如果还涉及到一些生命周期的问题，问题是不是就变得复杂了。
下面是Hook的实现方式：
```
import React, { useState } from 'react';

function Button() {
  // 声明一个叫 "count" 的 state 变量
  const [buttonText, setButtonText] = useState("Click me, please");
  return (<button onClick={()=>{setButtonText('Thanks, been clicked!')}}>{buttonText}</button>);
}
```
react-hook是函数式编程，你不需要定义constructor，render，定义class，只需要关注需要改变的内部状态即可，这样不是清爽多了！
### 状态微粒化
有过react的实际开发经历的都会知道，当一个页面的内容很复杂的时候，大家基本上会第一时间想到函数组件来拆分页面，实现组件化。组件不要变成复杂的容器，最好只是数据流的管道。组件的最佳写法应该是函数，而不是类。这么看普通的函数组件就能满足需求了，然而，事实并非如此。我们不可避免的需要在使用过程中使用一些状态，这个时候还得依赖class组件的state。这个时候使用react-hook就恰到好处，无需依赖其他state，自己管理自身的state即可。
上面那个class组件可以改装成这样：
```
import React, { Component, useState } from "react";

function Button() {
  // 声明一个叫 "count" 的 state 变量
  const [buttonText, setButtonText] = useState("Click me, please");
  return (<button onClick={()=>{setButtonText('Thanks, been clicked!')}}>{buttonText}</button>);
}

export default class Button extends Component {
  render() {
   return <Button />
  }
}
```
试想页面存在多个这样的不同的组件的时候，使用state那种让人头大方式就不复存在了，也能更好地关注业务的实现，在后期维护上也很方便。

### 逻辑复用
参考之前写的文章：（[React Hook 小小窥视](http://xunmengren.work/2020/06/03/%E5%89%8D%E7%AB%AF%E7%9F%A5%E8%AF%86%E6%A2%B3%E7%90%86-%E8%BF%9B%E9%98%B6)） 

总体来说，react对于逻辑复用这块，也就两种方式，第一种是使用插槽这种方式：
```
var LogMixin = {
  log: function() {
    console.log('log');
  },
  componentDidMount: function() {
    console.log('in');
  },
  componentWillUnmount: function() {
    console.log('out');
  }
};

var User = React.createClass({
  mixins: [LogMixin],
  render: function() {
    return (<div>...</div>)
  }
});

var Goods = React.createClass({
  mixins: [LogMixin],
  render: function() {
    return (<div>...</div>)
  }
});
```
这种方式在react中已经被弃用了。

第二种也就是常见的高阶组件这种方式了，具体如下：
```
/** 属性代理 */
function proxyHOC(WrappedComponent) {
  // props的一些劫持操作
  ...  
  return class extends Component {
    render() {
      return <WrappedComponent {...this.props} />;
    }
  }
}
/** 反向继承(侵入性比较强，不推荐) */
function inheritHOC(WrappedComponent) {
  return class extends WrappedComponent {
    // 操作一些生命周期
    ...  
    render() {
      return super.render();
    }
  }
}
```
所谓高阶组件也就是利用函数劫持传入的props进行一些操作，并将这些新特性传递给传入组件返回新组件的一种方式，看起来是挺优雅的，不会改变转入组件的原有特性，然而也有不尽之处。
* 如果大量使用HOC，将会产生非常多的嵌套，在代码理解和使用这块会比较困难，会使得调试变得非常困难，同时不利于后期的维护。
* props 在各个高阶组件没有一些约定的命名规范的时候，会造成不同高阶组件相同属性重名，造成props覆盖，这是不愿意看到的。
* props的来源难以追踪，当多重hoc嵌套时，是没法在最终的组件里区分这些props的来源的，一旦props出现问题，也不能立即定位到具体的高级组件。

这个时候Hook得出现就能解决这些问题。
Hook说白了也就是一个函数，我们可以写一些公用逻辑，例如写一个登录逻辑。
```
import React, { useState, useEffect } from 'react';

function(){
    const [logined, setLogined] = useState(false);
    useEffect(()=>{
       fetch('login', params).then(res=>{
           setLogined(res.logined)
       }) 
    })
    retuen { logined, }
}
```
然后在引入到不同组件中，登录逻辑被复用了，业务逻辑和ui逻辑也可以被分开。

* Hook 的方式与Minin 方式其实很像，但是Mixin引入的逻辑和状态是可以相互覆盖的，而Hook重命名的特性使得各个Hook互不影响。
* 大量使用HOC的情况下让我们的代码变得嵌套层级非常深，使用Hook，我们可以实现扁平式的状态逻辑复用，而避免了大量的组件嵌套，便于管理。
* hook组件基本上告别了this各种昏头转向的指向问题，使得开发过程也清晰简单很多。

应用例子：[业务逻辑与ui逻辑分离的问题](https://juejin.im/post/6844904100128112648)

此处使用hook来实现：
```
import { useState, useEffect } from 'react'

/** 逻辑区域 */
const useCardState = (id) =>{
  const [card, setCard] = useState({})
  useEffect(()=>{
     getCardById(this.props.id).then(card => {
       setCard(()=> card)
     }
  })
  return card;
}

/** 纯函数UI渲染 */

const Card = ({name, logoUrl, buyerNum, link}) => {
    return (
        <div className="card">
            <div className="logo">
                <img src={logoUrl} alt={name} />
            </div>
            <div className="name">{name}</div>
            <div className="buyer-num">
                {buyerNum}人已购买
            </div>
            <div className="booklet-link">
                <a href={link}>试读</a>
            </div>
        </div>
    )
}
```
当业务相当复杂的时候，将业务逻辑和ui渲染分隔开来，对于后期的维护有着十分重大的意义。此处hook便能提供一种优雅的解决方案。


## vue3里类似于react-hook的一些新特性
### 响应式系统
在vue3中也允许组件拥有自己独立的状态特性，这点和react-hook有着异曲同工之妙,下面就[vue3响应式系统，Composition API](https://vue3js.cn/vue-composition-api/#setup)做一个简单介绍：

#### 核心api
* reactive(定义state)
```
const obj = reactive({ count: 0 })
```
* computed(计算属性)
```
const count = ref(1)
const plusOne = computed(() => count.value + 1)

console.log(plusOne.value) // 2

plusOne.value++ // 错误！
```
也可以使用 get 和 set 函数
```
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => {
    count.value = val - 1
  },
})

plusOne.value = 1
console.log(count.value) // 0
```
* watchEffect(副作用)
```
const count = ref(0)

watchEffect(() => console.log(count.value))
// -> 打印出 0

setTimeout(() => {
  count.value++
  // -> 打印出 1
}, 100)
```
高级api:
* customRef
自定义ref，可以显式地控制依赖追踪和触发响应，[参考: 使用自定义 ref 实现带防抖功能的 v-model](https://vue3js.cn/vue-composition-api/#customref)

* shallowReactive
只为某个对象的私有（第一层）属性创建浅层的响应式代理，不会对“属性的属性”做深层次、递归地响应式代理，而只是保留原样。[参考](https://vue3js.cn/vue-composition-api/#shallowreactive)

* shallowReadonly(只读属性)
[参考](https://vue3js.cn/vue-composition-api/#shallowreactive)
```
const state = shallowReactive({
  foo: 1,
  nested: {
    bar: 2,
  },
})

// 变更 state 的自有属性是响应式的
state.foo++
// ...但不会深层代理
isReactive(state.nested) // false
state.nested.bar++ // 非响应式
```
例子：
```
const { reactive , onMounted } = Vue
export default {
  setup(){
      const state = reactive({
          count:0,
          todoList:[]
      })
      /* 生命周期mounted */
      onMounted(() => {
        console.log('mounted')
      })
      /* 增加count数量 */
      function add(){
          state.count++
      } 
      /* 减少count数量 */
      function del(){
          state.count--
      }
      /* 添加代办事项 */
      function addTodo(id,title,content){
          state.todoList.push({
              id,
              title,
              content,
              done:false
          })
      }
      /* 完成代办事项 */
      function complete(id){
          for(let i = 0; i< state.todoList.length; i++){
              const currentTodo = state.todoList[i] 
              if(id === currentTodo.id){
                  state.todoList[i] = {
                      ...currentTodo,
                      done:true
                  } 
                  break
              }
          }
      }
      return {
          state,
          add,
          del,
          addTodo,
          complete
      }
  }
}
```
原理介绍：
[Vue3响应式原理 + 手写reactive](https://segmentfault.com/a/1190000023465134)

应用实例：[Vue3 Composition API中的提取和重用逻辑](https://zhuanlan.zhihu.com/p/135445335)

## vue如何使用jsx

为什么要说这个呢，这也是最近使用vue发现的一个痛点吧，如下例所示：
![](https://xunmengren-blog.oss-cn-beijing.aliyuncs.com/20201012230715.png)
这是一个订单详情页的页面ui的一小部分代码片段，是不是每次有新需求了，再去改动这些代码的时候有点找不到路的感觉，庞大的页面代码，看的都眼花，急需拆分成各种小片段来‘分区治理’。在react中肯定轻易的想到使用函数组件来拆分这个，vue该怎么搞呢？
方法一：使用vue组件
这个比较常见，然而对于复用性不强的代码片段，单独封装成一个组件显得有点没必要。
方法二：使用vue的函数组件

```
Vue.component('anchored-heading', {
  render: function (createElement) {
    // 创建 kebab-case 风格的 ID
    var headingId = getChildrenTextContent(this.$slots.default)
      .toLowerCase()
      .replace(/\W+/g, '-')
      .replace(/(^-|-$)/g, '')

    return createElement(
      'h' + this.level,
      [
        createElement('a', {
          attrs: {
            name: headingId,
            href: '#' + headingId
          }
        }, this.$slots.default)
      ]
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```
拿官方的一个例子来看，是不是瞬间觉得vue的函数组件瞬间不香了，这也太麻烦了吧，活脱脱的一个原始的dom'虚拟树'啊,在书写上就已经不容易了，更不要谈后期的维护了。。。

方法三：
既然jsx这么嚣张，让老衲无处安身，不妨探究一下在vue中如何使用jsx吧，要善于向对手学习。
![](https://xunmengren-blog.oss-cn-beijing.aliyuncs.com/20201012233341.png)

参考文章：[在vue中使用jsx](https://juejin.im/post/6844904061930586125#heading-3)

大致引入步骤分2步：
1. 安装jsx的babel依赖
```
 # Using npm
 npm install --save-dev babel-preset-vue-app

# Using yarn
 yarn add --dev babel-preset-vue-app

```
2. 在.babelrc文件中，添加：
{
 "presets": ["vue-app"]
}

接下来就可以愉快的使用jsx。至于jsx的具体细节，参考上面文章就好了，这里没必要再次复述了。

在这里介绍如何在vue中使用jsx，并不是否定vue的模板化指令，只是就问题提供一种解决方案罢了，大家可以根据具体需求采纳方案。

以上便是就最近学习使用过程的一些感悟，写的不好，还望大家包涵，接下来再接再厉！
