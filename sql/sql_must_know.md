# SQL必知必会

## 了解SQL

### 数据库基础

数据库是一个以某种有组织的方式存储的数据集合。

数据库软件应称为**数据库管理系统**（即DBMS）。数据库是通过DBMS创建和操纵的容器。

* **表**是一种结构化的文件，可用来存储某种特定类型的数据。

  > **表（table）**
  > 某种特定类型数据的结构化清单。

  表具有一些特性，这些特性定义了数据在表中如何存储，包含存储什么样的数据，数据如何分解，各部分信息如何命名等信息。描述表的这组信息就是所谓的**模式**（schema），模式可以用来描述数据库中特定的表，也可以用来描述整个数据库（和其中表的关系）。

* 表由列组成。列存储表中某部分的信息。

  > **列（column）**
  >    表中的一个字段。所有表都是由一个或多个列组成的。

  数据库中每个列都有相应的数据类型。数据类型（datatype）定义了列可以存储哪些数据种类。

  数据类型及其名称是SQL不兼容的一个主要原因。

* 表中的数据是按行存储的，所保存的每个记录存储在自己的行内。

  > **行（row）**
  > 表中的一个记录。

* **主键**:表中每一行都应该有一列（或几列）可以唯一标识自己。

  > **主键（primary key）**
  > 一列（或一组列），其值能够唯一标识表中每一行。

### 什么是SQL

SQL是结构化查询语言（Structured Query Language）的缩写。SQL是一种专门用来与数据库沟通的语言。

## 检索数据

```sql
SELECT prod_name FROM Products;
```

利用`SELECT`语句从`Products`表中检索一个名为`prod_name`的列。

```sql
SELECT prod_id, prod_name, prod_price
FROM Products;
```

从一个表中检索多个列列名之间必须以逗号分隔。

```sql
SELECT * FROM Products;
```

使用星号（`*`）通配符检索所有列

```sql
SELECT DISTINCT vend_id FROM Products;
```

`DISTINCT`关键字，顾名思义，它指示数据库只返回不同的值。`DISTINCT`关键字作用于所有的列，不仅仅是跟在其后的那一列。

### 限制结果

限制返回第一行或者一定数量的行

```sql
SELECT TOP 5 prod_name FROM Products; --SQL Server等
SELECT prod_name FROM Products FETCH FIRST 5 ROWS ONLY; --DBMS
SELECT prod_name FROM Products WHERE ROWNUM <=5; --Oracle
SELECT prod_name FROM Products LIMIT 5;-- MySQL、MariaDB、PostgreSQL或者SQLite
```

指定哪里开始：

```sql
SELECT prod_name FROM Products LIMIT 5 OFFSET 5;
```

### 使用注释

注释：

```sql
-- 注释
/*这也是注释*/
```

## 排序检索数据

`ORDER BY`指定排序的依据

```sql
SELECT prod_name FROM Products ORDER BY prod_name; 
```

### 按多个列排序

```sql
SELECT prod_id, prod_price, prod_name FROM Products ORDER BY prod_price, prod_name; -- 首先按价格，然后按名称排序。
```

要按多个列排序，简单指定列名，列名之间用逗号分开即可。

### 指定排序方向

为了进行降序排序，必须指定`DESC`关键字。

```sql
SELECT prod_id, prod_price, prod_name FROM Products ORDER BY prod_price DESC;
```

```sql
SELECT prod_id, prod_price, prod_name FROM Products ORDER BY prod_price DESC, prod_name;
```

DESC关键字只应用到直接位于其前面的列名。在上例中，只对`prod_price`列指定`DESC`，对`prod_name`列不指定。

## 过滤数据

只检索所需数据需要指定**搜索条件**（search criteria），搜索条件也称为**过滤条件**（filter condition）。

```sql
SELECT prod_name, prod_price FROM Products WHERE prod_price = 3.49;
```

在同时使用`ORDER BY`和`WHERE`子句时，应该让`ORDER BY`位于`WHERE`之后，否则将会产生错误.

**WHERE子句操作符**

| 操作符  |      说   明       |
| :-----: | :----------------: |
|    =    |        等于        |
|   < >   |       不等于       |
|   !=    |       不等于       |
|    <    |        小于        |
|   <=    |      小于等于      |
|    !    |       不小于       |
|    >    |        大于        |
|   >=    |      大于等于      |
|   !>    |       不大于       |
| BETWEEN | 在指定的两个值之间 |
| IS NULL |      为NULL值      |

