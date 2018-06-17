---
title: 深入理解node的Event Loop
date: 2018-05-06 16:50:45
tags: [node,setTimeout]
category: [node]
---
### Event Loop 的六个阶段
#### timers   
> 调用setTimeout(),setInterval()的回调

Timers的回调函数在他指定的时间之后运行，因为他必须等待同步代码执行完毕，并且时间循环可能会被阻塞到poll阶段
另外系统的调度和其他回调的执行也有可能延迟他的执行

#### pending callbacks   
> 调用系统的错误回调

#### idle, prepare     
> 只在内部使用，不考虑

#### poll      
> 取新的I/O事件   
> 执行I/O相关的回调（除关闭回调、定时器的回调、setImmediate()的回调）

如果event loop进入了poll阶段，并且没有定时器，将会出现一下两种情况
- 如果poll队列不为空，event loop将遍历poll队列里面的所有回调函数以同步方式去执行，知道队列耗尽，或者达到系统的限制   
- 如果poll队列为空，将会出现两种情况
   - 执行脚本中存在setImmediate(),event loop将结束poll阶段，到下一个check阶段去执行setImmediate()的回调
   - 如果脚本中没有setImmediate() event loop将一直等待回调被添加到poll队列，并且立即去执行他

一旦poll阶段为空，event loop 将会去检查已经到时间的定时器，如果有准备好的定时器，event loop将会返回到timers阶段去执行定时器的回调   
event loop将在此阶段等待传入的链接，请求等


#### check      
> 调用setImmediate()回调

setImmediate()是一个特殊的定时器，处在event loop的一个单独阶段，poll阶段完成之后立即执行


#### close callbacks      
> 一些关闭回调，比如 socket.on('close', ...)


### Node四个定时器
为了协调异步任务，Node 提供了四个定时器，让任务可以在指定的时间运行。
* setTimeout()
* setInterval()
* setImmediate()
* process.nextTick()

猜测一下以下代码的运行结果
```javascript
setTimeout(() => console.log(1));
setImmediate(() => console.log(2));
process.nextTick(() => console.log(3));
Promise.resolve().then(() => console.log(4));
(() => console.log(5))();
```
运行结果如下。
```javascript
5
3
4
1
2
```
同步代码先于异步代码执行

异步任务分为：
* 追加在本轮循环的异步代码
* 追加在次轮循环的异步代码

```javascript
// 下面两行，次轮循环执行 等待时间相同的情况下setTimeout先于setInterval执行
setTimeout(() => console.log(1));
setInterval(() => console.log(1));
setImmediate(() => console.log(2));
// 下面两行，本轮循环执行
process.nextTick(() => console.log(3));
Promise.resolve().then(() => console.log(4));
```

#### setTimeout()
次轮循环中执行

setTimeout()在timers阶段执行，只是将事件插入了"任务队列"，必须等到当前代码（执行栈）执行完，主线程才会去执行它指定的回调函数。要是当前代码耗时很长，有可能要等很久，所以并没有办法保证，回调函数一定会在setTimeout()指定的时间执行。
#### setImmediate()
次轮循环中执行

setImmediate()在check阶段执行，主线程和事件队列的函数执行完成之后立即执行setImmediate指定的回调函数，和setTimeout(fn,0)的效果差不多   
当setImmediate()与setTimeout()在同一个主模块中运行而不是一个I/O循环中的时候，受到进程性能的影响，他们的执行顺序是不定的，如下例：
```javascript

// timeout_vs_immediate.js
setTimeout(() => {
  console.log('timeout');
}, 0);

setImmediate(() => {
  console.log('immediate');
});

$ node timeout_vs_immediate.js
timeout
immediate

$ node timeout_vs_immediate.js
immediate
timeout
```
如果他们处在一个I/O循环里面，则总是setImmediate()先执行，如下例：
```javascript 
// timeout_vs_immediate.js
const fs = require('fs');

fs.readFile(__filename, () => {
  setTimeout(() => {
    console.log('timeout');
  }, 0);
  //总是setImmediate()先执行
  setImmediate(() => {
    console.log('immediate');
  });
});

```
#### process.nextTick()
本轮循环中执行   
不在event loop中，不属于任何一个阶段   
同步代码执行完之后立即执行，所有异步里面最快的   

process.nextTick()方法可以在当前"执行栈"的尾部-->下一次Event Loop（主线程读取"任务队列"）之前-->触发process指定的回调函数。也就是说，它指定的任务总是发生在所有异步任务之前，当前主线程的末尾。（nextTick虽然也会异步执行，但是不会给其他io事件执行的任何机会）
 
**_最后process.maxTickDepth()的缺省值是1000，如果超过会报exceed callback stack。官方认为在递归中用process.nextTick会造成饥饿event loop，因为nextTick没有给其他异步事件执行的机会，递归中推荐用setImmediate_**
### Promise的执行

Promise的执行时在一个微任务队列中，process.nextTick()执行之后立即执行
