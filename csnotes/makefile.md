# Makefile学习

## 基本

最基本的结构：

```makefile
target: Prerequisites
	command
	...
```

wildcard函数：

```makefile
src = $(wildcard ./*.cpp)
```

获取所有后缀为cpp的文件的文件名；

patsubst函数：

```makefile
obj = $(patsubst %.cpp, %.o,$(src))
```

将src里的每个文件都由.cpp替换成.o，并存入obj；

makefile中使用变量：

```makefile
objects = a.o b.o c.o
# 使用变量用$
clean : rm $(objects)
```

GNU的make 很强大，它可以自动推导文件以及文件依赖关系后面的命令，于是就没必要去在
每一个 .o 文件后都写上类似的命令，因为，make 会自动识别，并自己推导命令。

只要 make 看到一个 .o 文件，它就会自动的把 .c 文件加在依赖关系中，如果 make 找到一个
whatever.o ，那么 whatever.c 就会是 whatever.o 的依赖文件。

每个 Makefile 中都应该写一个清空目标文件（.o 和执行文件）的规则，这不仅便于重编译，也很利
于保持文件的清洁。

引用其他makefile：

```makefile
include <filaname>
```

make 支持三个通配符:

* *
* ?
* ~

### 文件搜寻

在一些大的工程中，有大量的源文件，我们通常的做法是把这许多的源文件分类，并存放在不同的
目录中。所以，当 make 需要去找寻文件的依赖关系时，你可以在文件前加上路径，但最好的方法是把一个路径告诉 make，让 make 在自动去找。

Makefile 文件中的特殊变量 VPATH 就是完成这个功能的，如果没有指明这个变量，make 只会在当
前的目录中去找寻依赖文件和目标文件。如果定义了这个变量，那么，make 就会在当前目录找不到的情况下，到所指定的目录中去找寻文件了。

```makefile
VPATH = src:../headers
```

上面的定义指定两个目录，“src”和“../headers”，make 会按照这个顺序进行搜索。目录由“冒号”
分隔(当前目录永远是最高优先搜索的地方)。

另一个设置文件搜索路径的方法是使用 make 的“vpath”关键字：

```makefile
vpath <pattern> <directories> 
# 为符合模式 <pattern> 的文件指定搜索目录 <directories>。
vpath <pattern> 
# 清除符合模式 <pattern> 的文件的搜索目录。
vpath 
# 清除所有已被设置好了的文件搜索目录。
```

vapth 使用方法中的` <pattern>`需要包含 % 字符。% 的意思是匹配零或若干字符。

可以连续地使用 vpath 语句，以指定不同搜索策略。如果连续的 vpath 语句中出现了相同的
`<pattern>` ，或是被重复了的` <pattern>`，那么，make 会按照 vpath 语句的先后顺序来执行搜索。

### 伪目标

“伪目标”并不是一个文件，只是一个标签，由于“伪目标”不是文件，所以 make 无法生成它的依赖关系和决定它是否要执行。

```makefile
# “.PHONY”显式地指明一个目标是“伪目标”
.PHONY : clean
clean :
	rm *.o temp
```

### 多目标

Makefile 的规则中的目标可以不止一个，其支持多目标

```makefile
bigoutput littleoutput : text.g
	generate text.g -$(subst output,,$@) > $@
# 等价于

bigoutput : text.g
	generate text.g -big > bigoutput
littleoutput : text.g
	generate text.g -little > littleoutput
```

`-$(subst output,,$@) `中的 `$`表示执行一个 Makefile 的函数，函数名为 subst，后面的为参数。这个函数是替换字符串的意思，`$@` 表示目标的集合，就像一个数组，`$@`依次取出目标，并执于命令。