## 高级数据过滤

### 组合WHERE字句

AND操作符

要通过不止一个列进行过滤，可以使用`AND`操作符给`WHERE`子句附加条件。

```sql
SELECT prod_id, prod_price, prod_name FROM Products WHERE vend_id = 'DLL01' AND prod_price <= 4;
```

OR操作符

`OR`指示DBMS检索匹配任一条件的行

```sql
SELECT prod_name, prod_price FROM Products WHERE vend_id = 'DLL01' OR vend_id = 'BRS01';
```

求值顺序

圆括号具有比`AND`或`OR`操作符更高的求值顺序，所以DBMS首先过滤圆括号内的`OR`条件。

```sql
SELECT prod_name, prod_price FROM Products WHERE (vend_id = 'DLL01' OR vend_id = 'BRS01') AND prod_price >= 10;
```

### IN操作符

`IN`操作符用来指定条件范围，范围中的每个条件都可以进行匹配。`IN`取一组由逗号分隔、括在圆括号中的合法值。

```sql
SELECT prod_name, prod_price FROM Products WHERE vend_id IN ( 'DLL01', 'BRS01' ) ORDER BY prod_name;
```

为什么要使用`IN`操作符？其优点为：

- 在有很多合法选项时，`IN`操作符的语法更清楚，更直观。
- 在与其他`AND`和`OR`操作符组合使用`IN`时，求值顺序更容易管理。
- `IN`操作符一般比一组`OR`操作符执行得更快。

### NOT操作符

`WHERE`子句中的`NOT`操作符有且只有一个功能，那就是否定其后所跟的任何条件。因为`NOT`从不单独使用（它总是与其他操作符一起使用），所以它的语法与其他操作符有所不同。`NOT`关键字可以用在要过滤的列前，而不仅是在其后。

```sql
SELECT prod_name FROM Products WHERE NOT vend_id = 'DLL01' ORDER BY prod_name;
```

## 用通配符进行过滤

### LIKE操作符

1. 百分号（`%`）。在搜索串中，`%`表示任何字符出现任意次数。

   ```sql
   SELECT prod_id, prod_name  FROM Products  WHERE prod_name LIKE 'Fish%'; 
   ```

2. 下划线（`_`）。匹配单个字符，而不是多个字符。

   ```sql
   SELECT prod_id, prod_name FROM Products WHERE prod_name LIKE '__ inch teddy bear';
   ```

3. 方括号（`[]`）通配符用来指定一个字符集，它必须匹配指定位置（通配符的位置）的一个字符。 

   ```sql
   SELECT cust_contact FROM Customers WHERE cust_contact LIKE '[JM]%' ORDER BY cust_contact; --只有微软的Access和SQL Server支持集合。
   ```

## 创建计算字段

### 计算字段

存储在表中的数据都不是应用程序所需要的。我们需要直接从数据库中检索出转换、计算或格式化过的数据，而不是检索出数据，然后再在客户端应用程序中重新格式化。

### 拼接字段

**拼接（concatenate）**
将值联结到一起（将一个值附加到另一个值）构成单个值。

```sql
SELECT vend_name || ' (' || vend_country || ')' FROM Vendors ORDER BY vend_name; -- Access和SQL Server使用+号。DB2、Oracle、PostgreSQL、SQLite和Open Office Base使用||。
```

**TRIM函数**
大多数DBMS都支持`RTRIM()`（去掉字符串右边的空格）、`LTRIM()`（去掉字符串左边的空格）以及`TRIM()`（去掉字符串左右两边的空格）。

```sql
SELECT RTRIM(vend_name) || ' (' || RTRIM(vend_country) || ')' FROM Vendors ORDER BY vend_name;
```

**别名**

别名（alias）是一个字段或值的替换名。别名用`AS`关键字赋予。

```sql
SELECT RTRIM(vend_name) || ' (' || RTRIM(vend_country) || ')'
       AS vend_title
FROM Vendors
ORDER BY vend_name;
```

### 执行算术计算

计算字段的另一常见用途是对检索出的数据进行算术计算。

```sql
SELECT prod_id,
       quantity,
       item_price,
       quantity*item_price AS expanded_price
FROM OrderItems
WHERE order_num = 20008;
```

## 使用数据处理函数

大多数SQL实现支持以下类型的函数：

