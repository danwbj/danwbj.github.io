---
title: Linux环境下安装node
date: 2018-04-14 11:39:09
tags: [node,linux]
category: [node]
---
#### 下载
```
wget https://npm.taobao.org/mirrors/node/v8.9.3/node-v8.9.3-linux-x64.tar.xz
```
#### 解压
解压后的文件需要保留，压缩文件可以删除
```
tar -xzvf node-v8.9.3-linux-x64.tar.gz
```
#### 创建软连接
```
ln -s /node-v8.9.3-linux-x64/bin/node /usr/local/bin/node
ln -s /node-v8.9.3-linux-x64/bin/npm/usr/local/bin/npm
```
#### 测试安装是否成功
```
node -v  出现版本号即安装成功
npm -v   出现版本号即安装成功
```

ok!
