[TOC]
# MySql



#### 数据库操作

- 查看当前数据库
  
  ```mysql
  SELECT DATABASE();
  ```
  
- 显示当前时间、用户名、数据库版本
  
```mysql SELECT now(), user(), version();


```

- 创建库
      
      ```mysql
      CREATE DATABASE[ IF NOT EXISTS] 数据库名 数据库选项
      ```
      
      ​    数据库选项：
      
      ```mysql
      CHARACTER SET charset_name
      COLLATE collation_name
      ```
  
- 查看已有库
      
      ```mysql
      SHOW DATABASES[ LIKE 'PATTERN']
      ```
  
- 查看当前库信息
      
          ```mysql 
      SHOW CREATE DATABASE 数据库名
          ```
      
      
      
- 修改库的选项信息
      
  ```mysql
  ALTER DATABASE 库名 选项信息
  ```
  
- 删除库
      
  ```mysql
  
   DROP DATABASE[ IF EXISTS] 数据库名
          同时删除该数据库相关的目录及其目录内容
  ```
  
#### 数据操作

```mysql
- 增    INSERT [INTO] 表名 [(字段列表)] VALUES (值列表)[, (值列表), ...]        
	-- 如果要插入的值列表包含所有字段并且顺序一致，则可以省略字段列表。        
	-- 可同时插入多条数据记录！        REPLACE 与 INSERT 完全一样，可互换。    
	INSERT 	[INTO] 表名 SET 字段名=值[, 字段名=值, ...]
	INSERT INTO tb_name(id,name,score)VALUES(NULL,'张三',140),(NULL,'张四',178),(NULL,'张五',134);
- 查    SELECT 字段列表 FROM 表名[ 其他子句]        
	-- 可来自多个表的多个字段        
	-- 其他子句可以不使用        
	-- 字段列表可以用*代替，表示所有字段
- 删    DELETE FROM 表名[ 删除条件子句]        没有条件子句，则会删除全部
		DELETE FROM tb_name WHERE id=3;
- 改    UPDATE 表名 SET 字段名=新值[, 字段名=新值] [更新条件]
UPDATE tb_name SET score=189 WHERE id=2;
```

#### 连接查询(join)

  将多个表的字段进行连接，可以指定连接条件。

- 内连接(inner join)
  
      - 默认就是内连接，可省略inner。
          - 只有数据存在时才能发送连接。即连接结果不能出现空行。
          on 表示连接条件。其条件表达式与where类似。也可以省略条件（表示条件永远为真）
          也可用where表示连接条件。
          还有 using, 但需字段名相同。 using(字段名)
          -- 交叉连接 cross join
          即，没有条件的内连接。
          select * from tb1 cross join tb2;
  
- 外连接(outer join)
          - 如果数据不存在，也会出现在连接结果中。
          -- 左外连接 left join
          如果数据不存在，左表记录会出现，而右表为null填充
          -- 右外连接 right join
          如果数据不存在，右表记录会出现，而左表为null填充
      
- 自然连接(natural join)
          自动判断连接条件完成连接。
          相当于省略了using，会自动查找相同字段名。
          natural join
          natural left join
          natural right join
  
  select info.id, info.name, info.stu_num, extra_info.hobby, extra_info.sex from info, extra_info where info.stu_num = extra_info.stu_id;



#### 超键、候选键、主键、外键

在关系数据库中，一张表中的每一行数据被称为一条记录。一条记录就是由多个字段组成的。

于关系表，有个很重要的约束，就是任意两条记录不能重复。不能重复不是指两条记录不完全相同，而是指能够通过某个字段唯一区分出不同的记录，这个字段被称为*主键*。

主键是关系表中记录的唯一标识。主键的选取非常重要：主键不要带有业务含义，而应该使用BIGINT自增或者GUID类型。主键也不应该允许`NULL`。

可以使用多个列作为联合主键，但联合主键并不常用。



**超键：**在关系中能唯一标识元组的属性集称为关系模式的超键。一个属性可以为作为一个超键，多个属性组合在一起也可以作为一个超键。超键包含候选键和主键。



