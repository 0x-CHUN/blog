# chapter 5

## 使用程序设计语言访问数据库

SQL是声明性的查询语言，没有提供通用程序设计语言的表达能力；

从通用编程语言访问SQL:

* 动态SQL：程序设计语言可以通过函数或者方法来连接数据库并与之交互；
* 嵌入式SQL：嵌入式SQL语句必须在编译时确定；

### JDBC

Java程序连接数据库服务器的API；

### ODBC

开放数据库互联（Open Database Connectivity）标准定义了一个API来打开一个数据库连接、发送查询和更新，以及获取返回结果；

### 嵌入式SQL

SQL标准定义了嵌入SQL到不同的语言中，SQL查询所嵌入的语言被称为宿主语言，宿主语言中使用的SQL结构称为嵌入式SQL；

形式：`EXEC SQL <嵌入式SQL语句>`

## 函数和过程

函数和过程允许业务逻辑作为存储过程记录在数据库中，并在数据库内执行；

### 声明和调用SQL函数和过程

SQL标准支持返回关系作为结果的函数，这类函数称为**表函数**；

形式：

```sql
create function 函数名(参数)
	returns 关系
	begin
		SQL语句
		return 关系
	end
```

SQL也支持过程：

```sql
create procedure 过程名（in 输入,out 输出)
	begin
		SQL语句，into 指出输出
	end
```

可以从一个SQL过程或者从嵌入式SQL语句中使用call语句调用过程；

### 支持过程和函数的语言构造

SQL支持的构造赋予了同通用程序设计语言相当的几乎所有的功能；SQL标准中处理这些构造的部分称为**持久存储模块（Persistent Storage Module）**；

变量通过**declare**语句进行声明，使用set进行赋值；

一个复合语句有**begin ... end**的形式，在begin和end之间会包含复杂的SQL语句；

SQL支持while语句和repeat语句：

```sql
while 布尔表达式 do
	语句序列;
end while
-------------------
repeat
	语句序列;
until 布尔表达式
end repeat
```

SQL支持for循环，允许遍历查询结果；

SQL支持`if-then-else`语句：

```sql
if 布尔表达式
	then 语句
elseif 布尔表达式
	then 语句
else 语句
end if
```

SQL还支持发信号通知**异常条件**，以及声明句柄来处理异常；

## 触发器

触发器是一条语句，当对数据库修改时，它会被系统自动执行；

设置触发条件的要求：

* 指明什么条件下执行触发器
* 指明触发器执行时的动作

when语句指定一个条件；