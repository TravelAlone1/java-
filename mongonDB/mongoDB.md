[TOC]

# mongoDB

### MongoDB是什么？

MongoDB 是免费开源的跨平台 NoSQL 数据库

###  概述

是一种跨平台，面向文档的数据库。

主要的运行方式主要基于两个概念：集合（collection）与文档（document）

### 好处

用它创建的数据库可以试想高性能、高可用性，并且能够轻易扩展。

### 数据库

数据库是集合的容器。每一个数据库都在文件系统中有自己的一组文件，一个MongoDB服务器通常有多个数据库。

### 集合

集合就是 MongoDB 文档。它相当于关系型数据库（RDBMS）中表这种概念。

## 文档

文档就是一组键-值对。文档有着动态的模式，这意味着同一集合内的文档不需要具有同样的字段或结构。

下表展示了关系型数据库与 MongoDB 在术语上的对比：

| 关系型数据库 | MongoDB                              |
| :----------- | :----------------------------------- |
| 数据库       | 数据库                               |
| 表           | 集合                                 |
| 行           | 文档                                 |
| 列           | 字段                                 |
| 表 Join      | 内嵌文档                             |
| 主键         | 主键（由 MongoDB 提供的默认 key_id） |

# 基础语法

## 创建数据库

### 语法格式

`use` 语句的基本格式如下：

```mysql
use DATABASE_NAME
```

### 范例

创建一个名为 的数据库，使用 `use` 语句如下：

```mysql
>use mydb
switched to db mydb
```

使用命令 `db` 检查当前选定的数据库。

```mysql
>db
mydb
```

使用命令 `show dbs` 来检查数据库列表。

```mysql
>show dbs
local     0.78125GB
test      0.23012GB
```

刚创建的数据库（mydb）没有出现在列表中。为了让数据库显示出来，至少应该插入一个文档。

```mysql
>db.movie.insert({"name":"tutorials point"})
>show dbs
local      0.78125GB
mydb       0.23012GB
test       0.23012GB
```

## 删除数据库

### dropDatabase() 方法

dropDatabase() 方法

MongoDB 的 `dropDatabase()` 命令用于删除已有数据库。

### 语法格式

`dropDatabase()` 命令的语法格式如下：

```mysql
db.dropDatabase()
```

它将删除选定的数据库。如果没有选定要删除的数据库，则它会将默认的 test 数据库删除。

### 范例

如果想删除新数据库 `<mydb>`，如下面这样使用 `dropDatabase()` 方法：

```mysql
>use mydb
switched to db mydb
>db.dropDatabase()
>{ "dropped" : "mydb", "ok" : 1 }
>
```

## 创建集合

## createCollection() 方法

在 MongoDB 中，创建集合采用 **db.createCollection(name, options)** 方法。

### 语法格式

`createCollection()` 方法的基本格式如下：

```mysql
db.createCollection(name, options)
```

在该命令中，`name` 是所要创建的集合名称。`options` 是一个用来指定集合配置的文档。

| 参数    | 类型   | 描述                               |
| :------ | :----- | :--------------------------------- |
| name    | 字符串 | 所要创建的集合名称                 |
| options | 文档   | 可选。指定有关内存大小及索引的选项 |

参数 options 是可选的，所以你必须指定的只有集合名称。下表列出了所有可用选项：

### 坑

从3.2.0开始，不建议使用集合创建选项`autoIndexId`。从3.4开始，该选项将被删除。
在3.2中，服务器将支持该选项并在日志中打印警告。如果从外壳程序运行，它将在外壳程序中打印附加到命令结果的“注释”。

| 字段        | 类型 | 描述                                                         |
| :---------- | :--- | :----------------------------------------------------------- |
| capped      | 布尔 | （可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。 **当该值为 true 时，必须指定 size 参数。** |
| autoIndexID | 布尔 | （可选）如为 true，自动在 _id 字段创建索引。默认为 false。   |
| size        | 数值 | （可选）为固定集合指定一个最大值（以字节计）。 **如果 capped 为 true，也需要指定该字段。** |
| max         | 数值 | （可选）指定固定集合中包含文档的最大数量。                   |

在插入文档时，MongoDB 首先检查固定集合的 size 字段，然后检查 max 字段。

### 范例

不带参数的 `createCollection()` 方法的基本格式为：

```mysql
>use test
switched to db test
>db.createCollection("mycollection")
{ "ok" : 1 }
>
```