**候选键：**是最小超键，即没有冗余元素的超键。



**主键：**数据库表中对储存数据对象予以唯一和完整标识的数据列或属性的组合。一个数据列只能有一个主键，且主键的取值不能缺失，即不能为空值（Null）。



**外键：**在一个表中存在的另一个表的主键称此表的外键。

#### 一对多

当我们用主键唯一标识记录时，Student表确定任意一个学生的记录：

| id   | name | other columns... |
| :--- | :--- | :--------------: |
| 1    | 小明 |       ...        |
| 2    | 小红 |       ...        |





我们还可以在`classes`表中确定任意一个班级记录

| id   | name | other columns... |
| :--- | :--- | :--------------- |
| 1    | 一班 | ...              |
| 2    | 二班 | ...              |

这两个表的关系可以称为“一对多”，即一个`classes`的记录可以对应多个`students`表的记录

为了表达这种一对多的关系，我们需要在`students`表中加入一列`class_id`，让它的值与`classes`表的某条记录相对应

| id   | class_id | name | other columns... |
| :--- | :------- | :--- | :--------------- |
| 1    | 1        | 小明 | ...              |
| 2    | 1        | 小红 | ...              |
| 5    | 2        | 小白 | ...              |

外键并不是通过列名实现的，而是通过定义外键约束实现的：

```mysql
ALTER TABLE students
ADD CONSTRAINT fk_class_id
FOREIGN KEY (class_id)
REFERENCES classes (id);
```

其中，外键约束的名称`fk_class_id`可以任意，`FOREIGN KEY (class_id)`指定了`class_id`作为外键，`REFERENCES classes (id)`指定了这个外键将关联到`classes`表的`id`列（即`classes`表的主键）。



**由于外键约束会降低数据库的性能**，**大部分互联网应用程序为了追求速度，并不设置外键约束**，而是仅靠应用程序自身来保证逻辑的正确性。这种情况下，`class_id`仅仅是一个普通的列，只是它起到了外键的作用而已。

要删除一个外键约束，也是通过`ALTER TABLE`实现的：

```mysql
ALTER TABLE students
DROP FOREIGN KEY fk_class_id;
```

注意：删除外键约束并没有删除外键这一列。删除列是通过`DROP COLUMN ...`实现的

#### 多对多

需要用到中间表teacher_class（角色权限设计中也是多对多）

`teachers`表：

| id   | name   |
| :--- | :----- |
| 1    | 张老师 |
| 2    | 王老师 |
| 3    | 李老师 |
| 4    | 赵老师 |



`classes`表：

| id   | name |
| :--- | :--- |
| 1    | 一班 |
| 2    | 二班 |

中间表`teacher_class`关联两个一对多关系：

| id   | teacher_id | class_id |
| :--- | :--------- | :------- |
| 1    | 1          | 1        |
| 2    | 1          | 2        |
| 3    | 2          | 1        |
| 4    | 2          | 2        |
| 5    | 3          | 1        |
| 6    | 4          | 2        |

#### 一对一

一对一关系是指，一个表的记录对应到另一个表的唯一一个记录。

例如，`students`表的每个学生可以有自己的联系方式，如果把联系方式存入另一个表`contacts`，我们就可以得到一个“一对一”关系：

| id   | student_id | mobile      |
| :--- | :--------- | :---------- |
| 1    | 1          | 135xxxx6300 |
| 2    | 2          | 138xxxx2209 |
| 3    | 5          | 139xxxx8086 |

有细心的童鞋会问，既然是一对一关系，那为啥不给`students`表增加一个`mobile`列，这样就能合二为一了？

如果业务允许，完全可以把两个表合为一个表。但是，有些时候，如果某个学生没有手机号，那么，`contacts`表就不存在对应的记录。实际上，一对一关系准确地说，是`contacts`表一对一对应`students`表。

还有一些应用会把一个大表拆成两个一对一的表，目的是把经常读取和不经常读取的字段分开，以获得更高的性能。例如，把一个大的用户表分拆为用户基本信息表`user_info`和用户详细信息表`user_profiles`，大部分时候，只需要查询`user_info`表，并不需要查询`user_profiles`表，这样就提高了查询速度。