- 用于处理文本字符串（如删除或填充值，转换值为大写或小写）的文本函数。
- 用于在数值数据上进行算术操作（如返回绝对值，进行代数运算）的数值函数。
- 用于处理日期和时间值并从这些值中提取特定成分（如返回两个日期之差，检查日期有效性）的日期和时间函数。
- 返回DBMS正使用的特殊信息（如返回用户登录信息）的系统函数。

**文本处理函数**

```sql
SELECT vend_name, UPPER(vend_name) AS vend_name_upcase
FROM Vendors
ORDER BY vend_name;
```

|               函　　数                |       说　　明        |
| :-----------------------------------: | :-------------------: |
|     LEFT()（或使用子字符串函数）      | 返回字符串左边的字符  |
| LENGTH()（也使用DATALENGTH()或LEN()） |   返回字符串的长度    |
|     LOWER()（Access使用LCASE()）      |  将字符串转换为小写   |
|                LTRIM()                | 去掉字符串左边的空格  |
|     RIGHT()（或使用子字符串函数）     | 返回字符串右边的字符  |
|                RTRIM()                | 去掉字符串右边的空格  |
|               SOUNDEX()               | 返回字符串的SOUNDEX值 |
|     UPPER()（Access使用UCASE()）      |  将字符串转换为大写   |

**日期和时间处理函数**

DBMS提供的功能远不止简单的日期成分提取。大多数DBMS具有比较日期、执行基于日期的运算、选择日期格式等的函数。但是，可以看到，不同DBMS的日期-时间处理函数可能不同。关于具体DBMS支持的日期-时间处理函数，请参阅相应的文档。

**数值处理函数**

| 函　　数 |      说　　明      |
| :------: | :----------------: |
|  ABS()   | 返回一个数的绝对值 |
|  COS()   | 返回一个角度的余弦 |
|  EXP()   | 返回一个数的指数值 |
|   PI()   |     返回圆周率     |
|  SIN()   | 返回一个角度的正弦 |
|  SQRT()  | 返回一个数的平方根 |
|  TAN()   | 返回一个角度的正切 |

关于具体DBMS所支持的算术处理函数，请参阅相应的文档。

## 汇总数据

### 聚集函数

SQL查询可用于检索数据，以便分析和报表生成。这种类型的检索例子有：

- 确定表中行数（或者满足某个条件或包含某个特定值的行数）；
- 获得表中某些行的和；
- 找出表列（或所有行或某些特定的行）的最大值、最小值、平均值。

>  **聚集函数（aggregate function）**
> 对某些行运行的函数，计算并返回一个值。

| 函　　数 |     说　　明     |
| :------: | :--------------: |
|  AVG()   | 返回某列的平均值 |
| COUNT()  |  返回某列的行数  |
|  MAX()   | 返回某列的最大值 |
|  MIN()   | 返回某列的最小值 |
|  SUM()   |  返回某列值之和  |

```sql
SELECT AVG(prod_price) AS avg_price
FROM Products;
```

**警告：只用于单个列**
**AVG()**只能用来确定特定数值列的平均值，而且列名必须作为函数参数给出。为了获得多个列的平均值，必须使用多个`AVG()`函数。

`COUNT()`函数有两种使用方式：

- 使用`COUNT(*)`对表中行的数目进行计数，不管表列中包含的是空值（`NULL`）还是非空值。
- 使用`COUNT(column)`对特定列中具有值的行进行计数，忽略`NULL`值。

```sql
SELECT COUNT(*) AS num_cust
FROM Customers;
```

```sql
SELECT COUNT(cust_email) AS num_cust
FROM Customers; 
```

`MAX()`返回指定列中的最大值。`MAX()`要求指定列名

```sql
SELECT MAX(prod_price) AS max_price
FROM Products;
```

`MIN()`的功能正好与`MAX()`功能相反，它返回指定列的最小值。

```sql
SELECT MIN(prod_price) AS min_price
FROM Products;
```

`SUM()`用来返回指定列值的和（总计）。

```sql
SELECT SUM(quantity) AS items_ordered
FROM OrderItems
WHERE order_num = 20005;
```

### 聚集不同值

以上5个聚集函数都可以如下使用：

- 对所有行执行计算，指定`ALL`参数或不指定参数（因为`ALL`是默认行为）。
- 只包含不同的值，指定`DISTINCT`参数。

使用`AVG()`函数返回特定供应商提供的产品的平均价格。它与上面的`SELECT`语句相同，但使用了`DISTINCT`参数，因此平均值只考虑各个不同的价格：

