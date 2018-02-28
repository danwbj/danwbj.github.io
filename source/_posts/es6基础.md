---
title: es6基础
date: 2018-02-26 10:31:14
tags: [es6]
category: [javascript]
---
####  let命令

let有块级作用域，let声明的变量只在它所在的代码块有效

var有变量提升现象，let没有变量提升

let暂时性死区 
```javascript
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}

```
ES6 明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错
let不允许重复声明
一道面试题：
```javascript
var funcs = []
for (var i = 0; i < 10; i++) {
    funcs.push(function() { console.log(i) })
}
funcs.forEach(function(func) {
    func()
})
```
以上代码会输出10次数组10，如果想要输出0-9，两种解决方案：
```javascript
    // ES5告诉我们可以利用闭包解决这个问题
    var funcs = []
    for (var i = 0; i < 10; i++) {
        func.push((function(value) {
            return function() {
                console.log(value)
            }
        }(i)))
    }
    // es6
    for (let i = 0; i < 10; i++) {
        func.push(function() {
            console.log(i)
        })
    }
```

#### 模板字符串

字符串拼接

```javascript
//es5 
var name = 'lux'
console.log('hello' + name)
//es6
const name = 'lux'
console.log(`hello ${name}`) //hello lux
```
es6提供的常用的字符串方法

```javascript
// includes：判断是否包含然后直接返回布尔值
let str = 'hahay'
console.log(str.includes('y')) // true

// repeat: 获取字符串重复n次
let s = 'he'
console.log(s.repeat(3)) // 'hehehe'
```

#### 函数
默认参数
```javascript
//es5设置参数默认值
//这种方式如果num=0就会出现num的值被默认200覆盖
    function action(num) {
        num = num || 200
        //当传入num时，num为传入的值
        //当没传入参数时，num即有了默认值200
        return num
    }
//es6设置默认参数
    function action(num = 200) {
        console.log(num)
    }
    action() //200
    action(300) //300
```
箭头函数
> 三个特点:   
1. 不需要function关键字来创建函数  
2. 省略return关键字 
3. 继承当前上下文的 this 关键字
  
```javascript
//例如：
    [1,2,3].map( x => x + 1 )
    
//等同于：
    [1,2,3].map((function(x){
        return x + 1
    }).bind(this))
```

#### 拓展的对象功能
对象初始化简写
```javascript
let name = 'danw'
let age = 27
var d = {name:name,age:age}

//以上代码es6可以简写为：
let name = 'danw'
let age = 27
var d = {name,age}
```
对象初始化中方法赋值的简写
```javascript
const people = {
    name: 'lux',
    getName: function() {
        console.log(this.name)
    }
}
//以上代码简写如下：
const people = {
    name: 'lux',
    getName () {
        console.log(this.name)
    }
}
```
Object.assign()实现对象浅复制
```javascript
var objA = {a：1,b:2}
const obj = Object.assign({}, objA)
obj.c = 3
console.log(obj) //{a：1,b:2,c:3}
console.log(objA) //{a：1,b:2}  objA的值不会被改变
```

#### 更方便的数据访问--解构
```javascript
const people = {
    name: 'lux',
    age: 20
}
const name = people.name
const age = people.age
console.log(name + ' --- ' + age)

//对象解构取值
const {name,age} = people
//数组结构取值
const color = ['red', 'blue']
const [first, second] = color
```

#### Spread Operator 展开运算符（...）
```javascript
//数组
const color = ['red', 'yellow']
const colorful = [...color, 'green', 'pink']
console.log(colorful) //[red, yellow, green, pink]

//对象
const alp = { fist: 'a', second: 'b'}
const alphabets = { ...alp, third: 'c' }
console.log(alphabets) //{ "fist": "a", "second": "b", "third": "c"

```

#### import 和 export
用法总结
- 当用export default people导出时，就用 import people 导入（不带大括号）
- 一个文件里，有且只能有一个export default。但可以有多个export。
- 当用export name 时，就用import { name }导入（记得带上大括号）
- 当一个文件里，既有一个export default people, 又有多个export name 或者 export age时，导入就用 import people, { name, age } 
- 当一个文件里出现n多个 export 导出很多模块，导入时除了一个一个导入，也可以用import * as example

#### Promise
思考题
```javascript
setTimeout(function() {
      console.log(1)
    }, 0);
    new Promise(function executor(resolve) {
      console.log(2);
      for( var i=0 ; i<10000 ; i++ ) {
        i == 9999 && resolve();
      }
      console.log(3);
    }).then(function() {
      console.log(4);
    });
    console.log(5);
//输出：2 3 5 4 1

```
> 类似面试题：https://zhuanlan.zhihu.com/p/25407758