总结：**关系数据库通过外键可以实现一对多、多对多和一对一的关系。外键既可以通过数据库来约束，也可以不设置约束，仅依靠应用程序的逻辑来保证。**

#### 2、什么是事务？什么是锁？**



**事务：**就是被绑定在一起作为一个逻辑工作单元的 SQL 语句分组，如果任何一个语句操作失败那么整个操作就被失败，以后操作就会回滚到操作前状态，或者是上有个节点。为了确保要么执行，要么不执行，就可以使用事务。要将有组语句作为事务考虑，就需要通过 ACID 测试，即原子性，一致性，隔离性和持久性。



**锁：**在所以的 DBMS 中，锁是实现事务的关键，锁可以保证事务的完整性和并发性。与现实生活中锁一样，它可以使某些数据的拥有者，在某段时间内不能使用某些数据或数据结构。当然锁还分级别的。



**3、数据库事务的四个特性及含义**



**原子性：**整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。



**一致性：**在事务开始之前和事务结束以后，数据库的完整性约束没有被破坏。



**隔离性：**隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作。如果有两个事务，运行在相同的时间内，执行 相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统。这种属性有时称为串行化，为了防止事务操作间的混淆，必须串行化或序列化请 求，使得在同一时间仅有一个请求用于同一数据。



**持久性：**在事务完成以后，该事务所对数据库所作的更改便持久的保存在数据库之中，并不会被回滚。

####  索引

索引是对数据库表中一列或多列的值进行排序的一种结构。一个非常恰当的比喻就是书的目录页与书的正文内容之间的关系，为了方便查找书中的内容，通过对内容建立索引形成目录。索引是一个文件，它是要占据物理空间的。

- **主键索引:** 数据列不允许重复，不允许为NULL.一个表只能有一个主键。

- **唯一索引:** 数据列不允许重复，允许为NULL值，一个表允许多个列创建唯一索引。

  可以通过 `ALTER TABLE table_name ADD UNIQUE (column);` 创建唯一索引

  可以通过 `ALTER TABLE table_name ADD UNIQUE (column1,column2);` 创建唯一组合索引

- **普通索引:** 基本的索引类型，没有唯一性的限制，允许为NULL值。

  可以通过`ALTER TABLE table_name ADD INDEX index_name (column);`

  创建普通索引可以通过`ALTER TABLE table_name ADD INDEX index_name(column1, column2, column3);`创建组合索引

- **全文索引：** 是目前搜索引擎使用的一种关键技术。

  可以通过`ALTER TABLE table_name ADD FULLTEXT (column);`创建全文索引

  **最左前缀**

  - 顾名思义，就是最左优先，在创建多列索引时，要根据业务需求，where子句中使用最频繁的一列放在最左边。
  
  -  还有一个就是生效原则 比如
  
    ```mysql
    index(a,b,c)
    where a=3	只使用了a
    where a=3 and b=5	使用了a,b
    where a=3 and b=5 and c=4	使用了a,b,c
    where b=3 or where c=4	没有使用索引
    where a=3 and c=4	仅使用了a
    where a=3 and b>10 and c=7	使用了a,b
    where a=3 and b like 'xx%' and c=7	使用了a,b
    ```
  



索引是关系数据库中对某一列或多个列的值进行预排序的数据结构。通过使用索引，可以让数据库系统不必扫描整个表，而是直接定位到符合条件的记录，这样就大大加快了查询速度。

例如，对于`students`表：

| id   | class_id | name | gender | score |
| :--- | :------- | :--- | :----- | :---- |
| 1    | 1        | 小明 | M      | 90    |
| 2    | 1        | 小红 | F      | 95    |
| 3    | 1        | 小军 | M      | 88    |

如果要经常根据`score`列进行查询，就可以对`score`列创建索引：

```mysql
ALTER TABLE students
ADD INDEX idx_score (score);
```

使用`ADD INDEX idx_score (score)`就创建了一个名称为`idx_score`，使用列`score`的索引。索引名称是任意的，索引如果有多列，可以在括号里依次写上，例如：

