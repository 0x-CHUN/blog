# 语法

## 空白

注释：

* 行注释：`//`

* 块注释：

  ```javascript
  /*注释*/
  ```

## 标识符

标识符：由字母开头，其后可以加上一个或者多个字母、数字或下划线；

标识符不能用以下这些保留字：

```javascript
abstract
boolen byte 
case catch char class const continue 
debugger default delete do double 
else enum export extends 
false final finally float for function 
goto 
if implements import in instanceof int interface 
long 
native new null 
package private protected public
return
short static super switch 
true try typeof 
var volatile void 
while with
```

JavaScript不允许使用上述保留字来命名变量或者参数，不允许在对象字面量中，或者用点运算符提取对象属性时，使用保留字作为对象的属性名。

## 数字

JavaScript只有一个数字类型，在内部表示为64位浮点数；

JavaScript未分离出整数类型，因此1和1.0的值相同，这避免了短整型的溢出问题；

NaN是一个数值，表示一个不能产生正常结果的运算结果。

Infinity表示所有大于**1.79769313486231570e+308**的值；

## 字符串

字符串字面量可以被包在一对单引号或双引号中，包含一个或多个字符。

JavaScript在被创建时，Unicode码为16位的字符集，因此JavaScript的所有字符都是16位的；

`\`为转义字符，用于把那些正常情况下不被允许的字符插入到字符串中，如反斜线、引号和控制字符等；`\u`用来指定数字字符的编码；

## 语句

一个编译单元包含一组可执行的语句，在Web浏览器中，每个`<script>`标签提供一个被编译且立即执行的编译单元。因为缺少链接器，JavaScript把它们一起抛到一个公共的全局名字空间中。

代码块是包在一对花括号里的一组语句，JavaScript中的代码块不会创建新的作用域，因此变量应该被定义在函数的头部，而不是在代码块中。

下面的值会被当做假（false）：

* false
* null
* undefined
* 空字符串''
* 数字0
* 数字NaN

## 表达式

最简单的表达式是字面量、变量、内置的值、以new开头的调用表达式、以delete开头的属性提取表达式、包在圆括号里的表达式、以一个前置运算符作为前导的表达式，或者表达式后面跟着：

* 一个中置运算符与另外一个表达式
* 三元运算符
* 一个函数调用
* 一个属性提取表达式

## 字面量

对象字面量是一种可以方便地按照指定规格创建新对象地表示法。

属性名可以是标识符或字符串。这些名字被当作字面量名而不是变量名来对待，所以对象地属性名在编译时才能知道。属性的值就是表达式。

数组字面量是一种可以方便地按照指定规格创建新数组地表示法。

## 函数

函数字面量定义了函数值，它可以拥有一个可选的名字，用于递归地调用自己。它可以指定一个参数列表，在调用的时候初始化。函数的主题包括变量定义和语句。

