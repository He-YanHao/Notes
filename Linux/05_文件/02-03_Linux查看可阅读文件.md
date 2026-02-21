# Linux查看可阅读文件

## 输出到终端cat

基础的查看指令，将文件的内容输出到终端。



## 加行号输出到终端nl

在 cat 的基础上增加了行号。



## 按页查看 more

以页为单位查看文本文件。



## 高级查看 less

更强大的文本查看命令

| 命令                        | 操作           |      |
| --------------------------- | -------------- | ---- |
| 上翻页键(Page Up) 或 b      | 向前一页       |      |
| 下翻页键(Page Down) 或 空格 | 向后一页       |      |
| 上方向键                    | 向前一行       |      |
| 下方向键                    | 向后一行       |      |
| G                           | 移动到末尾     |      |
| 1G 或 g                     | 移动到开头     |      |
| /搜索内容                   | 搜索前文       |      |
| n                           | 重复上一次搜索 |      |
| h                           | 帮助信息       |      |
| q                           | 推出 less      |      |



## 查看文本的基本属性 wc

输出文本的基本属性，包含三个数字，分别是文件包含的 行数 单词数 字节数。



## 搜索 grep

`grep` 源自于 `g/re/p`（globally search a **r**egular **e**xpression and **p**rint），它的核心功能就是在文本中**搜索指定的模式（pattern）**，并将匹配到的行打印出来。

**基本语法**

```bash
grep [选项] '搜索模式' [文件...]
```

**核心常用选项**

假设我们有一个名为 `example.txt` 的文件，内容如下：
```
Hello World
This is a test file.
I love Linux and Linux is powerful.
Another line.
The test is over.
```

**基本搜索**

在文件中搜索包含 “test” 的行。

```bash
grep "test" example.txt
# 输出：
# This is a test file.
# The test is over.
```

**忽略大小写**   **-i**

搜索 “hello”，不区分大小写。

```bash
grep -i "hello" example.txt
# 输出：
# Hello World
```

**显示行号**   **-n**

搜索 “Linux” 并显示行号。

```bash
grep -n "Linux" example.txt
# 输出：
# 3:I love Linux and Linux is powerful.
```

**全字匹配**   **-w**

只匹配作为单词的 “is”，而不是 “This” 或 “this” 的一部分。

```bash
grep -w "is" example.txt
# 输出：
# This is a test file. (匹配的是中间的 is，而不是 This 里的 is)
# The test is over.
```

**递归搜索**   **-r**

在当前目录及其所有子目录的文件中搜索 “main()” 函数。

```bash
grep -r "main()" /path/to/source/code/
```

**显示上下文**   **-C num**

搜索 “Linux” 并显示其前后各 1 行。

```bash
grep -C 1 "Linux" example.txt
# 输出：
# This is a test file.
# I love Linux and Linux is powerful.
# Another line.
```

### 结合正则表达式

`grep` 的真正威力在于与正则表达式（Regular Expression）的结合。

**搜索以某词开头的行**

搜索以 “The” 开头的行。

```bash
grep "^The" example.txt
# 输出：
# The test is over.
```

**搜索以某词结尾的行**

搜索以 “.” 结尾的行。

```bash
grep "\.$" example.txt # 注意：. 是元字符，需要用 \ 转义
```

**使用扩展正则表达式**

搜索 “is” 或 “test”。

```bash
grep -E "is|test" example.txt
# 或者使用 egrep
egrep "is|test" example.txt
```

## 查看头尾head/tail

仅输出开头或结尾



## 对比不同 diff



