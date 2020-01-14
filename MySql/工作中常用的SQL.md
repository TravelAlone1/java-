[TOC]

# 工作中常用的SQL

## 常用函数

### group  by 解析

**group by 必须出现在where之后，order by 之前**

group by语法可以根据给定**数据列的每个成员对查询结果进行分组统计**，最终得到一个分组汇总表。

SELECT子句中的列名必须为分组列或列函数。列函数对于GROUP BY子句定义的每个组各返回一个结果。

例如

某个员工信息表结构和数据如下：
id name dept salary edlevel hiredate
1 张三 开发部 2000 3 2009-10-11
2 李四 开发部 2500 3 2009-10-01
3 王五 设计部 2600 5 2010-10-02
4 王六 设计部 2300 4 2010-10-03
5 马七 设计部 2100 4 2010-10-06
6 赵八 销售部 3000 5 2010-10-05
7 钱九 销售部 3100 7 2010-10-07
8 孙十 销售部 3500 7 2010-10-06
例如，我想列出每个部门最高薪水的结果，sql语句如下：

```mysql
SELECT DEPT, MAX(SALARY) AS MAXIMUM
FROM STAFF
GROUP BY DEPT
```

查询结果如下：

```mysql
  DEPT  MAXIMUM 
    开发部 2500
    设计部 2600
    销售部 3
```

#### 注意：在SELECT语句中指定的每个列名也在GROUP BY子句中提到。未在这两个地方提到的列名将产生错误。

GROUP BY子句对DEPT和EDLEVEL的每个唯一组合各返回一行。
在GROUP BY子句之后使用HAVING子句
可应用限定条件进行分组，以便系统仅对满足条件的组返回结果。为此，在GROUP BY子句后面包含一个HAVING子句。HAVING子句可包含一个或多个用AND和OR连接的谓词。每个谓词将组特性（如AVG(SALARY)）与下列之一进行比较：

### join查询

作用：**我想要的查询结果，一张表搞不掂，那我就join另一张表**

注意事项：

- 在join的时候，会产生笛卡尔积（至于什么是笛卡尔积我这里就不说了，反正我们要记住的是**join表时一定要写关联条件去除笛卡尔积**）



#### select子句顺序

| 子句     | 说明               | 是否必须使用           |
| -------- | ------------------ | ---------------------- |
| select   | 要返回的列或表示式 | 是                     |
| form     | 从中检索数据的表   | 仅在从表选择数据时使用 |
| where    | 行级过滤           | 否                     |
| group by | 分组说明           | 仅在按组计算聚集时使用 |
| having   | 组级过滤           | 否                     |
| order by | 输出排序顺序       | 否                     |
| limit    | 要检索的行数       | 否                     |

### case when

作用：与if-else类似

用法：case when then else end

示例：

```mysql
CASE WHEN sex = '1' THEN '男'
         WHEN sex = '2' THEN '女'
ELSE '其他' END  
```

在when后面可以跟多个表达式，比如说：

```mysql
CASE WHEN sex = '1' and name ='Java3y' THEN '男'
         WHEN sex = '2' and name ='Java4y' THEN '女'
ELSE '其他' END 
```

如果要为`case when`表达式取别名，在`end` 关键字后边直接加就好了