```mysql
ALTER TABLE students
ADD INDEX idx_name_score (name, score);
```

注意事项：

- **索引的效率取决于索引列的值是否散列，即该列的值如果越互不相同，那么索引效率越高****。反过来，如果记录的列存在大量相同的值，例如`gender`列，大约一半的记录值是`M`，另一半是`F`，因此，对该列创建索引就没有意
- **可以对一张表创建多个索引。索引的优点是提高了查询效率，缺点是在插入、更新和删除记录时，需要同时修改索引，因此，索引越多，插入、更新和删除记录的速度就越慢**
- **对于主键，关系数据库会自动对其创建主键索引。使用主键索引的效率是最高的，因为主键会保证绝对唯一**。

##### 唯一索引

在设计关系数据表的时候，看上去唯一的列，例如身份证号、邮箱地址等，因为他们具有业务含义，因此不宜作为主键。

但是，这些列根据业务要求，又具有唯一性约束：即不能出现两条记录存储了同一个身份证号。这个时候，就可以给该列添加一个唯一索引。例如，我们假设`students`表的`name`不能重复：

```mysql
ALTER TABLE students
ADD UNIQUE INDEX uni_name (name);
```

通过`UNIQUE`关键字我们就添加了一个唯一索引。

也可以只对某一列添加一个唯一约束而不创建唯一索引：

```mysql
ALTER TABLE students
ADD CONSTRAINT uni_name UNIQUE (name);
```

这种情况下，`name`列没有索引，但仍然具有唯一性保证。

无论是否创建索引，对于用户和应用程序来说，使用关系数据库不会有任何区别。这里的意思是说，当我们在数据库中查询时，如果有相应的索引可用，数据库系统就会自动使用索引来提高查询效率，如果没有索引，查询也能正常执行，只是速度会变慢。因此，索引可以在使用数据库的过程中逐步优化。

小结：通过对数据库表创建索引，可以提高查询速度。通过创建唯一索引，可以保证某一列的值具有唯一性。数据库索引对于用户和应用程序来说都是透明的。

#### 索引设计的原则？

1，适合索引的列是出现在where子句中的列，或者连接子句中指定的列

2，基数较小的类，索引效果较差，没有必要在此列建立索引

3，使用短索引，如果对长字符串列进行索引，应该指定一个前缀长度，这样能够节省大量索引空间

4，不要过度索引。索引需要额外的磁盘空间，并降低写操作的性能。在修改表内容的时候，索引会进行更新甚至重构，索引列越多，这个时间就会越长。所以只保持需要的索引有利于查询即可。

#### **Mysql 中 MyISAM 和 InnoDB 的区别有哪些？**

**区别：**

1、InnoDB支持事务，MyISAM不支持，对于InnoDB每一条SQL语言都默认封装成事务，自动提交，这样会影响速度，所以最好把多条SQL语言放在begin和commit之间，组成一个事务；

2、InnoDB支持外键，而MyISAM不支持。对一个包含外键的InnoDB表转为MYISAM会失败；

3、InnoDB是聚集索引，数据文件是和索引绑在一起的，必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大。而MyISAM是非聚集索引，数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的。

4、InnoDB不保存表的具体行数，执行select count(*) from table时需要全表扫描。而MyISAM用一个变量保存了整个表的行数，执行上述语句时只需要读出该变量即可，速度很快；

5、Innodb不支持全文索引，而MyISAM支持全文索引，查询效率上MyISAM要高；

**如何选择：**

1、是否要支持事务，如果要请选择innodb，如果不需要可以考虑MyISAM；

2、如果表中绝大多数都只是读查询，可以考虑MyISAM，如果既有读写也挺频繁，请使用InnoDB。

3、系统奔溃后，MyISAM恢复起来更困难，能否接受；

4、MySQL5.5版本开始Innodb已经成为Mysql的默认引擎(之前是MyISAM)，说明其优势是有目共睹的，如果你不知道用什么，那就用InnoDB，至少不会差。

#### 基础语法

##### 条件查询

