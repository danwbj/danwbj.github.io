---
title: nodejs模块机制理解
date: 2018-07-29 17:01:27
tags: [node, commonjs]
category: [node]
---

#### CommonJS 规范

CommonJS 出发点: js 没有模块系统、缺乏包管理系统、标准库较少

CommonJS 规范涵盖了模块，二进制 ,Buffer,字符集编码,I/O 流, 进程环境,文件系统,接】套接字,单元测试,web 服务器网关接口, 包管理等。

CommonJS 对模块的定义包含：  
模块引用:（var math = require('math')）  
模块定义:  
一个文件就是一个模块,将方法挂在到 exports 对象上做法属性定义导出  
module 对象代表模块本身，exports 是 module 的属性  
模块标志:  
require('math')方法的参数，可以是字符串，相对路径，绝对路径

Node 中模块分类: node 提供的模块（核心模块）、用户编写的模块（文件模块）

模块引入的步骤：

1. 路径分析
2. 文件定位
3. 编译执行

优先从缓存加载

核心模块部分在 node 源代码编译时候已被编译进了二进制执行文件，Node 启动时候被加载进内存，引入时候无需文件定位，编译执行，速度最快，仅次于缓存加载

##### 路径分析

根据模块标识符分析，不同标识符分析方式不同

标识符分类：

1. 核心模块（http,fs，path）  
   已被编译为二进制代码，加载速度最快
2. 以.或者../开头的相对路径  
   将路径转换为真实路径，以路径为索引加载，将编译执行后的结果放入缓存中，加载速度仅次于核心模块
3. 非路径形式的文件模块（express）  
   使用模块路径查找策略，会生成一个数组，生成规则是当前路径的 node_modules、父目录下的 node_modules、父目录的父目录的 node_modules，依次往上查找，当前文件路径越深，查找越费时，这种文件模块的查找最慢

##### 文件定位

扩展名分析顺序：.js、.json、.node(C/C++编写的模块)

分析查找标识符过程中如果没有得到一个文件却得到了一个目录，此时查找顺序是：  
1、找到 package.json  
2、通过 JSON.parse()解析出包描述对象，找到 main 属性指定的文件名进行定位  
3、若第二步的文件名没有扩展，则进行扩展名分析  
4、如查找 main 指定的文件错误，或没有 package.json 则，默认查找 index 文件，依次查找 index.js,index.josn,index.node  
5、若以上四步定位失败，则进入下一个模块路径查找

##### 编译

.js 文件 （fs 模块读取编译执行）  
 编译的时候对 js 代码进行头尾包装，以实现作用域隔离

```javascript
(function(exports,require,module,_filename,_dirname){
	var math  = require('math')
	exports.area = function (radius){
		retutn Math.PI * radius * radius
	}
})
```

.json（fs 读取后，通过 JSON.parse()解析返回结果）  
 .node(c/c++编写的扩展模块，通过 dlopen()加载最后编译)
