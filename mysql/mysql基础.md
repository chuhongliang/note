## MYSQL基础

### 数据库三范式
- 第一范式：1NF是对属性的原子性约束，要求字段具有原子性，不可再分解；(只要是关系型数据库都满足1NF)

- 第二范式：2NF是在满足第一范式的前提下，非主键字段不能出现部分依赖主键；解决：消除复合主键就可避免出现部分以来，可增加单列关键字。

- 第三范式：3NF是在满足第二范式的前提下，非主键字段不能出现传递依赖，比如某个字段a依赖于主键，而一些字段依赖字段a，这就是传递依赖。解决：将一个实体信息的数据放在一个表内实现。
  
### SQL语句分类
- DDL：数据定义语言（create drop）
- DML：数据操作语句（insert update delete）
- DQL：数据查询语句（select ）
- DCL：数据控制语句，进行授权和权限回收（grant revoke）
- TPL：数据事务语句（commit collback savapoint）
  
### CHAR和VARCHAR的区别
- CHAR和VARCHAR类型在存储和检索方面有所不同
- CHAR列长度固定为创建表时声明的长度，长度值范围是1到255
- 当CHAR值被存储时，它们被用空格填充到特定长度，检索CHAR值时需删除尾随空格
  
### 锁
- 表级锁：开销小，加锁快，不会出现死锁。锁定粒度大，发生锁冲突的概率最高，并发量最低
- 行级锁：开销大，加锁慢，会出现死锁。锁力度小，发生锁冲突的概率小，并发度最高

### delete、drop、truncate区别
- truncate 和 delete只删除数据，不删除表结构 ,drop删除表结构，并且释放所占的空间。
- 删除数据的速度，drop> truncate > delete
- delete属于DML语言，需要事务管理，commit之后才能生效。drop和truncate属于DDL语言，操作立刻生效，不可回滚。

### 存储引擎MyISAM和InnoDB区别
- InnoDB支持事务，MyISAM不支持。
- MyISAM适合查询以及插入为主的应用，InnoDB适合频繁修改以及涉及到安全性较高的应用。
- InnoDB支持外键，MyISAM不支持。
- 从MySQL5.5.5以后，InnoDB是默认引擎。
- MyISAM支持全文类型索引，而InnoDB不支持全文索引。
- InnoDB中不保存表的总行数，select count(*) from table时，InnoDB需要扫描整个表计算有多少行，但MyISAM只需简单读出保存好的总行数即可。注：当count(*)语句包含where条件时MyISAM也需扫描整个表。
- 对于自增长的字段，InnoDB中必须包含只有该字段的索引，但是在MyISAM表中可以和其他字段一起建立联合索引。
- 清空整个表时，InnoDB是一行一行的删除，效率非常慢。MyISAM则会重建表。MyisAM使用delete语句删除后并不会立刻清理磁盘空间，需要定时清理，命令：OPTIMIZE table dept;
- InnoDB支持行锁（某些情况下还是锁整表，如 update table set a=1 where user like ‘%lee%’）
- Myisam创建表生成三个文件：.frm 数据表结构 、 .myd 数据文件 、 .myi 索引文件，Innodb只生成一个 .frm文件，数据存放在ibdata1.log
- 现在一般都选用InnoDB，主要是MyISAM的全表锁，读写串行问题，并发效率锁表，效率低，MyISAM对于读写密集型应用一般是不会去选用的。

### 应用场景 
- MyISAM不支持事务处理等高级功能，但它提供高速存储和检索，以及全文搜索能力。如果应用中需要执行大量的SELECT查询，那么MyISAM是更好的选择。
InnoDB用于需要事务处理的应用程序，包括ACID事务支持。如果应用中需要执行大量的INSERT或UPDATE操作，则应该使用InnoDB，这样可以提高多用户并发操作的性能。