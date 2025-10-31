# makefile

## 基础格式

我们假设有这样一个目录：

```text
project/
├── Makefile
├── main.c
├── helper.c
├── helper.h
└── README.md
```

可以建立一个这样的**makefile**文件对其进行编译：

```makefile
# 定义编译器
CC = gcc

# 编译选项
CFLAGS = -Wall -g -I./common -I./main
## 此处还可以指定头文件目录

# 目标可执行文件名
TARGET = out
## target

# 源文件
SRCS = ./main/main.c ./common/node.c
## srcs指定源文件路径 使用绝对路径或者相对路径都行

# 生成对象文件列表
OBJS = $(SRCS:.c=.o)
## objs定义对象文件
## $()使用了linux的shell里的命令替换 将SRCS变量里的所有.c替换成.o作为输出

# 默认目标
all: $(TARGET)
## 当仅输入make时 执行此操作

# 链接目标文件生成可执行文件
$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $(TARGET) $(OBJS)
## 此处将编译生成的.o文件连接成最终产物
## 使用 gcc 加上 目标参数 将 目标文件 编译生成 .o文件
## 此处执行第一句指令时会检查所有的.c文件对于.o有无变化 若无则直接连接 若有编译指定项之后连接

# 编译main.c
./main/main.o: ./main/main.c ./main/main.h
	$(CC) $(CFLAGS) -c ./main/main.c  -o ./main/main.o
## 显式指名 main.o 不仅依赖于 main.c 还依赖于 main.h
## 使用 gcc 加上 目标参数 将 目标文件 编译生成 .o文件

# 编译node.c
./common/node.o: ./common/node.c ./common/node.h
	$(CC) $(CFLAGS) -c ./common/node.c -o ./common/node.o

# 清理生成的文件
clean:
	rm -f $(TARGET) $(OBJS)
## 调用 make clean 清理过程文件

# 声明伪目标
.PHONY: all clean
```

## 核心元素

**makefile**需要指定：

- 编译器CC
- 编译选项CFLAGS
- 目标可执行文件名称TARGET
- 源文件SRCS
- 生成对象文件列表OBJS
- 默认目标all
- 
- 





### 2

### 3

## 标准

在Makefile 的第一部分（宏定义）定义好不同用途的库和include：

