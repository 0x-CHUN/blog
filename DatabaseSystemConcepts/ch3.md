# chapter 3

SQL语言：

* 数据定义语言（Data-Definition Language， DDL）：定义关系模式、删除关系、修改关系模式的命令；
* 数据操作语言（Data-Manipulation Language，DML）：从数据库中查询信息，以及在数据库中插入元组、删除元组、修改元组；
* 完整性：SQL DDL包括定义完整性约束的命令；
* 视图定义
* 事务控制
* 嵌入式SQL和动态SQL
* 授权

## SQL数据定义

基本类型：

* char(n)：固定长度的字符串，长度为n；
* varchar(n)：可变长度的字符串，最大长度为n；
* int
* smallint
* numeric(p,d)：定点数
* real、double、
* float

### 基本模式定义

`create table`定义SQL关系：

```sql
create table department(
    dept name varchar (20),
    building varchar (15),
    budget numeric (12,2),
    primary key (dept name));
```

定义了三个属性，其中`dept name`定义为主码；

插入：`insert into table name values (对应的关系的元组)`

删除元组：`delete from table name`

删除关系：`drop table table_name`

增加属性：`alter table table_name add A D`

## SQL查询的基本结构