```sql
SELECT AVG(DISTINCT prod_price) AS avg_price
FROM Products
WHERE vend_id = 'DLL01'; -- DISTINCT`**不能用于**`COUNT(*)
```

### 组合聚集函数

```sql
SELECT COUNT(*) AS num_items,
       MIN(prod_price) AS price_min,
       MAX(prod_price) AS price_max,
       AVG(prod_price) AS price_avg
FROM Products;
```

## 分组数据

### 创建分组

分组是使用`SELECT`语句的`GROUP BY`子句建立的。

```sql
SELECT vend_id, COUNT(*) AS num_prods
FROM Products
GROUP BY vend_id;
```

### 过滤分组

`HAVING`非常类似于`WHERE`。事实上，目前为止所学过的所有类型的`WHERE`子句都可以用`HAVING`来替代。唯一的差别是，`WHERE`过滤行，而`HAVING`过滤分组。

```sql
SELECT cust_id, COUNT(*) AS orders
FROM Orders
GROUP BY cust_id
HAVING COUNT(*) >= 2;
```

**SELECT子句及其顺序**    

| 子　　句 |      说　　明      |      是否必须使用      |
| :------: | :----------------: | :--------------------: |
|  SELECT  | 要返回的列或表达式 |           是           |
|   FROM   |  从中检索数据的表  | 仅在从表选择数据时使用 |
|  WHERE   |      行级过滤      |           否           |
| GROUP BY |      分组说明      | 仅在按组计算聚集时使用 |
|  HAVING  |      组级过滤      |           否           |
| ORDER BY |    输出排序顺序    |           否           |

## 使用子查询

### 子查询

子查询（subquery），即嵌套在其他查询中的查询。

现在，假如需要列出订购物品`RGAN01`的所有顾客，应该怎样检索？下面列出具体的步骤。  

1. 检索包含物品`RGAN01`的所有订单的编号。
2. 检索具有前一步骤列出的订单编号的所有顾客的ID。
3. 检索前一步骤返回的所有顾客ID的顾客信息。

```sql
SELECT cust_name, cust_contact
FROM Customers
WHERE cust_id IN (SELECT cust_id
                  FROM Order
                  WHERE order_num IN (SELECT order_num
                                      FROM OrderItems
                                      WHERE prod_id = 'RGAN01'));
```

### 作为计算字段使用子查询

```sql
SELECT cust_name, 
       cust_state,
       (SELECT COUNT(*) 
        FROM Orders 
        WHERE Orders.cust_id = Customers.cust_id) AS orders
FROM Customers 
ORDER BY cust_name; 
```

## 联结表

用于把来自两个或多个表的行结合起来。

### 创建联结

```sql
SELECT vend_name, prod_name, prod_price
FROM Vendors, Products
WHERE Vendors.vend_id = Products.vend_id;
```

### 内联结

基于两个表之间的相等测试的联结。

```sql
SELECT vend_name, prod_name, prod_price
FROM Vendors INNER JOIN Products
ON Vendors.vend_id = Products.vend_id;
```

### 联结多个表

```sql
SELECT prod_name, vend_name, prod_price, quantity
FROM OrderItems, Products, Vendors
WHERE Products.vend_id = Vendors.vend_id
AND OrderItems.prod_id = Products.prod_id
AND order_num = 20007;
```

## 创建高级联结

### 使用表别名

```sql
SELECT cust_name, cust_contact
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
AND OI.order_num = O.order_num
AND prod_id = 'RGAN01';
```

### 自联结

```sql
SELECT c1.cust_id, c1.cust_name, c1.cust_contact
FROM Customers AS c1, Customers AS c2
WHERE c1.cust_name = c2.cust_name
AND c2.cust_contact = 'Jim Jones'; 
```

### 自然联结

自然联结排除多次出现，使每一列只返回一次。

```sql
SELECT C.*, O.order_num, O.order_date,
       OI.prod_id, OI.quantity, OI.item_price
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
 AND OI.order_num = O.order_num
 AND prod_id = 'RGAN01';
```

### 外联结

输出没有关联行的那些行

```sql
SELECT Customers.cust_id, Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
 ON Customers.cust_id = Orders.cust_id; 
```

### 使用带聚集函数的联结

```sql
SELECT Customers.cust_id,
       COUNT(Orders.order_num) AS num_ord
FROM Customers INNER JOIN Orders
 ON Customers.cust_id = Orders.cust_id
GROUP BY Customers.cust_id
```

