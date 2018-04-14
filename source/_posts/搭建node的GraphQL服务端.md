---
title: 搭建node的GraphQL服务端
date: 2018-04-15 14:50:06
tags: [GraphQL]
category: [node]
---
GraphQL和传统的REST API相比查询更加灵活，GraphQL 简单来说就是：取哪些数据是由client来决定，GraphQL 中，client 直接对 server说想要什么数据，server负责精确的返回目标数据，
以nodejs为例搭建一个简单地服务端。 
#### GraphQL工作的大致流程
- 描述你的数据
```javascript
type Project {
  name: String
  tagline: String
  contributors: [User]
}
```
- 请求你所要的数据
```
{
  project(name: "GraphQL") {
    tagline
  }
}
```
- 得到可预测的结果
```
{
  "project": {
    "tagline": "A query language for APIs"
  }
}
```
#### 需求分析设计 
用户可以发表作品，可以对其他用户发表的作品投票
#### 使用apollo-server-express初始化一个服务
创建目录初始化项目
```
mkdir graphql_node_demo
cd graphql_node_demo
npm init
```

安装依赖
```
npm i express body-parser apollo-server-express graphql graphql-tools --save
```
在根目录下创建启动文件index.js
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const { graphqlExpress, graphiqlExpress } = require('apollo-server-express');
const { createServer } = require('http');
const PORT = process.env.PORT || 3000;
const NODE_ENV = process.env.NODE_ENV || 'development'

const start = async () => {
  var app = express();

  const buildOptions = async (req, res) => {
    return {
      context: {}
    };
  };
  app.use('/graphql', bodyParser.json(), graphqlExpress(buildOptions));


  app.use('/graphiql', graphiqlExpress({
    endpointURL: '/graphql',
  }));

  const server = createServer(app);
  server.listen(PORT, () => {
    console.log(`fmlp.${NODE_ENV} GraphQL server running on port ${PORT}`)
  });
};

start();
```
现在可以启动服务尝试一下
```
node index.js
```
在浏览器访问http://localhost:3000/graphiql 可以看到会报错，是因为还没有添加schema
#### 增加schema
创建一个schema文件夹，并在文件夹里面抽创建两个文件，index.js、resolvers.js
index.js里面定义我们的type，也就是描述数据，resolvers.js定义对返回数据的解析函数
```
mkdir schema
cd schema
touch index.js
touch resolvers.js
```
schema/index.js文件中我们定义三个type
```javascript
const {makeExecutableSchema} = require('graphql-tools');
const resolvers = require('./resolvers');
// 在这里定义所有的类型
const typeDefs = `
  type Link {
    id: ID!
    url: String!
    description: String!
    postedById:String!
    postedBy: User
  }
  type Query {
    allLinks: [Link!]! //定义了Query类型的查询，里面有一个查询所有Links,他的返回值是一个Link类型的数组
  }
  type User {
    id: ID!
    name: String!
    email: String
  }
`;

```
schema/resolvers.js文件中去执行查询并且返回客户端
```javascript

let links = [
    {
        id: 1,
        url: "http://url1",
        description: "link1",
        postedById:"userid1"
    }
]
module.exports = {
    Query: {
        allLinks: (_, data) => {
            return links
        },
    }
};
```
ok 启动服务，刷新浏览器成功
输入我们想要的查询

```
//此处需要哪些字段写哪些字段
{
  allLinks {
    id
    url
    description
    postedById
  }
}
```
就可以返回
```json
{
  "data": {
    "allLinks": [
      {
        "id": "1",
        "url": "http://url1",
        "description": "link1",
        "postedById": "userid1"
      }
    ]
  }
}
```
这就是一个简单的graphql查询     
另外Link中有一个字段postedBy是一个User类型，我们可以在resolvers.js中增加对Link中的User字段的解析，使Link列表可以返回每个Link是哪个用户发表的，这样实现了原始的关联查询   
在resolvers.js中增加以下代码：
```javascript
Link: {
        postedBy: ({ postedById }, data) => {
            //此处需要根绝postedById查询数据库返回对用的user
            return {
                id: postedById,
                name: "zhangsan",
                email: "zhangsan@11.com"
            }
        }
    },
```
由于我们没有对接真实的数据库，所以此处省略数据库查询的过程   
刷新浏览器   
现在的查询就可以是这样的
```
{
  allLinks {
    id
    url
    description
    postedById
    postedBy{
      id
      name
      email
    }
  }
}
```
返回值也变化了
```json
{
  "data": {
    "allLinks": [
      {
        "id": "1",
        "url": "http://url1",
        "description": "link1",
        "postedById": "userid1",
        "postedBy": {
          "id": "userid1",
          "name": "zhangsan",
          "email": "zhangsan@11.com"
        }
      }
    ]
  }
}
```
这里只是实现了简单地查询操作，另外还有一些创建编辑删除的操作是需要用到Mutation类型来实现，具体不写出来，直接看github源码
https://github.com/danwbj/graphql-server-apollo.git




