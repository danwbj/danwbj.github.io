---
title: 常见算法总结
date: 2018-09-12 10:54:29
tags: [算法]
category: [javascript]
---

#### 自定义实现一个 js 数组的 map 函数

---

```javascript
function map(arr, fn) {
  let newlist = [];
  for (let i = 0; i < arr.length; i++) {
    newlist[i] = fn(arr[i], i, arr);
  }
  return newlist;
}
console.log(
  map(list, item => {
    return item + 1;
  })
);
```

#### 自定义实现一个 js 数组的 filter 函数

---

```javascript
let list = [1, 2, 3, 4, 5, 6];
function filter(arr, fn) {
  let newlist = [];
  for (let i = 0; i < arr.length; i++) {
    if (fn(arr[i], i, arr)) {
      newlist.push(arr[i]);
    }
  }
  return newlist;
}
console.log(
  filter(list, item => {
    return item > 2;
  })
);
```

#### 输出 1-100 之间的素数

---

素数：除了 1 和它本身外没有其他因数，即，除了 1 和它本身外不能被其他数整除
1---不是素数
2---最小的素数
3---2（判断是否能被 2 整除）
4---2，3（判断是否能被 2,3 整除）
5---2，3，4（判断是否能被 2,3,4 整除）
6---2，3，4，5（判断是否能被 2,3,4,5 整除）

```javascript
for (let i = 2; i <= 100; i++) {
  let flag = true;
  for (let j = 2; j < i; j++) {
    if (i % j == 0) {
      flag = false;
      break;
    }
  }
  if (flag) {
    console.log(i);
  }
}
```

#### js 实现冒泡排序算法

---

原理：将前后两个数进行比较，较大或者较小的往后放
例如：let numbers={ 1,5,3,6,4,9,8,0,7,2}
第一轮比较：
第一次比较：1，5，3，6，4，9，8，0，7，2 第一个数不大于第二个数，不调换位置

第二次比较：1，3，5，6，4，9，8，0，7，2 第二个数大于第三个数，调换位置

第三次比较：1，3，5，6，4，9，8，0，7，2 第三个数不大于第四个数，不调换位置

第四次比较：1，3，5，4，6，9，8，0，7，2 第四个数大于第五个数，调换位置
.
.
.
以此类推 第九次比较：1，3，5，4，6，8，0，7，2，9 第九个数大于第十个数，调换位置

第二轮比较：
比较次数 8 次

第三轮比较：
比较次数 7 次
.
.
.
以此类推 第九轮比较 1 次
从上面的分析我们可以看出我们排 10 个数需要比较九轮，每一轮比较由 9 次递减到 1 次

以下是 js 代码的实现：

```javascript
var arr = [2, 10, 3, 4, 1, 7, 5, 6, 9, 8];
function sort(arr) {
  for (let i = 0; i < arr.length - 1; i++) {
    for (let j = 0; j < arr.length - 1 - i; j++) {
      let temp = arr[j];
      if (arr[j] > arr[j + 1]) {
        arr[j] = arr[j + 1];
        arr[j + 1] = temp;
      }
    }
  }
}
sort(arr);
console.log(arr);
```

#### js 实现插入排序算法

---

原理：从下标 1 开始，current=array[i],对数组的前 i-1 项进行检查，如果存在大于 current 值，将此值往后移，赋值给下一项

```javascript
function sort(arr) {
  for (let i = 1; i < arr.length; i++) {
    let current = arr[i];
    var index = i; //记录要被插入的下标
    for (let j = i - 1; j >= 0; j--) {
      if (current < arr[j]) {
        arr[j + 1] = arr[j];
        index = j;
      }
    }
    arr[index] = current;
  }
  return arr;
}
let arr = [34, 8, 64, 51, 32, 21];
console.log(sort(arr));
```

#### 爬楼梯问题（斐波拉契数列）

---

问题：楼梯总共有 N 级，若每次只能跨上一级或二级或者三级，要走上第 N 级，共有多少种走法？
思路：
N = 1 的时候只有 1 种方法。
N = 2 的时候有 2 方法。
N = 3 的时候有 4 方法。
N = K 的时候，可以先走到 k-1 那里，然后向前走一阶，或者先走到 k-2 那里然后前走两阶
所以后一个答案是前两个的答案之和。

```javascript
function f(n) {
  if (n < 3) return n;
  if (n == 3) {
    return 4;
  }
  return f(n - 1) + f(n - 2) + f(n - 3);
}
```
