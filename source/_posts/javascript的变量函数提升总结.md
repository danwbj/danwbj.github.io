---
title: javascript的变量函数提升总结
date: 2018-03-01 10:28:56
tags: [变量函数提升]
category: [javascript]
---

首先对比三段代码：
```javascript
var v='Hello World';
alert(v); //Hello World
```
```javascript
var v='Hello World';
(function(){
    alert(v);
})()//Hello World
```
```javascript
var v='Hello World';
(function(){
    alert(v);
    var v='I love you';
})()//undefined
```
#### javascript作用域
JavaScript是函数级作用域(function-level scope)，并没有块级作用域,因此在代码块中并不会创建一个新的作用域。只有函数才会创建新的作用域，这样块里面的变量会影响到外部作用域，比如if语句：
```javascript
var x = 1;
    console.log(x); // 1
 if (true) {
   var x = 2;
   console.log(x); //2
}
 console.log(x);// 2
```
解决方案：
在函数中创建一个临时的作用域，请像下面这样做
```javascript
function foo() {
    var x = 1;
    if (x) {
        (function () {
            var x = 2;
            // some other code
        }());
    }
    // x is still 1.
}

```
#### 变量提升
变量提升 只是提升变量的声明，并不会把赋值也提升上来。
我们定义三个变量：

```javascript
(function(){
    var a='One';
    var b='Two';
    var c='Three';
})()
```
实际上它是这样子的：
```javascript
(function(){
    var a,b,c;
    a='One';
    b='Two';
    c='Three';
})()
```
####  函数提升
js中函数的定义有两种，函数声明方式和函数表达式方式，需要注意的是只有函数声明会被提升
函数声明方式提升【成功】

```javascript
function myTest(){
    foo();
    function foo(){
        alert("我来自 foo");
    }
}
myTest();
```
函数表达式方式提升【失败】

```javascript
function myTest(){
    foo();
   var foo =function foo(){
        alert("我来自 foo");
    }
}
myTest();
```
原文：http://www.cnblogs.com/damonlan/archive/2012/07/01/2553425.html