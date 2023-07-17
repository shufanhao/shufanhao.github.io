---
title: Understanding Cascade Left Joins and Writing Complex Queries
categories: sql
tags: sql
abbrlink: e674a0bc
date: 2023-07-15 08:44:30
---

> In SQL, the left join is a powerful tool for combining data from multiple tables based on a common column. In this blog post, we will explore the concept of cascade left joins, providing clear explanations and examples to help you grasp this important technique. Additionally, we will delve into writing complex queries, enabling you to tackle more advanced data retrieval tasks with confidence. 
<!--more-->

## a left join b left join c

The basic each join knowledge, pelase refer [https://zhuanlan.zhihu.com/p/29234064](https://zhuanlan.zhihu.com/p/29234064)

let's give an example: 
* table `total`: total  student in one school, includes two columns student id and room id, named `id` and `room`. 
* table `active`: active student who go to library in the past 30 days, also includes same two columns. 
* table `paid`: paid student who paid for library to get static seat in library in the past 30 days, also includes same two columns. 

Create table: 
```sql
CREATE TABLE IF NOT EXISTS `total` (
  `id` int(6) unsigned NOT NULL,
  `room` varchar(200) NOT NULL,
  PRIMARY KEY (`id`,`room`)
) DEFAULT CHARSET=utf8;
INSERT INTO `total` (`id`, `room`) VALUES
  ('1', '103'),
  ('2', '103'),
  ('3', '103'),
  ('4', '104'),
  ('5', '104'),
  ('6', '105'),
  ('7', '107');

CREATE TABLE IF NOT EXISTS `active` (
  `id` int(6) unsigned NOT NULL,
  `room` varchar(200) NOT NULL,
  PRIMARY KEY (`id`,`room`)
) DEFAULT CHARSET=utf8;
INSERT INTO `active` (`id`, `room`) VALUES
  ('1', '103'),
  ('2','103'),
  ('5', '104');

CREATE TABLE IF NOT EXISTS `paid` (
  `id` int(6) unsigned NOT NULL,
  `room` varchar(200) NOT NULL,
  PRIMARY KEY (`id`,`room`)
) DEFAULT CHARSET=utf8;
INSERT INTO `paid` (`id`, `room`) VALUES
  ('1', '103'),
  ('5', '104');
```
So what's the output of a left join b left join c ? Before get answer, please understand: 

* `A LEFT JOIN B`: This indicates that table A is the left table, and table B is the right table. The left join between A and B returns all rows from table A, along with any matching rows from table B. If there is no match, the columns from table B will contain NULL values.
* `A LEFT JOIN B LEFT JOIN C`: This extends the previous left join to include table C. In this case, the left join between A and B is performed first. Then, the result of that join is left joined with table C. This means that all rows from table A are preserved, along with any matching rows from table B and C. Again, if there is no match, the columns from the respective tables will contain NULL values.

let's see, [http://sqlfiddle.com/#!9/5911603/14/0](http://sqlfiddle.com/#!9/5911603/14/0)

```sql
select
  total.*,
  active.*,
  paid.*
from
  total
  left join active on total.id = active.id
  left join paid on active.id = paid.id
```
if changed to `left join paid on active.id = paid.id` to `left join paid on total.id = paid.id`,  will base on total.id not active.id 

![在这里插入图片描述](https://img-blog.csdnimg.cn/3e4f8eae116241ae93ac02c6cb53a302.png)

## user case 
Let's implement this case, want to know how many student count per room with different student type
*  `inactive_student`:  how many student is inactive student to use library, should be `total left join active on  total.id = active.id where active.id is NULL`
* `paid_active_student`:  how many student is paid active student to use library, should be `active left join paid on active.id = paid.id where paid.id is not NULL`
* `not_paid_active_student`: how many user is not paid active student to use library, should be `active left join paid on active.id = paid.id where paid.id is NULL`

How to achieve above case by one sql query, let's understand step by step. 

sql: [http://sqlfiddle.com/#!9/5911603/3/0](http://sqlfiddle.com/#!9/5911603/3/0)

**IMPORTANT**:  the conditions within the `CASE WHEN` statement are evaluated in `order`, and once a condition evaluates to true, the corresponding result is returned, **and the subsequent conditions are not evaluated.**

```sql
select
  total.id as id,
  total.room as room,
  (
    case 
      /**
      after run first when: active.id is NULL then 'inactive_student', the rest of users are active student. 
      in the following two when, can split into paid_active_student and not_paid_active_student
      **/
      when active.id is NULL then 'inactive_student'
      when paid.id is NOT NULL then 'paid_active_student'
      else 'not_paid_active_student'
    end
  ) AS student_type
from
  total
  left join active on total.id = active.id
  left join paid on active.id = paid.id
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/8d7d7bfffe1b4318b50fc37be59f44ee.png)
Then based on above result to group by to know per room student type. 

sql: [http://sqlfiddle.com/#!9/5911603/13/0](http://sqlfiddle.com/#!9/5911603/13/0

```sql
select
  total.room as room,
  (
    case
      /**
      orderly to match the result
      **/
      when active.id is NULL then 'inactive_student'
      when paid.id is NOT NULL then 'paid_active_student'
      else 'not_paid_active_student'
    end
  ) AS student_type,
  count(total.id) as total_student
from
  total
  left join active on total.id = active.id
  left join paid on active.id = paid.id
group by
  room,
  student_type
order by
  room,
  student_type
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b8fc28eaa2794dd0af7b9247c7caa781.png)

## sql query execution order 

for better understand above use case, let's talk about sql query execution order. If you are familar with it, please skip. 

In SQL, the order of execution of a query is generally as follows:

 1.  `FROM` clause: This specifies the tables or views involved in the query and sets up the initial result set.

2. `JOIN` clause: If there are any join operations specified in the query, the join conditions are evaluated, and the appropriate rows are combined from the joined tables.

3. `WHERE` clause: This filters the rows from the result set based on the specified conditions.

4. `GROUP BY` clause: If grouping is specified, the result set is divided into groups based on the specified grouping columns.

5. `HAVING` clause: This filters the groups from the result set based on the specified conditions.

6. `SELECT` clause: This selects the desired columns from the result set.

7. `DISTINCT` keyword: If present, duplicate rows are eliminated from the result set.

8. `ORDER BY` clause: The result set is sorted based on the specified columns and sort order.

9. `LIMIT` or `OFFSET` clauses: If specified, the result set is limited to a certain number of rows or skipped by a certain number of rows.