要查询数据库表的数据，我们使用如下的SQL语句：

```mysql
SELECT * FROM <表名>
```

假设表名是`students`，要查询`students`表的所有行，我们用如下SQL语句：

```mysql
SELECT * FROM students;
```

**条件查询**的语法就是：

```mysql
SELECT * FROM <表名> WHERE <条件表达式>
```

条件表达式可以用`<条件1> AND <条件2>`表达满足条件1并且满足条件2。例如，符合条件“分数在80分或以上”，并且还符合条件“男生”，把这两个条件写出来：

- 条件1：根据score列的数据判断：`score >= 80`；
- 条件2：根据gender列的数据判断：`gender = 'M'`，注意`gender`列存储的是字符串，需要用单引号括起来。

```mysql
SELECT * FROM students WHERE score >= 80 AND gender = 'M';
```

第二种条件是`<条件1> OR <条件2>`，表示满足条件1或者满足条件2。例如，把上述`AND`查询的两个条件改为`OR`，查询结果就是“分数在80分或以上”或者“男生”，满足任意之一的条件即选出该记录：

```mysql
SELECT * FROM students WHERE score >= 80 OR gender = 'M';
```

第三种条件是`NOT <条件>`，表示“不符合该条件”的记录。例如，写一个“不是2班的学生”这个条件，可以先写出“是2班的学生”：`class_id = 2`，再加上`NOT`：`NOT class_id = 2`：

```mysql
SELECT * FROM students WHERE NOT class_id = 2;
```

**上述`NOT`条件`NOT class_id = 2`其实等价于`class_id <> 2`，因此，`NOT`查询不是很常用。**

要组合三个或者更多的条件，就需要用小括号`()`表示如何进行条件运算。例如，编写一个复杂的条件：分数在80以下或者90以上，并且是男生：

```mysql
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M';
```

**如果不加括号，条件运算按照`NOT`、`AND`、`OR`的优先级进行，即`NOT`优先级最高，其次是`AND`，最后是`OR`。加上括号可以改变优先级。**

### 常用的条件表达式

| 条件                 | 表达式举例1     | 表达式举例2      | 说明                                              |
| :------------------- | :-------------- | :--------------- | :------------------------------------------------ |
| 使用=判断相等        | score = 80      | name = 'abc'     | 字符串需要用单引号括起来                          |
| 使用>判断大于        | score > 80      | name > 'abc'     | 字符串比较根据ASCII码，中文字符比较根据数据库设置 |
| 使用>=判断大于或相等 | score >= 80     | name >= 'abc'    |                                                   |
| 使用<判断小于        | score < 80      | name <= 'abc'    |                                                   |
| 使用<=判断小于或相等 | score <= 80     | name <= 'abc'    |                                                   |
| 使用<>判断不相等     | score <> 80     | name <> 'abc'    |                                                   |
| 使用LIKE判断相似     | name LIKE 'ab%' | name LIKE '%bc%' | %表示任意字符，例如'ab%'将匹配'ab'，'abc'，'abcd' |

##### 投影查询

用`SELECT * FROM <表名> WHERE <条件>`可以选出表中的若干条记录。我们注意到返回的二维表结构和原表是相同的，即结果集的所有列与原表的所有列都一一对应。

如果我们只希望返回某些列的数据，而不是所有列的数据，我们可以用`SELECT 列1, 列2, 列3 FROM ...`，让结果集仅包含指定列。这种操作称为投影查询。

```mysql
SELECT id, score, name FROM students;
```

这样返回的结果集就只包含了我们指定的列，并且，结果集的列的顺序和原表可以不一样。

使用`SELECT 列1, 列2, 列3 FROM ...`时，还可以给每一列起个别名，这样，结果集的列名就可以与原表的列名不同。它的语法是`SELECT 列1 别名1, 列2 别名2, 列3 别名3 FROM ...`。

例如，以下`SELECT`语句将列名`score`重命名为`points`，而`id`和`name`列名保持不变：

```mysql
SELECT id, score points, name FROM students;
```

##### 排序

