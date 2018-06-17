---
title: js实现插入排序算法
date: 2018-03-22 16:58:40
tags: [插入排序]
category: [算法]
---

原理：从下标1开始，current=array[i],对数组的前i-1项进行检查，如果存在大于current值，将此值往后移，赋值给下一项

以下是js代码的实现：
```javascript
function sort(arr) {
    for (let i = 1; i < arr.length; i++){
        let current = arr[i]
        var index = i;//记录要被插入的下标
        for (let j = i-1; j >= 0; j--){
            if (current < arr[j]) { 
                arr[j + 1] = arr[j]
                index = j
            }
        }
        arr[index] = current
    }
    return arr
}
let arr = [34,8,64,51,32,21]
console.log(sort(arr))
```