## 组合查询

允许执行多个查询（多条SELECT语句），并将结果作为一个查询结果集返回。这些组合查询通常称为并（union）或复合查询（compound query）。

```sql
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_state IN ('IL','IN','MI')
UNION
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_name = 'Fun4All';
```

### UNION规则

UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔;UNION中的每个查询必须包含相同的列、表达式或聚集函;列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含转换的类型。

### 包含或取消重复的行

重复的行会被自动取消，这是UNION的默认行为。

使用`UNION ALL`返回所有行；

### 对组合查询结果排序

只能在最后的一条`UNION`后使用`ORDER BY`;

## 插入数据

### 数据插入

插入有几种方式：

* 插入完整的行

  ```sql
  INSERT INTO Customers(
      -- 表项列名
  )
  VALUES(
      -- 列名对应的值
  )
  ```

* 插入行的一部分(代码同上)

* 插入某些查询的结果

  ```sql
  INSERT INTO Customers(
  )
  SELECT -- 列名对应的值
  ```

### 表的复制

使用`INSERT INTO`

```sql
SELECT * 
INTO CustCopy
FROM Customers
```

## 跟新和删除数据

### 跟新数据

使用update的方式：

* 更新特定的行
* 更新所有的行

```sql
UPDATE Customer SET cust_email = 'kim@thetoystore.com'
WHERE cust_id = 'xxxx';
```

### 删除数据

使用delete的方式：

- 删除特定的行
- 删除所有的行

```sql
DELETE FROM Customer
WHERE cust_id = 'xxx';
```

> 一般都有WHERE，没有就会应用到所有行！！

## 创建和操作表

### 创建表

```sql
CREATE TABLE Products(
    prod_id CHAR(10) NOT NULL, 
    vend_id CHAR(10) NOT NULL,
    prod_name CHAR(256) NOT NULL,
    prod_price DECIMAL(8,2) NOT NULL,
    prod_desc VARCHAR(1000) NULL DEFAULT 'xxx' -- 默认值
)
```

注意不同数据库的创建有差异！ 

**NOT NULL**说明该列不能为空，为空则会报错。 

主键永远不能为空！！

 DEFAULT指定默认值。

### **更新表** 

```sql
ALTER TABLE VendorsADD  vend_phone CHAR(20);-- 增加一个名字为vend_phone的列 

ALTER TABLE VendorsDROP COLUMN vend_phone;-- 删除列（不一定对所有DBMS生效）
```

### **删除表** 

DROP TABLE xxxx;-- 删除整个表 

**视图**

视图时虚拟的表，只包含使用时动态检索数据的查询。 

例如，假设从三个表中检索数据：

```sql
 SELECT cust_name, cust_contactFROM Customers, Orders, OrderItems
WHERE Customers.cust_id = Orders.cust_id 
AND OrderItems.order_num = Orders.order_num
AND prod_id = 'RGAN01'; 
```

可以包装成一个名为ProductCustomers的虚拟表 

```sql
SELECT cust_name, cust_contact
FROM ProductCustomers WHERE prod_id = 'RGAN01'; 
```

这就是视图的作用：

1. 重用SQL语句
2. 简化复杂SQL操作
3. 使用表的一部分而不是整个表
4. 保护数据
5. 更改数据格式和表示 

**创建视图** 

创建：**CREATE VIEW viewname** 

删除：**DROP VIEW viewname**

 简化复杂操作：

```sql
CREATE　VIEW　ProductCustomers AS
SELECT cust_name, cust_contactFROM Customers, Orders, OrderItems
WHERE Customers.cust_id = Orders.cust_id
AND OrderItems.order_num = Orders.order_num
AND prod_id = 'RGAN01'; 
```

重新格式化检索出的数据： 

```sql
SELECT RTRIM（vend_name) + '(' + RTRIM(vend_country) + ')' AS vend_title FROM Vendors 
ORDER BY vend_name; 
```

使用视图过滤数据： 

```sql
CREATE VIEW CustomerEmailList AS
SELECT cust_id, cust_name, cust_email
FROM CustomersWHERE cust_email IS NOT NULL 
```

## **存储过程**

存储过程就是为以后使用而保存的一条或者多条SQL语句不同DMBS的创建存储过程不一致！ 

## **管理事务处理**

略

## **游标**

略

## **高级特性**

略