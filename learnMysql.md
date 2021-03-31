#### 概念

>每一行称为`记录`
>
>每一列称为`字`
>
>`字段应该避免为NULL,不允许为NULL可以简化查询条件`
>
>关系表分为`一对多` `多对一` `一对一` 的关系
>
>`两条记录不能重复`  指**主键**不能重复
>
>主键选取与业务无关的字段，如:`id`
>
>`int自增类型`  `int`只支持<21亿  `bigint`支持912亿亿条记录
>
>`联合主键`  多个字段均设为主键，只要不是都重复就可以
>
>`外键` 通过一个字段关联到另外一张表 
>
>外键约束会降低数据库性能，所以一般不设置外键约束而是靠逻辑实现

>`索引` 对某一列或多个列的值进行预排列的数据结构 ：  该列越不相同，索引效率越高 
>
> `ADD INDEX idx_score(score)` 对score列创建索引
>
>插入，更新、删除记录是会修改索引
>
>`UNIQUE` 关键字创建唯一索引
>
>`ADD CONSTRAINT uni_name UNIQUE (name);`  创建唯一约束而不创建索引



>查询数据

`SELECT * FROM students` 查询表内所有记录

`SELECT 1 ;` 测试数据库是否连接

`SELECT * FROM students WHERE score >= 80`条件查询

`gender = 'M'`  匹配字符需要用单引号括起来

`OR` 或  `AND`并 `NOT` 非

>投影查询  只返回指定列

`SELECT id ，score , name FROM students;`



>排序

`ORDER BY score DESC`  从高到低排序 order默认从低到高  即升序`ASC`

`ORDER BY score DESC, gender;`  先按socre排序，再按gender排序



>分页 

> `ORDER BY score DESC LIMIT 3 OFFSET 0`  
>
>每页分为3条记录，获取第1页
>
>OFFSET 3 获取第二页



>聚合查询

`SELECT COUNT(*) num FROM students`  查询共有多少条记录

SUM 合计   AVG 平均值  MAX 最大值  MIN 最小值   `不匹配会返回NULL`



>分组查询  

`SELECT COUNT(*) num FROM students GROUP BY class_id` 按class_id分组并计算总数

`SELECT class_id,gender,COUNT(*)num FROM students GROUP BY class_id,gender; ` 按class_id和gender进行分组

>多表查询

`SELECT * FREOM students, classes`   这样查询后是笛卡尔集，即10x10为  100条记录

`SELECT s.id sid, s.name,s.gender,s.score,c.id cid,c.name cname FROM students s, classes c
WHERE s.gender = 'M' AND c.id = 1;`   这样查询可以大大减少记录

>连接查询

`SELECT s.id , s.name , s.class_id , c.name class_name ,s.gender ,s.score FROM students s INNER JOIN class s ON s.class_id = c.id` 连接两张表查询

INNER JOIN ON  内连接 只返回两张表都存在的记录

RIGHT OUTER JOIN ON 右外连接，返回右表都存在的行，左表不存在返回NULL

LEFT OUTER JOIN ON 左外连接 ，====

FULL OUTER JOIN ON   两张表所有记录全部选择出来



#### 修改数据

>增删改查

>增 ：插入
>
>` INSERT INTO students (class_id , name ,gender ,score) VALUES (2,'小明'，'M'，80)，(1，'小红'，'M',99)`
>
>

>改：更新
>
>` UPDATA students SET name='大牛',score=66 WHERE id=1`

>删：删除
>
>`DELETE FROM students where id=1`

#### 高级操作

>插入或者覆盖
>
>`REPLACE INTO students (id , class_id , name ,gender ,score) VALUES (1,1,'小明','F',80)`  有则覆盖。 无则插入

>插入或者忽略
>
>`INSERT IGNORE INTO ` 有则什么也不做，无则插入

>对表进行快照
>
>`CREATE TABLE student_copy SELECT * FROM students`

>查询结果写入新表
>
>`创建表`
>
>`INSERT INTO stunew (class_id, average) SELECT class_id , AVG(score) FROM students GROUP BY class_id`

>强制使用指定索引
>
>`SELECT * FROM students FORCE INDEX (idx_class_id) WHERE class_id = 1 ORDER BY id DESC;`  强制使用idx_class_id作为表的索引





>mysql使用

`mysql -u root -p` 登入

exit退出

`mysql -h  192.168.3.123 -u root -p`   -h 指定ip



>information_schema   mysql  performance_schema sys 是系统库表，不能动

`CREATE DATABASE test` 创建数据库

`DROP DATABASE test` 删除数据库

`USE test` 切换到数据库

`SHOW TABLES` 列出数据库中所有表





#### 事务

>银行转账，一加一减同时进行， 一个失败，另一个也失败。。称为一个事务

> 单条语句数据库自动将其作为一个事务执行，称为隐式事务。。 

> 显式事务，手动将多条sql语句作为一个事务执行

`BEGIN`

`update  =======`

`update ======`

`COMMIT`

> 主动让事务失败

`BEGIN`

`update  ======`

`update ======`

`ROLLBACK`



>事务的并发操作可能会导致数据不一致。。 脏读，不可重读，幻读

>脏读：一个事务读到另一个事务更新但未提交（读到后回滚了）的数据
>
>不可重复读：一个事务读的过程另一个事务对该条记录进行了修改（读了两次，所以两次不一样）
>
>幻读：一个事务在读之前没有，读的过程中另一个事务进行了操作
>
>serializable : 事务依次执行，不会出现以上情况。。但效率低，很少用

