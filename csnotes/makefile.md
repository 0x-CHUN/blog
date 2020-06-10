# Makefile学习

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