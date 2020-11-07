---
title: Sequelize的一些使用心得
tags: 
      - Sequelize
      - NodeJS
      - 后端
---

Sequelize的一些使用心得
=================================

〇、前言
-------------------------

- 由于这个项目（~~自娱自乐项目~~）的需求要使用NodeJS，但是NodeJS是使用拼接的方式来实现数据库的查询，所以我认为它不太安全，于是我就使用了Sequelize这个ORM框架来与数据库连接。<!--more-->

- Sequelize的介绍：“*Sequelize 是一个基于 promise 的 Node.js ORM, 目前支持 Postgres, MySQL, MariaDB, SQLite 以及 Microsoft SQL Server. 它具有强大的事务支持, 关联关系, 预读和延迟加载,读取复制等功能。*
- *Sequelize 遵从 语义版本控制. 支持 Node v10 及更高版本以便使用 ES6 功能.*”——[Sequelize Docs 中文版][1]


一、心得体会
----------------

### 文档与手册

- Sequelize的中文文档还是很详细的，就是有些不够详细具体，不过虽然Sequelize这个框架更迭了这么多个版本，其内在的函数还是没有太大的改变，如果想要粗略的学习一下这个框架的Api，那我建议大概翻阅一下我上面贴的那份链接就可以。**不过如果想要查询手册的话，我建议这个网站**——[Sequelize 中文API文档－1. 快速入门、Sequelize类][2]——这个网站的右边阅读排行还有2、3、4、5等等其他十至十多章，可以通过链接快速跳转，作为一个使用手册快速查询还是很不错的。

- 如果觉得中文文档的例子不够清晰明了，还可以去这个[英文官方网站][3]啃生肉，这里面的例子足够清晰明了，就是可能英文看着头疼一些。

### 上手使用

#### 配置分层

- 这个框架上手使用是很快的，翻阅完文档之后，配合Stack Overflow与其他七七八八的中文网站，你很容易就可以把关于Sequelize的配置、分层以及使用方法摸得差不多了。我在这里也讲解一下我对这个框架的分层理解吧。

- 如果你之前使用过Java的SpringBoot之类的SpringMVC框架，那么你肯定能一下就上手了解了，虽然和Spring的分层有些许不同，不过大同小异。我将使用这个Sequelize框架的方法分为持久层与实体层（Model类），首先通过**实体层映射数据库字段**，然后**通过实体层的类来调用持久层的方法**，这一步是可以通过业务层（Service类）来实现的，然后再将业务层返回的数据返回给路由接口层（通过Koa、Express等框架的路由进行实现）。

- 所以基本上NodeJS的后端是这样的逻辑情况，和SpringBoot类似：

- 前端->后端（路由接口层->业务层->持久层->数据库）然后原路返回。

- 需要注意的地方有一点，如果在使用define()方法或者init()方法时**，表名若是使用单数，则在生成数据库表的时候会自动变成复数形式**，所以如果提前建好了数据库表的话，在建立映射的时候就要注意表名要写成复数形式，并且在表中还要添加createAt、updateAt和deleteAt（若是软删除则必须）字段。

### 常用方法

- 由于这个项目还是比较简单的，所以使用的方法也不是很多，就大概介绍一下增删改，然后详细介绍一下查询的方法。

#### 增删改

- **增**的时候会在createAt和updateAt处添加插入该数据时的时间，**删**的时候（软删除）会在deleteAt处添加插入该数据时的时间，**改**的时候会在updateAt处添加插入该数据时的时间。

- 增：一般都是用create()，还有一个不会保存进数据库表的方法build()（~~不知道有什么用XD~~）

- 删：使用destroy()方法，执行成功后返回被删除的行数。

- 改：使用update()方法。

### 普通查询

- 查询有最简单的findAll()方法，可以看做是select * from table。

- 然后是findAndCountAll()方法，这个方法主要是拿来分页查询，效果如下：

```
Model.findAndCountAll({
  where: {...},
  limit: 12,
  offset: 12
}).then(res => {
  ...
})
```

- 然后返回的res格式如下：

```
{
    count: Number,
    rows: Array,
}
```

- 如果只需要查询一条信息，则可以使用findOne()方法，使用方法和前两个方法类似。

### 关联查询

- 一对一的关联查询也很简单，使用方法如下：

```
ModelA.belongsTo(ModelB, {as: 'ModelB', foreignKey: 
'ModelBId', targetKey: 'ModelBId'});

ModelA.findAndCountAll({
    // include关键字表示关联查询
    include: [{ 
        model:ModelB,// 指定关联的model
        as:'ModelB',// 由于前面建立映射关系时为ModelB表起了别名，那么这里也要与前面保持一致，否则会报错
        attributes: ['ModelBName'],// 这里的attributes属性表示查询ModelB表的字段
    }]
}).then(res => {
  ...
})
```

- 其中belongsTo的方法中，foreignKey指的是该表中的外键字段名，targetKey指的是外键对应的那个表中的字段名。

- 若是遇到了需要进行多个外键的查询的情况，那么也是一样的，因为include是一个数组，我们只需要向数组中加入新的对象即可：

```
ModelA.belongsTo(ModelB, {as: 'ModelB', foreignKey: 
'ModelBId', targetKey: 'ModelBId'});

ModelA.belongsTo(ModelC, {as: 'ModelC', foreignKey: 
'ModelCId', targetKey: 'ModelCId'});

ModelA.findAndCountAll({
    include: [{ 
        model:ModelB,
        as:'ModelB',
        attributes: ['ModelBName'],
    },{ 
        model:ModelC,
        as:'ModelC',
        attributes: ['ModelCName']
    }]
}).then(res => {
  ...
})
```

- 若是多个外键均指向同一个表的同一个字段，这种情况和前一种情况其实是一样的，为这些外键建立多次belongsTo关联到那一个表的字段即可，其他的都和上面情况一致。

- 由于我这个项目较为简单，还未遇到特别复杂的关联查询和联合查询，所以若是遇到了更复杂的联合查询，请查看手册的[这一章][4]。


二、说在最后的话
----------------

- 这一个前后端项目做下来，我感觉对于这些个框架的使用逻辑理解得更深入了，如果有时间就好好看一下它们的源码，相信那个时候会更得心应手，不过这么🕊的人会有那个时间嘛XD。对于数据库的了解也更深了一些，最起码知道如果后端想当懒狗把事情推给前端，那我也可以从那些个层次与逻辑出发让他别坑我~

- ~~终于有时间写点心得体会了（泣）~~

<br>
上传于2020-05-13


  [1]: https://demopark.github.io/sequelize-docs-Zh-CN/
  [2]: https://itbilu.com/nodejs/npm/VkYIaRPz-.html
  [3]: https://sequelize.org/v5/
  [4]: https://itbilu.com/nodejs/npm/EJarwPD8W.html