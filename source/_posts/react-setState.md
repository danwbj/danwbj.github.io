---
title: 深入理解react的setState()
date: 2018-11-28 16:31:23
tags: [react, setState()]
category: [web前端]
---

之前对 react 的 setState 一直不太理解其实现机制，并且在一次面试中被问到，因此查阅了官方文档以及一些资料，将 setState()的一些问题记录一下。

### 了解 react 的 setState()

很多人在使用 setState()中如果不清楚 setState 的机制就会造成一些奇怪的问题，setState()的使用在 react 中是一个比较复杂的问题。
官方[文档](https://reactjs.org/docs/react-component.html#setstate)中指出了使用 setState()的时候需要注意的几点：

- 不要直接改变 this.state，虽然这个也可以更新 state 的值，但是他不会引起页面重新渲染，而且之后调用 setState()可能会替换掉你做的改变。把 this.state 当做是不可变的。

- 将 setState()认为是一次请求而不是一次立即执行更新组件的命令。为了更为可观的性能，React 可能会推迟它，稍后会一次性更新这些组件。React 不会保证在 setState 之后，能够立刻拿到改变的结果。

- 对 setState 的调用没有任何同步性的保证，并且调用可能会为了性能收益批量延迟执行。因此如果想获取到更新后的值就需要使用**componentDidUpdate**或者**setState 的回调函数**

- setState()将总是触发一次重绘，除非在 shouldComponentUpdate()中返回了 false。因此仅在新 state 和之前的 state 存在差异的时候调用 setState()可以避免不必要的重新渲染。

### setState()是异步的？

一个经典的例子，我们都知道会输出 0

```javascript
// state.count 当前为 0
componentDidMount(){
    this.setState({count: state.count + 1});
    console.log(this.state.count) //0
}

```

因此我们都觉得 setState()是异步的，如果想要获取更新后的值，可以使用回调函数实现：

```javascript
// state.count 当前为 0
componentDidMount(){
    this.setState({count: state.count + 1},()=>{
     console.log(this.state.count) //1
    });

}
```

React 还会尝试将 setState 调用分组或批处理为一个调用

```javascript
//假设this.state = {value：0};
this.setState（{count：this.state.count + 1}）;
this.setState（{count：this.state.count + 1}）;
this.setState（{count：this.state.count + 1}）;
```

处理完所有上述调用 this.state.value 后将是 1，而不是我们所期望的 3，这是因为重复设置 count 的值会被 react 批处理为一个调用，因此会输出 1

如果需要根据前一个状态设置状态，就需要使用 updater 参数了
setState 接受一个函数作为其参数,如果将函数作为 setState 的第一个参数传递，则 React 将使用 at-call-time-current 状态调用它，并期望您返回一个 Object 以合并到状态。所以将上面的示例更新为：

```javascript
this.setState(state => ({ count: state.count + 1 }));
this.setState(state => ({ count: state.count + 1 }));
this.setState(state => ({ count: state.count + 1 }));
```

此语法计算的该值是根据以前的状态计算的，因此上例会得到结果 this.state.count 的值为 3

### setState()是同步的？

从上述的种种例子可能我们认为 setState()就是异步的，但是事实并非如此，就想官方网站说的**不保证同步**，是否同步是取决于它的执行上下文，我们看下边的例子：

```javascript
  handerClick() {
    console.log('prev state:', this.state.counter); //0
    this.setState({ counter: this.state.counter + 1 });
    console.log('next state:', this.state.counter);//1
  }

componentDidMount() {
    //延时调用onclick事件
    setTimeout(this.handerClick.bind(this), 0);
  }
```

我们看到 setState()之后输出了 1
我们再看一个例子使用原生绑定一个 mousedown 事件：

```javascript
  handerClick() {
    console.log('prev state:', this.state.counter); //0
    this.setState({ counter: this.state.counter + 1 });
    console.log('next state:', this.state.counter);//1
  }

componentDidMount() {
   //手动绑定mousedown事件
ReactDOM.findDOMNode(this).addEventListener('mousedown',this.handerClick.bind(this));
}

```

我们看到 setState()之后依然输出了 1

以上的几个例子我们发现他似乎是同步的又似乎是异步的，通过阅读源码我们可以了解 setState 异步的实现

### setState 异步的实现

> 在 componentWillMount 中调用 setState

先是找到需渲染组件并将新的 state 并入该组件的需更新的 state 队列中,然后根据批量跟新策略更新组件

如果没有在事务流中，调用 batchedUpdates 方法进入更新流程，进入流程后，会将 isBatchingUpdates 设置为 true。

否则，将需更新的组件放入 dirtyComponents 中，也很好理解，先将需更新的组件存起来，稍后更新。

因此在 componentDidMount 中调用 setState 并不会立即更新 state，因为正处于一个更新流程中，isBatchingUpdates 为 true，所以只会放入 dirtyComponents 数组中等待稍后更新

> 事件中的调用 setState

react 是通过合成事件实现了对于事件的绑定，在事件的处理中也是通过同样的事务完成的，当进入事件处理流程后，该事务的 isBatchingUpdates 为 true，如果在事件中调用 setState 方法，也会进入 dirtyComponent 流程

> 原生事件绑定和 setTimeout 中 setState

原生事件绑定不会通过合成事件的方式处理，自然也不会进入更新事务的处理流程。setTimeout 也一样，在 setTimeout 回调执行时已经完成了原更新组件流程，不会放入 dirtyComponent 进行异步更新，其结果自然是同步的

### 总结

- 在更新组建时，将更新的 state 合并到原 state 是在 componentWillUpdate 之后，render 之前，所以在 componentWillUpdate 之前设置的 setState 可以在 render 中拿到最新值

- 在组件生命周期中或者 react 合成事件绑定中，setState 是通过异步更新的。

- 在原生事件和 setTimeout 中调用不一定是异步的

- setState()中的“异步”并不是真正的异步，其实本身执行的过程和代码都是同步的，只是组件生命周期中或者 react 合成事件的调用顺序在更新之前，导致在组件生命周期中或者 react 合成事件中没法立马拿到更新后的值，形式了所谓的“异步”

- setState 的批量更新优化也是建立在“异步”（合成事件、生命周期）之上的，在原生事件和 setTimeout 中不会批量更新
- 在“异步”中如果对同一个值进行多次 setState ， setState 的**批量更新策略**会对其进行覆盖，取最后一次的执行，如果是同时 setState 多个不同的值，在更新时会对其进行合并批量更新
