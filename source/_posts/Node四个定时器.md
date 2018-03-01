---
title: Node四个定时器
date: 2018-02-09 13:50:45
tags: [node setTimeout]
category: [node]
---
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

### setTimeout
次轮循环中执行

setTimeout()只是将事件插入了"任务队列"，必须等到当前代码（执行栈）执行完，主线程才会去执行它指定的回调函数。要是当前代码耗时很长，有可能要等很久，所以并没有办法保证，回调函数一定会在setTimeout()指定的时间执行。
### setImmediate
次轮循环中执行

setImmediate()是将事件插入到事件队列尾部，主线程和事件队列的函数执行完成之后立即执行setImmediate指定的回调函数，和setTimeout(fn,0)的效果差不多，但是当他们同时在同一个事件循环中时，**_执行顺序是不定的_**。
### process.nextTick
本轮循环中执行

同步代码执行完之后立即执行，所有异步里面最快的

process.nextTick()方法可以在当前"执行栈"的尾部-->下一次Event Loop（主线程读取"任务队列"）之前-->触发process指定的回调函数。也就是说，它指定的任务总是发生在所有异步任务之前，当前主线程的末尾。（nextTick虽然也会异步执行，但是不会给其他io事件执行的任何机会）
 
**_最后process.maxTickDepth()的缺省值是1000，如果超过会报exceed callback stack。官方认为在递归中用process.nextTick会造成饥饿event loop，因为nextTick没有给其他异步事件执行的机会，递归中推荐用setImmediate_**