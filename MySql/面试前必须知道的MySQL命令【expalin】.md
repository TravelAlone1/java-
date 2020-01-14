# 一、explain命令

## 1.1体验explain命令

首先我们来**体验**一下`explain`命令是怎么使用的，以及输出的结果是什么：

```mysql
explain select * from table_user ;
```

## 1.2为什么需要explain命令

我们很多时候编写完一条SQL语句，往往想知道这条SQL语句执行是否高效。或者说，我们建立好的索引在这条SQL语句中是否使用到了，就可以使用`explain`命令来分析一下！

- 简单来说：通过`explain`命令我们可以学习到该条SQL是如何执行的，随后解析explain的结果可以帮助我们使用更好的索引，最终来优化它！

通过`explain`命令我们可以知道以下信息：表的读取顺序，数据读取操作的类型，哪些索引可以使用，哪些索引实际使用了，表之间的引用，每张表有多少行被优化器查询等信息。

```
// 好了，我们下面看一下explain出来的结果是怎么看的。
```

## 1.3读懂explain命令结果

explain命令输出的结果有10列：`id、select_type、table、type、possible_keys、key、key_len、ref、rows、Extra`

