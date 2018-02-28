---
title: javascript的GC理解
date: 2018-02-24 16:07:36
tags: [垃圾回收]
category: [javascript]
---
js会将我们使用不到的变量销毁，怎么判断哪些变量是不会再使用的
* 全局变量是不会被销毁的，因为我们随时都可能会用到这个变量，所以不能被销毁。
* 函数内部的变量再函数执行完之后就会被销毁，但是如果这个函数有被外部的变量引用就不会销毁
```javascript
function a(){
    var b = 0;
    return function(){
        b ++;
        console.log(b);
    }
}
var d = a();
d();//1
d();//2
```