我们使用SELECT查询时，细心的读者可能注意到，查询结果集通常是按照`id`排序的，也就是根据主键排序。这也是大部分数据库的做法。如果我们要根据其他条件排序怎么办？可以加上`ORDER BY`子句。例如按照成绩从低到高进行排序：

**默认是升序**

```mysql
SELECT id, name, gender, score FROM students ORDER BY score;
```

如果要反过来，按照成绩从高到底排序，我们可以加上`DESC`表示“倒序”：

```mysql
SELECT id, name ,gender, score FROM student ORDER BY score DESC
```

如果`score`列有相同的数据，要进一步排序，可以继续添加列名。例如，使用`ORDER BY score DESC, gender`表示先按`score`列倒序，如果有相同分数的，再按`gender`列排序：

```mysql
SELECT id, name, gender, score FROM students ORDER BY score DESC, gender;
```

默认的排序规则是`ASC`：“升序”，即从小到大。`ASC`可以省略，即`ORDER BY score ASC`和`ORDER BY score`效果一样。

如果有`WHERE`子句，那么`ORDER BY`子句要放到`WHERE`子句后面。例如，查询一班的学生成绩，并按照倒序排序：

```mysql
SELECT id, name, gender, score
FROM students
WHERE class_id = 1
ORDER BY score DESC;
```

**小结**

使用`ORDER BY`可以对结果集进行排序；

可以对多列进行升序、倒序排序。

##### 分页查询

使用SELECT查询时，如果结果集数据量很大，比如几万行数据，放在一个页面显示的话数据量太大，不如分页显示，每次显示100条。

要实现分页功能，实际上就是从结果集中显示第1~100条记录作为第1页，显示第101~200条记录作为第2页，以此类推。

因此，分页实际上就是从结果集中“截取”出第M~N条记录。这个查询可以通过`LIMIT <M> OFFSET <N>`子句实现。

现在，我们把结果集分页，每页3条记录。要获取第1页的记录，可以使用`LIMIT 3 OFFSET 0`：

```mysql
SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 0;
```

上述查询`LIMIT 3 OFFSET 0`表示，对结果集从0号记录开始，最多取3条。注意SQL记录集的索引从0开始。

如果要查询第2页，那么我们只需要“跳过”头3条记录，也就是对结果集从3号记录开始查询，把`OFFSET`设定为3：

```mysql
SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 3;
```

类似的，查询第3页的时候，`OFFSET`应该设定为6:

```mysql
SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 6;
```

查询第4页的时候，`OFFSET`应该设定为9:

```mysql
SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 9;
```

由于第4页只有1条记录，因此最终结果集按实际数量1显示。`LIMIT 3`表示的意思是“最多3条记录”。

可见，分页查询的关键在于，首先要确定每页需要显示的结果数量`pageSize`（这里是3），然后根据当前页的索引`pageIndex`（从1开始），确定`LIMIT`和`OFFSET`应该设定的值：

- `LIMIT`总是设定为`pageSize`；
- `OFFSET`计算公式为`pageSize * (pageIndex - 1)`。

这样就能正确查询出第N页的记录集。

如果原本记录集一共就10条记录，但我们把`OFFSET`设置为20，会得到什么结果呢？

```mysql
Empty result set
```

`OFFSET`超过了查询的最大数量并不会报错，而是得到一个空的结果集。

##### 重点注意

`OFFSET`是可选的，如果只写`LIMIT 15`，那么相当于`LIMIT 15 OFFSET 0`。

在MySQL中，`LIMIT 15 OFFSET 30`还可以简写成`LIMIT 30, 15`。

使用`LIMIT <M> OFFSET <N>`分页时，随着`N`越来越大，查询效率也会越来越低。

**小结**

使用`LIMIT <M> OFFSET <N>`可以对结果集进行分页，每次查询返回结果集的一部分；

分页查询需要先确定每页的数量和当前页数，然后确定`LIMIT`和`OFFSET`的值。

##### 聚合查询

如果我们要统计一张表的数据量，例如，想查询`students`表一共有多少条记录，难道必须用`SELECT * FROM students`查出来然后再数一数有多少行吗？

