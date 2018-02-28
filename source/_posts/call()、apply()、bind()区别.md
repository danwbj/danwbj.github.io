---
title: call()、apply()、bind()区别
date: 2018-02-24 15:22:35
tags: [call() apply() bind()]
category: [javascript]
---
#### 执行的环境this
this指向：this永远指向的是最后调用它的对象，也就是看它执行的时候是谁调用的   

apply(),call(),bind()都是改变函数执行的环境（this）的，apply(),call()改变函数的this之后会立即执行函数，而bind()返回的是被修改this之后的新函数，在需要调用的时候去调用这个新函数，并且可以在执行的执行的时候去传递参数。


```javascript
var a = {
    user:"danw",
    fn:function (){
        console.log(this.user)
    }
}
var b = a.fn
b() //undefined
```
上面的代码之所以返回undefined是因为b在调用的时候是window.b(),this指向的是window，如果直接执行a.fn就会返回a里面的user
```javascript
var a = {
    user:"danw",
    fn:function (){
        console.log(this.user)
    }
}
a.fn()  //danw
```
虽然这种方式可以正确的返回，但是有时间我们不得不将对象赋值给另一个变量，这时候就需要使用以下三种方法来改变this指向：
#### call()
```javascript
var a = {
    user:"danw",
    fn:function (){
        console.log(this.user)
    }
}
var b = a.fn
b.call(a)   //danw
```
通过call方法将b添加到a执行环境中去执行，所有this就会指向a   
call方法还可以传递多个参数
```javascript
var a = {
    user:"danw",
    fn:function (p1,p2){
        console.log(this.user)
    }
}
var b = a.fn
b.call(a,1,2)   //danw
```
#### apply()
apply和call方法的效果一样，唯一不同的是在传递参数的时候是按照数组传递的。
```javascript
var a = {
    user:"danw",
    fn:function (p1,p2){
        console.log(this.user)
    }
}
var b = a.fn
b.apply(a,[1,2])   //danw
```
#### bind()
bind()方法和apply(),call()方法一样会改变this指向，但是bind()方法不会立即去执行函数
```javascript
var a = {
    user:"danw",
    fn:function (){
        console.log(this.user)
    }
}
var b = a.fn
b.bind(a)
```
执行上面代码发现并没有打印出结果，是因为b.bind(a)会返回一个新的函数
```javascript
var a = {
    user:"danw",
    fn:function (){
        console.log(this.user)
    }
}
var b = a.fn
var c = b.bind(a)   //返回新的函数c
c() //danw      执行函数c,会返回danw
```
所以谁bind()可以让对应的函数想什么时候执行就什么时候执行   
bind()也可以传递参数,并且可以在执行的时候再次追加参数
```javascript
var a = {
    user:"danw",
    fn:function (p1,p2,p3){
        console.log(p1,p2,p3)   //1,2,3
        console.log(this.user)
    }
}
var b = a.fn
var c = b.bind(a,1)   //返回新的函数c
c(2,3) //danw      执行函数c,会返回danw
```
