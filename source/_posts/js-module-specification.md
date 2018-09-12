---
title: js中的模块规范
date: 2018-07-18 14:32:15
tags: [模块规范]
category: [javascript]
---

## js 中的模块规范

#### CommonJS

主要应用于服务器，实现同步加载

模块输出方式：exports 和 module.exports

模块输入方式：require

```js
var math = require("math");

math.add(2, 3);
```

前者支持动态导入，也就是 require(${path}/xx.js)

node.js 的模块系统，就是参照 CommonJS 规范实现的

#### AMD

AMD 规范应用于浏览器，如 requirejs，为异步加载

模块输出方式：define(['dependency'], callback);

模块输入方式：require([module], callback); 第一个参数为需要加载的模块名称，第二个参数为加载完模块之后需要执行的回调函数

```js
require(["math"], function(math) {
  math.add(2, 3);
});
```

主要有两个 Javascript 库实现了 AMD 规范：require.js 和 curl.js

#### CMD

同步加载方案

CMD 有个浏览器的实现 SeaJS

```js
// 定义模块  myModule.js
define(function(require, exports, module) {
  var $ = require("jquery.js");
  $("div").addClass("active");
});

// 加载模块
seajs.use(["myModule.js"], function(my) {});
```

#### ES6 模块规范

浏览器和服务器通用的模块解决方案。

不支持动态导入，但是已有提案

模块输出方式：export 和 export default

模块输入方式：import ... from ...