这个方法当然可以，但是比较弱智。对于统计总数、平均数这类计算，SQL提供了专门的聚合函数，使用聚合函数进行查询，就是聚合查询，它可以快速获得结果。

仍然以查询`students`表一共有多少条记录为例，我们可以使用SQL内置的`COUNT()`函数查询：

```mysql
SELECT COUNT(*) FROM students;
```

`COUNT(*)`表示查询所有列的行数，要注意聚合的计算结果虽然是一个数字，但查询的结果仍然是一个二维表，只是这个二维表只有一行一列，并且列名是`COUNT(*)`。

通常，使用聚合查询时，我们应该给列名设置一个别名，便于处理结果：

```mysql
SELECT COUNT(*) num FROM students;
```

`COUNT(*)`和`COUNT(id)`实际上是一样的效果。

另外注意，聚合查询同样可以使用`WHERE`条件，因此我们可以方便地统计出有多少男生、多少女生、多少80分以上的学生等：

```mysql
SELECT COUNT(*) boys FROM students WHERE gender = 'M';
```

除了`COUNT()`函数外，SQL还提供了如下聚合函数：

| 函数 | 说明                                   |
| :--- | :------------------------------------- |
| SUM  | 计算某一列的合计值，该列必须为数值类型 |
| AVG  | 计算某一列的平均值，该列必须为数值类型 |
| MAX  | 计算某一列的最大值                     |
| MIN  | 计算某一列的最小值                     |

注意，`MAX()`和`MIN()`函数并不限于数值类型。如果是字符类型，`MAX()`和`MIN()`会返回排序最后和排序最前的字符。

要统计男生的平均成绩，我们用下面的聚合查询：

```mysql
SELECT AVG(score) average FROM students WHERE gender = 'M';
```

要特别注意：如果聚合查询的`WHERE`条件没有匹配到任何行，`COUNT()`会返回0，而`SUM()`、`AVG()`、`MAX()`和`MIN()`会返回`NULL`：

```
-- WHERE条件gender = 'X'匹配不到任何行:
```

```mysql
SELECT AVG(score) average FROM students WHERE gender = 'X';
```

### 分组

如果我们要统计一班的学生数量，我们知道，可以用`SELECT COUNT(*) num FROM students WHERE class_id = 1;`。如果要继续统计二班、三班的学生数量，难道必须不断修改`WHERE`条件来执行`SELECT`语句吗？

对于聚合查询，SQL还提供了“分组聚合”的功能。我们观察下面的聚合查询：

```
-- 按class_id分组: 
```

```mysql
SELECT COUNT(*) num FROM students GROUP BY class_id;
```

执行这个查询，`COUNT()`的结果不再是一个，而是3个，这是因为，`GROUP BY`子句指定了按`class_id`分组，因此，执行该`SELECT`语句时，会把`class_id`相同的列先分组，再分别计算，因此，得到了3行结果。

但是这3行结果分别是哪三个班级的，不好看出来，所以我们可以把`class_id`列也放入结果集中：

```
-- 按class_id分组: 
```

```mysql
SELECT class_id, COUNT(*) num FROM students GROUP BY class_id;
```

这下结果集就可以一目了然地看出各个班级的学生人数。我们再试试把`name`放入结果集：

```
-- 按class_id分组:
```

```mysql
SELECT name, class_id, COUNT(*) num FROM students GROUP BY class_id;
```

不出意外，执行这条查询我们会得到一个语法错误，因为在任意一个分组中，只有`class_id`都相同，`name`是不同的，SQL引擎不能把多个`name`的值放入一行记录中。因此，聚合查询的列中，只能放入分组的列。

也可以使用多个列进行分组。例如，我们想统计各班的男生和女生人数

```mysql
SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;
```

| class_id | gender | num  |
| :------- | :----- | :--- |
| 1        | M      | 2    |
| 1        | F      | 2    |
| 2        | F      | 1    |
| 2        | M      | 2    |
| 3        | F      | 2    |
| 3        | M      | 1    |

上述查询结果集一共有6条记录，分别对应各班级的男生和女生人数。