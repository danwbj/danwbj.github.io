---
title: setTimeout、setImmediate、process.nextTick区别
date: 2018-02-09 13:50:45
tags: [node setTimeout]
category: [node]
---
setTimeout()、setImmediate()、process.nextTick()
### setTimeout
setTimeout()只是将事件插入了"任务队列"，必须等到当前代码（执行栈）执行完，主线程才会去执行它指定的回调函数。要是当前代码耗时很长，有可能要等很久，所以并没有办法保证，回调函数一定会在setTimeout()指定的时间执行。
### setImmediate
setImmediate()是将事件插入到事件队列尾部，主线程和事件队列的函数执行完成之后立即执行setImmediate指定的回调函数，和setTimeout(fn,0)的效果差不多，但是当他们同时在同一个事件循环中时，执行顺序是不定的。
### process.nextTick
process.nextTick()方法可以在当前"执行栈"的尾部-->下一次Event Loop（主线程读取"任务队列"）之前-->触发process指定的回调函数。也就是说，它指定的任务总是发生在所有异步任务之前，当前主线程的末尾。（nextTick虽然也会异步执行，但是不会给其他io事件执行的任何机会）
 
##### 最后process.maxTickDepth()的缺省值是1000，如果超过会报exceed callback stack。官方认为在递归中用process.nextTick会造成饥饿event loop，因为nextTick没有给其他异步事件执行的机会，递归中推荐用setImmediate