# chapter 3

SQL语言：

* 数据定义语言（Data-Definition Language， DDL）：定义关系模式、删除关系、修改关系模式的命令；
* 数据操作语言（Data-Manipulation Language，DML）：从数据库中查询信息，以及在数据库中插入元组、删除元组、修改元组；
* 完整性：SQL DDL包括定义完整性约束的命令；
* 视图定义
* 事务控制
* 嵌入式SQL和动态SQL
* 授权

推荐：[SQL必知必会](sql/sql_must_know)

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

基本的语句：

```sql
SELECT 属性 FROM 关系 WHERE 条件
```

关键词`distinct`指示去除重复，关键词`all`指示不去重；

select子句可以带有简单的加减乘除的运算，运算对象是常数或元组的属性；

where子句可以使用逻辑连词and、or、not；

SQL查询的含义

1. 为from子句中列出的关系产生笛卡尔积
2. 应用where子句中指定的谓词
3. 输出select子句中指定的属性

### 自然连接

自然连接（natural join）：作用于两个关系，并产生一个关系作为结果，自然连接只考虑那些在两个关系模式中都出现的属性上取值相同的元组对；

形式：

```sql
select xxx from 关系1 natural join 关系2;
```

## 集合运算

SQL作用在关系上的`union、intersect、except`对应集合中的并运算、交运算、差运算；

## 空置

SQL测试为空的语句为`is null`;

## 聚集函数

五个固有的聚集函数：

* 平均值：avg
* 最小值：min
* 最大值：max
* 总和：sum
* 计数：count

### 分组聚集

SQL根据`group by`子句中给出的一个或多个属性来构造分组；

### having子句

having子句用来对分组进行限定；

## 嵌套子查询

子查询是嵌套在另外 一个查询中表达式，子查询嵌套在where子句中；

### 集合成员资格

SQL允许测试元组在关系中的成员资格，`in`测试元组是否是集合中的成员；

### 集合的比较

短语：至少比某一个要大，在SQL中用`> some`表示；同样支持`< some、<= some、>= some、> some、= some、<> some`；

短语：比所有的都大，在SQL中用`> all`表示；同样支持`< all、<= all、>= all、> all、= all、<> all`；

### 空关系测试

`exists`在SQL中用于测试子查询中是否存在元组；exists结构在作为参数的子查询非空时返回true；

### 重复元组存在性测试

SQL提供一个布尔函数，用于测试在一个子查询的结果中是否存在重复元组：unique；

### from子句中的子查询

SQL允许from子句中使用子查询表达式；

### with子句

with子句提供定义临时关系的方法，这个定义只对包含with子句的查询有效；

### 标量子查询

SQL允许子查询出现在返回单个值的表达式能够出现的任何地方，只要该子查询只返回包含单个属性的单个元组；

## 数据库的修改

### 删除

只能删除整个元组，而不能只删除某些属性上的值；

形式：`delete from r where p`

delete只能作用于一个关系；

### 插入

形式：`insert into  r values (xxx , xxx )`

### 更新

形式：`update r set xx=xx`

SQL支持case结构，一般形式如下：

```sql
case
	when xxx then xxx
	...
	else xxx
end
```

