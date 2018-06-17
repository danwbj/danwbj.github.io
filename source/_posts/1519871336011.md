---
title: js对象引用赋值引发数据可变的解决方案
date: 2018-03-01 10:28:56
tags: [引用赋值]
category: [javascript]
---

### js赋值（引用赋值）
```javascript
const student1 = {
    school: 'Baidu',
    name: 'HOU Ce',
    birthdate: '1995-12-15',
}

const changeStudent = (student, newName, newBday) => {
    const newStudent = student;
    newStudent.name = newName;
    newStudent.birthdate = newBday;
    return newStudent;
}

const student2 = changeStudent(student1, 'YAN Haijing', '1990-11-10');

// both students will have the name properties
console.log(student1, student2);
// Object {school: "Baidu", name: "YAN Haijing", birthdate: "1990-11-10"} 
// Object {school: "Baidu", name: "YAN Haijing", birthdate: "1990-11-10"}
```
创建了一个新的对象student2，但是老的对象student1也被改动了

### 深拷贝与浅拷贝的区别
以Object.assign(浅拷贝)为列：
```javascript
var obj1 = { a: 0, b: { c: 0 } }
var obj2 = Object.assign({}, obj1)
obj2.a = 1
obj2.b.c = 1
console.log(obj1) //{ a: 0, b: { c: 1 } }
console.log(obj2) //{ a: 1, b: { c: 1 }
```
为什么改变属性a不是指向同一个引用，而b.c指向了同一个引用?因为b不是简单地数据类型，Object.assign拷贝的时候是浅拷贝，只复制了{ c: 0 }的引用变量b,而a:1是简单类型，拷贝的时候拷贝的是值，所以当obj2.b.c改变的时候，因为obj1.b和obj2.b指向的是同一个内存地址，所以obj1.b.c的值也发生了改变。
如果是深拷贝obj1.b.c的值就不会因为obj2.b.c改变而改变

### 解决方案（创建不可变数据）

 ##### 使用ES6当中的解构赋值(浅拷贝，而不是深拷贝)
```javascript
const changeStudent = (student, newName, newBday) => {
    return {
        ...student, // 使用解构
        name: newName, // 覆盖name属性
        birthdate: newBday // 覆盖birthdate属性
    }
}

const student2 = changeStudent(student1, 'YAN Haijing', '1990-11-10');

// both students will have the name properties
console.log(student1, student2);
// Object {school: "Baidu", name: "HOU Ce", birthdate: "1995-12-15"} 
// Object {school: "Baidu", name: "YAN Haijing", birthdate: "1990-11-10"}
```
 ##### Objects.assign(浅拷贝，而不是深拷贝)
```javascript
const changeStudent = (student, newName, newBday) => Object.assign({}, student, {name: newName, birthdate: newBday})

const student2 = changeStudent(student1, 'YAN Haijing', '1990-11-10');

console.log(student1, student2);
```
 ##### 使用第三方库
 
 比如lodash中的merge函数


注意：对于数组来说，它里面的 .map, .filter或者.reduce函数不会改变原数组，而是产生并返回一个新数组。这和纯函数的思想不谋而合。


原文连接：https://juejin.im/post/58d0ff6f1b69e6006b8fd4e9