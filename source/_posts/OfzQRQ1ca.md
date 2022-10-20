---
title: 'Knex - 灵活轻便的 Node.js SQL 查询构建器'
date: 2021-01-13 13:26:57
tags: [nodejs,sql]
published: true
hideInList: false
feature: 
isTop: false
---
[每日开源代码](https://www.toutiao.com/a6874595191253271053/)

不管你承认与否，Node.js 都是一个后端语言平台。那么，操作数据库，这个后端最为常用的代码逻辑之一，Node.js 自然也需要拥有。我们当然也可以使用数据库驱动直接执行 SQL 语句，但我们往往需要更为高级 SQL 构建方式。Knex.js，为 Node.js 提供了多数据库后端的 SQL 查询构建的能力，可以更加方便地完成数据库查询和操作，并为更为高层次的 ORM 框架提供基础。

![Knex - 灵活轻便的 Node.js SQL 查询构建器](https://gitee.com/limeng1984/pstore/raw/master/blog/7a73fd535f36419899f45d89e7f3b6e4.png)

Knex.js库

## **简介**

Knex，是 knex 组织在 Github 上开源的 Node.js 的 SQL 查询构建器，项目位于 https://github.com/knex/knex，目前版本为 0.21.5。Knex 灵活轻便，支持多数据库后端，包括 MySQL、MSSQL、PostgreSQL 等，且支持：

- 数据库事务
- 连接池化
- 流式查询
- 包括 Promise 和回调形式的 API

![Knex - 灵活轻便的 Node.js SQL 查询构建器](https://gitee.com/limeng1984/pstore/raw/master/blog/e4cb057fe6cc4c67b9988604b33d5718-20210113132548787.png)

Knex.js Github

## **安装**

Knex 主要的目标环境的 Node.js，需求 Node.js 10+，可以使用 NPM 进行安装：

```bash
$ npm install knex --save
```

Knex 支持多种数据库后端，需要安装各数据库的驱动：

```bash
$ npm install pg
$ npm install sqlite3
$ npm install mysql
$ npm install mysql2
$ npm install oracledb
$ npm install mssql
```

## 示例

安装完成后，在 Node.js 项目中，引入 knex，并初始化客户端实例：

```javascript
var knex = require('knex')({
  client: 'mysql',
  connection: {
    host : '127.0.0.1',
    user : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test'
  }
});
```

其中 client 参数为数据库后端类型，而 connection 代表客户端的数据库连接，包括主机、用户名、密码和数据库等。 Knex 使用连接池的方式，优化与数据库的连接性能，使用 pool 参数定义连接池的大小：

```javascript
var knex = require('knex')({
    pool: { min: 0, max: 7 }
})
```

也可以使用 log 参数注册不同等级的日志记录器：

```javascript
var knex = require('knex')({
   log: {
    warn(message) {
    },
    error(message) {
    },
    deprecate(message) {
    },
    debug(message) {
    },
  }
});
```

![Knex - 灵活轻便的 Node.js SQL 查询构建器](https://gitee.com/limeng1984/pstore/raw/master/blog/8f3bbc30e3ff408aa4574aaa8d06f68e.png)

Knex.js文档

Knex.js 提供了数据库的 DDL 操作，使用 knex.schema 提供的方法实现。创建表，使用 createTable：

```javascript
knex.schema.createTable('users', function (table) {
  table.increments();
  table.string('name');
  table.timestamps();
})
```

createTable 的回调接受一个 table 参数，使用该 table 定义数据列，如 table.increments 提供了递增的整数 ID 列，table.string 提供了字符串列等，对应的 SQL 语句是：

```javascript
create table `users` (
    `id` int unsigned not null auto_increment primary key,
    `name` varchar(255),
    `created_at` datetime,
    `updated_at` datetime
)
```

对应的提供了 dropTable 和 dropTableIfExists 来实现表的删除：

```javascript
knex.schema.dropTable('users')
knex.schema.dropTableIfExists('users')
```

并提供了 knex.table 实现表定义的修改：

```javascript
knex.schema.table('users', function (table) {
  table.dropColumn('name');
  table.string('first_name');
  table.string('last_name');
})
```

Knex 提供了 SQL 查询的构建器。我们来看一个简单的例子：

```javascript
knex({ a: 'table', b: 'table' })
  .select({
    aTitle: 'a.title',
    bTitle: 'b.title'
  })
  .whereRaw('?? = ??', ['a.column_1', 'b.column_2'])
```

查询了两个表 a 和 b，并接 select 条件，进行连表查询。

Knex 最基本的查询构建是 select 方法：

```javascript
knex.select().from('books')
```

对应最简单的 select 语句

```sql
select * from `books`
```

在 select 方法中可以提供数据列来查询具体的列：

```javascript
knex.select('title', 'author', 'year').from('books')
```

并且，可以串联 where 方法，实现等值条件查询：

```javascript
knex('users').where({
  first_name: 'Test',
  last_name:  'User'
}).select('id')
```

Knex.js 提供了 join 方法，实现数据表的 join 查询：

```javascript
knex('users')
  .join('contacts', 'users.id', 'contacts.user_id')
  .select('users.id', 'contacts.phone')
```

Knex.js 还提供了聚合函数：

```javascript
knex('users').sum('products')
```

Knex.js 提供了 Promise 形式的异步接口，可以串联所有数据库操作：

```javascript
// Create a table
knex.schema
  .createTable('users', table => {
    table.increments('id');
    table.string('user_name');
  })

  // ...and another
  .createTable('accounts', table => {
    table.increments('id');
    table.string('account_name');
    table
      .integer('user_id')
      .unsigned()
      .references('users.id');
  })

  // Then query the table...
  .then(() =>
    knex('users').insert({ user_name: 'Tim' })
  )

  // ...and using the insert id, insert into the other table.
  .then(rows =>
    knex('accounts').insert({ account_name: 'knex', user_id: rows[0] })
  )

  // Query both of the rows.
  .then(() =>
    knex('users')
      .join('accounts', 'users.id', 'accounts.user_id')
      .select('users.user_name as user', 'accounts.account_name as account')
  )

  // map over the results
  .then(rows =>
    rows.map(row => {
      console.log(row)
    })
  )

  // Finally, add a .catch handler for the promise chain
  .catch(e => {
    console.error(e);
  });
```

Knex.js 还支持 TypeScript，为数据库操作提供了数据类型：

```javascript
interface User {
  id: number;
  name: string;
  age: number;
}

knex('users')
  .where('id')
  .first(); // Resolves to any

knex<User>('users') // User is the type of row in database
  .where('id', 1) // Your IDE will be able to help with the completion of id
  .first(); // Resolves to User | undefined
```

![Knex - 灵活轻便的 Node.js SQL 查询构建器](https://gitee.com/limeng1984/pstore/raw/master/blog/f659b94d772c4d3298383d9568eb1ed8.jpeg)

SQL数据库

## 总结

Knex.js 作为一个 Node.js 的 SQL 查询构建器，为 JS 提供了多数据库后端的操作，并提供了丰富的数据库操作和查询功能，使得开发者不必手动编写 SQL 语句，进而使用更为高级的 JS 方法进行查询的构建，为更为高层次的 ORM 框架提供了可靠的数据库操作，是一个质量较高的 Node.js 数据库相关的基础库。