可以使用 `show collections` 来查看创建了的集合。

```mysql
>show collections
mycollection
system.indexes
```

下面是带有几个关键参数的 `createCollection()` 的用法：

```mysql
>db.createCollection("mycol", { capped : true, size : 6142800, max : 10000 } )
{ "ok" : 1 }
>
```

在 MongoDB 中，你不需要创建集合。当你插入一些文档时，MongoDB 会自动创建集合。

```mysql
>db.tutorialspoint.insert({"name" : "tutorialspoint"})
>show collections
mycol
mycollection
system.indexes
tutorialspoint
>
```

### 删除集合

## `drop()` 方法

MongoDB 利用 `db.collection.drop()` 来删除数据库中的集合。

### 语法格式

`drop()` 命令的基本格式如下：

```mysql
db.COLLECTION_NAME.drop()
```

### 范例

首先检查在数据库 mydb 中已有集合：

```mysql
>use mydb
switched to db mydb
>show collections
mycol
mycollection
system.indexes
tutorialspoint
>
```

接着删除集合 mycollection。

```mysql
>db.mycollection.drop()
true
>
```

再次检查数据库中的现有集合：

```mysql
>show collections
mycol
system.indexes
tutorialspoint
>
```

如果成功删除选定集合，则 `drop()` 方法返回 true，否则返回 false。

### 数据类型

MongoDB 支持如下数据类型：

- **String**：字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。
- **Integer**：整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。
- **Boolean**：布尔值。用于存储布尔值（真/假）。
- **Double**：双精度浮点值。用于存储浮点值。
- **Min/Max keys**：将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。
- **Arrays**：用于将数组或列表或多个值存储为一个键。
- **Timestamp**：时间戳。记录文档修改或添加的具体时间。
- **Object**：用于内嵌文档。
- **Null**：用于创建空值。
- **Symbol**：符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。
- **Date**：日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。
- **Object ID**：对象 ID。用于创建文档的 ID。
- **Binary Data**：二进制数据。用于存储二进制数据。
- **Code**：代码类型。用于在文档中存储 JavaScript 代码。
- **Regular expression**：正则表达式类型。用于存储正则表达式。

## 插入文档

### insert() 方法

要想将数据插入 MongoDB 集合中，需要使用 `insert()` 或 `save()` 方法。

### 语法格式

`insert()` 方法的基本格式为：

```mysql
>db.COLLECTION_NAME.insert(document)
```

### 范例 1

```mysql
>db.mycol.insert({
   _id: ObjectId(7df78ad8902c),
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by: 'tutorials point',
   url: 'http://www.tutorialspoint.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
})
```

mycol 是上一节所创建的集合的名称。如果数据库中不存在该集合，那么 MongoDB 会创建该集合，并向其中插入文档。

在插入的文档中，如果我们没有指定 `_id` 参数，那么 MongoDB 会自动为文档指定一个唯一的 ID。

`_id` 是一个 12 字节长的 16 进制数，这 12 个字节的分配如下：

```json
_id: ObjectId(4 bytes timestamp, 3 bytes machine id, 2 bytes process id, 3 bytes incrementer)
```

为了，你可以将用 `insert()` 方法传入一个文档数组，范例如下：

### 范例 2

```mysql
>db.post.insert([
{
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by: 'tutorials point',
   url: 'http://www.tutorialspoint.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
},
{
   title: 'NoSQL Database', 
   description: "NoSQL database doesn't have tables",
   by: 'tutorials point',
   url: 'http://www.tutorialspoint.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 20, 
   comments: [  
      {
         user:'user1',
         message: 'My first comment',
         dateCreated: new Date(2013,11,10,2,35),
         like: 0 
      }
   ]
}
])
```

也可以用 `db.post.save(document)` 插入文档。如果没有指定文档的 `_id`，那么 `save()` 就和 `insert()` 完全一样了。如果指定了文档的 `_id`，那么它会覆盖掉含有 `save()` 方法中指定的 `_id`的文档的全部数据。

## 查询文档

### find() 方法

要想查询 MongoDB 集合中的数据，使用 `find()` 方法。

### 语法格式

`find()` 方法的基本格式为：

```mysql
>db.COLLECTION_NAME.find()
```

`find()` 方法会以非结构化的方式来显示所有文档。

## pretty() 方法

用格式化方式显示结果，使用的是 `pretty()` 方法。

