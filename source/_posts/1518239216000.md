---
title: mac下使用tree命令
date: 2018-02-10 13:06:56
category: [mac]
tags: [mac,shell]
---

mac下默认是没有 tree命令的，不过我们可以使用find命令模拟出tree命令的效果

如显示当前目录的 tree 的命令
```
find . -print | sed -e 's;[^/]*/;|____;g;s;____|; |;g'
```

使用alias 指定别名，将它变成一个命令

.bash_profile文件增加以下代码：

```
alias tree="find . -print | sed -e 's;[^/]*/;|____;g;s;____|; |;g'"
```
