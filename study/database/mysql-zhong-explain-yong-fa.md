# MySQL中explain用法

##  MySQL中explain用法和结果分析（详解）

> [https://www.php.cn/mysql-tutorials-454417.html](https://www.php.cn/mysql-tutorials-454417.html)

### explain的作用

我们使用explain命令来查看mysql语句的执行计划（execution plan）,解释mysql是如何执行一条sql语句的；解释的内容主要包括表的连接方式和顺序，以及索引的使用情况。使用explain，可以分析出需要在哪里加上索引，以及调整表的连接，以达到优化查询的目的；explain命令之后不仅可以跟select语句，也可以跟delete,insert,update,replace语句。

\#\# 用法

只需要在sql语句前加上explain就可以了，比如：

```text
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows   | filtered | Extra |
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
|  1 | SIMPLE      | user  | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 100310 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
```

### 如何分析

我们看到explain命令的结果有很多列，通常情况下，我们最关心的指标是`select_type`和`type`。

#### 概念

| Column | JSON Name | Meaning |
| :--- | :--- | :--- |
| [`id`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_id) | `select_id` | The `SELECT` identifier（选择标识符） |
| [`select_type`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_select_type) | None | The `SELECT` type（查询类型） |
| [`table`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_table) | `table_name` | The table for the output row（结果集使用的表） |
| [`partitions`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_partitions) | `partitions` | The matching partitions（匹配的分区） |
| [`type`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_type) | `access_type` | The join type（连接类型） |
| [`possible_keys`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_possible_keys) | `possible_keys` | The possible indexes to choose（可能使用到的索引） |
| [`key`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_key) | `key` | The index actually chosen（实际使用的索引） |
| [`key_len`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_key_len) | `key_length` | The length of the chosen key （使用索引的长度） |
| [`ref`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_ref) | `ref` | The columns compared to the index（和索引比较的列） |
| [`rows`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_rows) | `rows` | Estimate of rows to be examined（扫描的行数） |
| [`filtered`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_filtered) | `filtered` | Percentage of rows filtered by table condition（表条件过滤行数的百分比） |
| [`Extra`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fexplain-output.html%23explain_extra) | None | Additional information（附加的信息） |

#### 进一步解释

**id**

id的值越大，执行的优先级越高，id相同，则从上往下顺序执行。所以以下例子：

| id | placeholder |
| :--- | :--- |
| 1 | A |
| 1 | B |
| 2 | C |

执行的顺序是：C，A，B。

**select\_type**

| `select_type` Value | JSON Name | Meaning（含义） |
| :--- | :--- | :--- |
| `SIMPLE` | None | Simple [`SELECT`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fselect.html) \(not using [`UNION`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Funion.html) or subqueries\)（查询查询，不使用union或子查询） |
| `PRIMARY` | None | Outermost [`SELECT`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fselect.html)（最外层的select查询） |
| [`UNION`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Funion.html) | None | Second or later [`SELECT`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fselect.html) statement in a [`UNION`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Funion.html) （在union中排在第二位甚至更靠后的select语句） |
| `DEPENDENT UNION` | `dependent` \(`true`\) | Second or later [`SELECT`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fselect.html) statement in a [`UNION`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Funion.html), dependent on outer query （在union中排在第二位甚至更靠后的select语句，取决于外面的查询） |
| `UNION RESULT` | `union_result` | Result of a [`UNION`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Funion.html).（union结果集） |
| [`SUBQUERY`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Foptimizer-hints.html%23optimizer-hints-subquery) | None | First [`SELECT`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fselect.html) in subquery（子查询中的第一个select） |
| `DEPENDENT SUBQUERY` | `dependent` \(`true`\) | First [`SELECT`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Fselect.html) in subquery, dependent on outer query（子查询中的第一个select，取决于外面的查询） |
| `DERIVED` | None | Derived table（派生表） |
| `DEPENDENT DERIVED` | `dependent` \(`true`\) | Derived table dependent on another table （派生表，依赖其他表） |
| `MATERIALIZED` | `materialized_from_subquery` | Materialized subquery （实现子查询） |
| `UNCACHEABLE SUBQUERY` | `cacheable` \(`false`\) | A subquery for which the result cannot be cached and must be re-evaluated for each row of the outer query（结果不能被缓存并且外部查询的每一行都必须被重新评估的子查询） |
| `UNCACHEABLE UNION` | `cacheable` \(`false`\) | The second or later select in a [`UNION`](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdoc%2Frefman%2F8.0%2Fen%2Funion.html) that belongs to an uncacheable subquery \(see `UNCACHEABLE SUBQUERY`\)（在union中排位第二甚至更靠后属于不能缓存的子查询） |

**table**

显示这行的数据是关于哪张表的，也可能是表的别名。

**type**

**可能的取值**

null &gt; system &gt; const &gt; eq\_ref &gt; ref &gt; range &gt; index &gt; all,从左到右，性能递减，null最好，all最差，一般的，最好能优化查询到const到range之间。

**含义**

ALL：Full Table Scan，遍历全表。

index: Full Index Scan，index与ALL区别为index类型只遍历索引树，不遍历数据行，所以比all的速度要快。

range: 只检索给定范围的行，这个范围必须应用在一个有索引的列上。

ref: 使用了非唯一索引作为where或join条件，是一个确定的值。

eq\_ref: 同ref，但索引是唯一索引。

const、system: 将确定的值应用在索引（unique and not unique）上，`type`将会是`const`,当结果只有一行时，`type`是`system`。

NULL: MySQL在优化过程中分解语句，执行时甚至不用访问表或索引，例如从一个索引列里选取最小值可以通过单独索引查找完成。

**例子**

每一个`type`都给出了一个例子:

```text
drop table t1, t2, t3;
​
create table t1 (
  id      int(11) auto_increment,
  name    varchar(50),
  address varchar(100),
  primary key (id),
  key index_name(name)
);
create table t2 (
  id      int(11) auto_increment,
  name    varchar(50),
  address varchar(100),
  primary key (id),
  key index_name(name)
);
create table t3 (
  id      int(11) auto_increment,
  name    varchar(50),
  address varchar(100),
  primary key (id),
  key index_name(name)
);
​
insert into t1 (name, address)
values ('tom', 'downtown'),
       ('jack', 'country yard');
insert into t2 (name, address)
values ('tom', 'downtown'),
       ('jack', 'country yard');
insert into t3 (name, address)
values ('tom', 'downtown'),
       ('jack', 'country yard');
​
explain select *
        from t1
        where id = 1; -- const
explain select *
        from t1
        limit 10; -- all
explain select *
        from t1
        where name = 'tom'; -- ref
explain select t1.id
        from t1,
             t2
        where t1.id = t2.id; -- index, eq_ref
explain select t1.*
        from t1,
             t2
        where t1.id = t2.id; -- index, all
explain select *
        from t1
        where id between 1 and 10; -- range
explain select t1.name
        from t1,
             t2
        where t1.name = t2.name; -- index, ref
explain select *
        from t2,
             (select id from t1 where t1.id = 1) t
        where t2.id = t.id;
explain select *
        from (select * from t1 where t1.address = 'downtown') t;
explain select *
        from t1
        where id = 1
        union
        select *
        from t2
        where id = 2; -- const,const,all
explain select *
        from (select * from t1 limit 1) a1; -- system, all
```

**Extra**

Using where:不用读取表中所有信息，仅通过索引就可以获取所需数据，这发生在对表的全部的请求列都是同一个索引的部分的时候，表示mysql服务器将在存储引擎检索行后再进行过滤。

Using temporary：表示MySQL需要使用临时表来存储结果集，常见于排序和分组查询，常见 group by ; order by。

Using filesort：当Query中包含 order by 操作，而且无法利用索引完成的排序操作称为“文件排序”。

Using join buffer：该值强调了在获取连接条件时没有使用索引，并且需要连接缓冲区来存储中间结果。如果出现了这个值，那应该注意，根据查询的具体情况可能需要添加索引来改进能。

Impossible where：这个值强调了where语句会导致没有符合条件的行（通过收集统计信息不可能存在结果）。

Select tables optimized away：这个值意味着仅通过使用索引，优化器可能仅从聚合函数结果中返回一行

No tables used：Query语句中使用from dual 或不含任何from子句。

### 应用

在很多博客上，都有关于between,&gt;,&lt;,in,not in,like是否会使用索引，如果使用，那么`type`是什么的文章，其实这个问题的本质是对`explain`命令的使用，我们只需要写几个包含以上`where`条件的语句，就可以找到问题的答案了。我们新建一个叫`user`的表，并且插入10万条随机的英文姓名。测试的结果如下。

```text
create table user (
  id   int(11) auto_increment,
  name varchar(100),
  age  int(11),
  primary key (id),
  key index_age(age),
  key index_name(name)
);
​
explain select * from user where name in ('tom'); -- use
explain select * from user where name = 'tom'; -- use
explain select * from user where name between 'tom' and 'jerry'; -- not use
explain select * from user where name <> 'tom'; -- not use
explain select * from user where name = 'tom' or name = 'jerry'; -- use
explain select * from user where name like 'om%'; -- use
explain select * from user where name like 'tom'; -- use, 这里的like相当于等号
explain select * from user where name like '%to'; -- not use
​
explain select * from user where age between 0 and 1; -- use
explain select * from user where age not between 0 and 1; -- not use
explain select * from user where age > 50; -- not use
explain select * from user where age < 50; -- not use
explain select * from user where age != 3; -- not use
explain select * from user where age in (1, 99); -- use
explain select * from user where age = 1 or age = 3; -- use
explain select * from user where age like '1%'; -- not use
explain select * from user where age like '%1'; -- not use
```

从以上的测试结果，我们可以得出结论： `between and, >,<,in,not in,or,like`都是会使用索引的，但是`between and, >,<`必须用在数值类型的列上；`in，not in,or`可以用到数值和字符串的列上；而`like`只能用到字符串类型的列上，而且必须是左边不能以通配符开头。

