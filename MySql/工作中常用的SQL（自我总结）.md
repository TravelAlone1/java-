# 工作中常用的SQL

## 一、回顾join查询

很久不用，忘得差不多了，

简单理解：**一张表搞不定就用两张表**

 值得注意的是：在join的时候，会产生笛卡尔积（至于什么是笛卡尔积我这里就不说了，反正我们要记住的是**join表时一定要写关联条件去除笛卡尔积**） 

```mysql
--学生表
CREATE TABLE `Student`(
  `s_id` VARCHAR(20),
  `s_name` VARCHAR(20) NOT NULL DEFAULT '',
  `s_birth` VARCHAR(20) NOT NULL DEFAULT '',
  `s_sex` VARCHAR(10) NOT NULL DEFAULT '',
  PRIMARY KEY(`s_id`)
);
--课程表
CREATE TABLE `Course`(
  `c_id`  VARCHAR(20),
  `c_name` VARCHAR(20) NOT NULL DEFAULT '',
  `t_id` VARCHAR(20) NOT NULL,
  PRIMARY KEY(`c_id`)
);
--成绩表
CREATE TABLE `Score`(
  `s_id` VARCHAR(20),
  `c_id`  VARCHAR(20),
  `s_score` INT(3),
  PRIMARY KEY(`s_id`,`c_id`)
);
```

比如学生表和成绩表，

不使用关联条件的情况下不同s_id的数据会生成

## HAVING

[HAVING](https://www.baidu.com/s?wd=HAVING&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)语句通常与GROUP [BY](https://www.baidu.com/s?wd=BY&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)语句联合使用，用来过滤由GROUP [BY](https://www.baidu.com/s?wd=BY&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)语句返回的记录集。

[HAVING](https://www.baidu.com/s?wd=HAVING&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)语句的存在弥补了[WHERE](https://www.baidu.com/s?wd=WHERE&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)关键字不能与聚合函数联合使用的不足。

SELECT column1, column2, ... column_n, aggregate_function (expression)FROM tables[WHERE](https://www.baidu.com/s?wd=WHERE&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao) predicatesGROUP [BY](https://www.baidu.com/s?wd=BY&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao) column1, column2, ... column_n[HAVING](https://www.baidu.com/s?wd=HAVING&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao) condition1 ... condition_n;

同样使用本文中的学生表格，如果想查询平均分高于80分的学生记录可以这样写：

SELECT id, COUNT(course) as numcourse, AVG(score) as avgscore

FROM student

GROUP BY id

HAVING AVG(score)>=80;

在这里，如果用[WHERE](https://www.baidu.com/s?wd=WHERE&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)代替HAVING就会出错。

**where在group by 之前执行，having在group by 之后执行**



## IN 操作符

IN 操作符允许我们在 WHERE 子句中规定多个值。

### SQL IN 语法

```mysql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1,value2,...)
```