### 语法格式

```mysql
>db.mycol.find().pretty()
```

### 范例

```mysql
>db.mycol.find().pretty()
{
   "_id": ObjectId(7df78ad8902c),
   "title": "MongoDB Overview", 
   "description": "MongoDB is no sql database",
   "by": "tutorials point",
   "url": "http://www.tutorialspoint.com",
   "tags": ["mongodb", "database", "NoSQL"],
   "likes": "100"
}
>
```

除了 `find()` 方法之外，还有一个 `findOne()` 方法，它只返回一个文档。

## MongoDB 中类似于 WHERE 子句的语句

如果想要基于一些条件来查询文档，可以使用下列操作。

| 操作       | 格式                     | 范例                                               | RDBMS中的类似语句              |
| :--------- | :----------------------- | :------------------------------------------------- | :----------------------------- |
| 等于       | `{<key>:<value>`}        | `db.mycol.find({"by":"tutorials point"}).pretty()` | `where by = 'tutorials point'` |
| 小于       | `{<key>:{$lt:<value>}}`  | `db.mycol.find({"likes":{$lt:50}}).pretty()`       | `where likes < 50`             |
| 小于或等于 | `{<key>:{$lte:<value>}}` | `db.mycol.find({"likes":{$lte:50}}).pretty()`      | `where likes <= 50`            |
| 大于       | `{<key>:{$gt:<value>}}`  | `db.mycol.find({"likes":{$gt:50}}).pretty()`       | `where likes > 50`             |
| 大于或等于 | `{<key>:{$gte:<value>}}` | `db.mycol.find({"likes":{$gte:50}}).pretty()`      | `where likes >= 50`            |
| 不等于     | `{<key>:{$ne:<value>}}`  | `db.mycol.find({"likes":{$ne:50}}).pretty()`       | `where likes != 50`            |

## MongoDB 中的 And 条件

### 语法格式

在 `find()` 方法中，如果传入多个键，并用逗号(`,`)分隔它们，那么 MongoDB 会把它看成是 **AND** 条件。AND 条件的基本语法格式为：

```mysql
>db.mycol.find({key1:value1, key2:value2}).pretty()
```

### 范例

下例将展示所有由 “tutorials point” 发表的标题为 “MongoDB Overview” 的教程。

```mysql
>db.mycol.find({"by":"tutorials point","title": "MongoDB Overview"}).pretty()
{
   "_id": ObjectId(7df78ad8902c),
   "title": "MongoDB Overview", 
   "description": "MongoDB is no sql database",
   "by": "tutorials point",
   "url": "http://www.tutorialspoint.com",
   "tags": ["mongodb", "database", "NoSQL"],
   "likes": "100"
}
>
```

对于上例这种情况，RDBMS 采用的 WHERE 子句将会是：**where by='tutorials point' AND title='MongoDB Overview'**。你可以在 find 子句中传入任意的键值对。

## MongoDB 中的 OR 条件

### 语法格式

若基于 **OR** 条件来查询文档，可以使用关键字 **$or**。 OR 条件的基本语法格式为：

```mysql
>db.mycol.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty()
```

### 范例

下例将展示所有由 “tutorials point” 发表的标题为 “MongoDB Overview” 的教程。

```mysql
>db.mycol.find({$or:[{"by":"tutorials point"},{"title": "MongoDB Overview"}]}).pretty()
{
   "_id": ObjectId(7df78ad8902c),
   "title": "MongoDB Overview", 
   "description": "MongoDB is no sql database",
   "by": "tutorials point",
   "url": "http://www.tutorialspoint.com",
   "tags": ["mongodb", "database", "NoSQL"],
   "likes": "100"
}
>
```

## 结合使用 AND 与 OR 条件

### 范例

下例所展示文档的条件为：喜欢数大于 100，标题是 “MongoDB Overview”，或者是由 “tutorials point” 所发表的。响应的 SQL WHERE 子句为：**where likes>10 AND (by = 'tutorials point' OR title = 'MongoDB Overview')**

```mysql
>db.mycol.find({"likes": {$gt:10}, $or: [{"by": "tutorials point"},{"title": "MongoDB Overview"}]}).pretty()
{
   "_id": ObjectId(7df78ad8902c),
   "title": "MongoDB Overview", 
   "description": "MongoDB is no sql database",
   "by": "tutorials point",
   "url": "http://www.tutorialspoint.com",
   "tags": ["mongodb", "database", "NoSQL"],
   "likes": "100"
}
>
```

