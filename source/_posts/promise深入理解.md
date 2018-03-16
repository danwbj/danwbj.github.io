---
title: promise深入理解
date: 2018-02-12 14:01:37
tags: [js,promise]
category: [node]
---
#### promise是什么
promise 是一个构造函数，自己身上有all、reject、resolve这些方法，原型上有then、catch这些方法，用Promise new出来的对象肯定就有then、catch方法。
```javascript
var p = new Promise(function(resolve, reject){
    //做一些异步操作
    setTimeout(function(){
        console.log('执行完成');
        resolve('随便什么数据');
    }, 2000);
});
```

*注意，这里知识new了一个Promise对象，并没有去调用它，但是传进去的函数已经之行了，所以在使用Promise的时候一般是包在一个函数中，使用的时候再去调用函数。

#### promise链式操作调用

```javascript
function runAsync1(){
    return new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('runAsync1执行完成');
            resolve('runAsync1随便什么数据');
        }, 2000);
    });
}
function runAsync2(){
    return new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('runAsync2执行完成');
            resolve('runAsync2随便什么数据');
        }, 2000);
    });
}
function runAsync3(){
    return new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('runAsync3执行完成');
            resolve('runAsync3随便什么数据');
        }, 2000);
    });
}

runAsync1().then(data => {
    console.log(data)//runAsync1函数的resolve结果
    return runAsync2()
}).then(data => {
    console.log(data)//runAsync2函数的resolve结果
    return runAsync3()
}).then(data => {
    console.log(data) //runAsync3函数的resolve结果  
})

//如果当前函数执行不依赖于上一个函数的返回值，也可以这样写:
runAsync1().then(runAsync2).then(runAsync3).then(data => {
    console.log(data) //这里只能拿到runAsync3函数的resolve结果 
})

//当然这种方式也可以手动在每一个函数内部获取上一个函数的返回值，例如runAsync2想要使用runAsync1的返回值，runAsync2就得这样写：

function runAsync2(data){
    console.log(data)//data为runAsync1函数的resolve结果
    return new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('runAsync2执行完成');
            resolve('runAsync2随便什么数据');
        }, 2000);
    });
}
```
#### 异常处理
使用catch处理异常
then方法有两个参数，第一个是处理resolve的回调，第二个是处理reject的回调，catch和then的第二个参数一样也是指定reject回调的。
以上面的列子说明：
1. 可以在then的第二个参数里面处理error
```javascript
runAsync1().then(data => {
    console.log(data)//runAsync1函数的resolve结果
    return runAsync2()
}).then(data => {
    console.log(data)//runAsync2函数的resolve结果
    return runAsync3()
}).then(data => {
    console.log(data) //runAsync3函数的resolve结果  
},err=>{
    console.log(err)
})

```
2. 也可以放在then方法的外面使用catch处理，效果和写在then的第二个参数里面一样。不过它还有另外一个作用：在执行resolve的回调（也就是上面then中的第一个参数）时，如果抛出异常了（代码出错了），那么并不会报错卡死js，而是会进到这个catch方法中
```javascript
runAsync1().then(data => {
    console.log(data)
    return runAsync2()
}).then(data => {
    console.log(data)
    return runAsync3()
}).then(data => {
    console.log(data)    
}).catch(err => { 
    console.log(err)
})

```
*注意，不管runAsync1、runAsync2、runAsync3出现异常，都会走到catch中，如果runAsync1出现异常，runAsync2、runAsync3都不会执行。
#### Promise.all使用（谁执行的慢以谁为准执行回调，但是返回值是所有的返回值集合）
并行执行一组异步操作，并且返回值是所有异步操作返回值的数组
```javascript
Promise
    .all([runAsync1(), runAsync2(), runAsync3()])
    .then(function(results){
        console.log(results);
});
```
#### Promise.race（谁执行的快以谁为准执行回调）
使用场景：可以用race给某个异步请求设置超时时间，并且在超时后执行相应的操作
```javascript
Promise
.race([runAsync1(), runAsync2(), runAsync3()])
.then(function(results){
    console.log(results);
});
```
###### 示例代码：
```javascript
//10秒之后执行
function timeout10(){
    var p = new Promise(function(resolve, reject){
        setTimeout(function(){
            resolve('success');
        }, 4000);
    });
    return p;
}

//延时函数，用于给请求计时
function timeout5(){
    var p = new Promise(function(resolve, reject){
        setTimeout(function(){
            reject('请求超时');
        }, 5000);
    });
    return p;
}

Promise
.race([timeout10(), timeout5()])
.then(function(results){
    console.log(results);
})
.catch(function(reason){
    console.log(reason);
});
```

参考帖子：http://www.cnblogs.com/lvdabao/p/es6-promise-1.html
