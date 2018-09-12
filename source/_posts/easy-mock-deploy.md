---
title: 使用docker部署easy-mock
date: 2018-09-04 15:00:49
tags: [docker easy-mock]
category: [docker]
---

easy-mock 是一个开源的持久的服务，可以快速生成模拟数据并提供可视化视图，使用 easy-mock 在实际开发中可以更好的将前后端开发分离并行开发，避免前端一直等待后台接口的尴尬局面，接下来演示一下如何使用 docker 部署一个 easy-mock

#### 克隆 easy-mock 源码到本地

```
git clone https://github.com/easy-mock/easy-mock.git
```

#### build 镜像

进入项目目录，在根路径下创建 Dockerfile 文件,文件内容如下

```
FROM keymetrics/pm2:latest-alpine

# Bundle APP files
COPY . ./app

# Install app dependencies
ENV NPM_CONFIG_LOGLEVEL warn
ENV PORT 7300
ENV NODE_ENV production
# Show current folder structure in logs
RUN ls -al

WORKDIR ./app

EXPOSE 7300

CMD [ "pm2-runtime", "start", "pm2.json" ]
```

项目使用 pm2 启动，因此在根路径下创建一个 pm2 的配置文件 pm2.json,内容如下,具体每个配置的意义可以查阅 pm2 文档

```
{
    "apps": [
        {
            "script": "./app.js",
            "name": "easy-mock",
            "interpreter": "node",
            "env": {
                "NODE_ENV": "production"
            },
            "out_file": "easy-mock.log",
            "error_file": "easy-mock-err.log",
            "watch": "./config/",
            "restart_delay": 2000
        }
    ]
}
```

准备好以上两个文件后开始使用 docker 命令 Build 镜像

```
docker build -t hub.c.163.com/dandanwu/easy-mock .
```

build 成功之后使用 docker images 可以看到 build 出来的脚本

#### 运行

easy-mock 的运行依赖于 mongodb 以及 redis，因此需要启动 mongo、redis,都是用 docker 启动

```
redis
docker run --name redis --restart always -p 6379:6379 -v /root/docker-data/redis/data:/data  -d redis redis-server --appendonly yes

mongo
docker run --name mongo --restart always -v  /root/docker-data/mongo/data:/data/db -d -p 27017:27017 mongo
```

由于 mongo 和 redis 我们使用了 docker 启动，因此需要修改 easy-mock 的源码中的配置文件 config/default.json

```
"db": "mongodb://mongo/easy-mock",
  "redis": {
    "keyPrefix": "[Easy Mock]",
    "port": 6379,
    "host": "redis",
    "password": "",
    "db": 0
  },
```

最后运行 easy-mock 镜像，link 到 mongo 以及 redis

```
docker run  -d -p 7300:7300 --restart always --link=mongo:mongo --link=redis:redis hub.c.163.com/dandanwu/easy-mock
```

成功后访问 http://localhost:7300 可以看到以下界面，表明部署成功！
![avatar](/images/1.jpg)
部署成功后默认会有一个演示的项目
![avatar](/images/2.jpg)