# 更新文档

MongoDB 中的 `update()` 与 `save()` 方法都能用于更新集合中的文档。`update()` 方法更新已有文档中的值，而 `save()` 方法则是用传入该方法的文档来替换已有文档。

## update() 方法

`update()` 方法更新已有文档中的值。

### 语法格式

`update()` 方法基本格式如下：

```mysql
>db.COLLECTION_NAME.update(SELECTIOIN_CRITERIA, UPDATED_DATA)
```

### 范例

假如 mycol 集合中有下列数据：

```mysql
{ "_id" : ObjectId(5983548781331adf45ec5), "title":"MongoDB Overview"}
{ "_id" : ObjectId(5983548781331adf45ec6), "title":"NoSQL Overview"}
{ "_id" : ObjectId(5983548781331adf45ec7), "title":"Tutorials Point Overview"}
```

下面的例子将把文档原标题 'MongoDB Overview' 替换为新的标题 'New MongoDB Tutorial'。

```mysql
>db.mycol.update({'title':'MongoDB Overview'},{$set:{'title':'New MongoDB Tutorial'}})
>db.mycol.find()
{ "_id" : ObjectId(5983548781331adf45ec5), "title":"New MongoDB Tutorial"}
{ "_id" : ObjectId(5983548781331adf45ec6), "title":"NoSQL Overview"}
{ "_id" : ObjectId(5983548781331adf45ec7), "title":"Tutorials Point Overview"}
>
```

MongoDB 默认只更新单个文档，要想更新多个文档，需要把参数 `multi` 设为 `true`。

```mysql
>db.mycol.update({'title':'MongoDB Overview'},{$set:{'title':'New MongoDB Tutorial'}},{multi:true})
```

## save() 方法

`save()` 方法利用传入该方法的文档来替换已有文档。

### 语法格式

`save()` 方法基本语法格式如下：

```mysql
>db.COLLECTION_NAME.save({_id:ObjectId(),NEW_DATA})
```

### 范例

下例用 `_id` 为 '5983548781331adf45ec7' 的文档代替原有文档。

```mysql
>db.mycol.save(
   {
      "_id" : ObjectId(5983548781331adf45ec7), "title":"Tutorials Point New Topic", "by":"Tutorials Point"
   }
)
>db.mycol.find()
{ "_id" : ObjectId(5983548781331adf45ec5), "title":"Tutorials Point New Topic", "by":"Tutorials Point"}
{ "_id" : ObjectId(5983548781331adf45ec6), "title":"NoSQL Overview"}
{ "_id" : ObjectId(5983548781331adf45ec7), "title":"Tutorials Point Overview"}
>
```

# 删除文档

## remove() 方法

MongoDB 利用 `remove()` 方法 清除集合中的文档。它有 2 个可选参数：

- deletion criteria：（可选）删除文档的标准。
- justOne：（可选）如果设为 true 或 1，则只删除一个文档。

### 语法格式

`remove()` 方法的基本语法格式如下所示：

```mysql
>db.COLLECTION_NAME.remove(DELLETION_CRITTERIA)
```

### 范例

假如 mycol 集合中包含下列数据：

```
{ "_id" : ObjectId(5983548781331adf45ec5), "title":"MongoDB Overview"}
{ "_id" : ObjectId(5983548781331adf45ec6), "title":"NoSQL Overview"}
{ "_id" : ObjectId(5983548781331adf45ec7), "title":"Tutorials Point Overview"}
```

下面我们将删除其中所有标题为 'MongoDB Overview' 的文档。

```
>db.mycol.remove({'title':'MongoDB Overview'})
>db.mycol.find()
{ "_id" : ObjectId(5983548781331adf45ec6), "title":"NoSQL Overview"}
{ "_id" : ObjectId(5983548781331adf45ec7), "title":"Tutorials Point Overview"}
>
```

## 只删除一个文档

如果有多个记录，而你只想删除第一条记录，那么就设置 `remove()` 方法中的 `justOne` 参数：

```
>db.COLLECTION_NAME.remove(DELETION_CRITERIA,1)
```

## 删除所有文档

如果没有指定删除标准，则 MongoDB 会将集合中所有文档都予以删除。**这等同于 SQL 中的 truncate 命令**。

```mysql
>db.mycol.remove({})
>db.mycol.find()
>  
  
```

