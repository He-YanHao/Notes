# shell工具

## 此处文档 Here Document

Here Document（通常简称为 heredoc）是 Shell 脚本中一种强大的输入重定向功能，它允许你在脚本中直接嵌入多行文本内容，并将其作为命令的输入。

### 基本语法

Here Document 的基本语法如下：

```bash
command << delimiter
    text line 1
    text line 2
    ...
    text line N
delimiter
```

或者使用更常见的格式：

```bash
command <<EOF
文本内容...
多行文本...
EOF
```

- <<：Here Document 的重定向操作符
- delimiter：分隔符，可以是任何字符串（通常使用 EOF、END 或自定义字符串）
- 文本内容：位于分隔符之间的多行文本
- 结束分隔符：单独一行，仅包含分隔符（不能有前导或尾随空格）

```shell
# 使用 cat 命令显示多行文本
cat << EOF
这是第一行
这是第二行
这是第三行
EOF
```

## basename

可以去掉文件的扩展名，或者去掉路径全名中的目录部分。

```shell
basename example.html .html
basename /usr/local/bin/example
```

以上两条命令都会返回example。

其中第一条会将example.html中的后缀.html去掉，第二条会将全路径中的目录部分去掉。



## awk







## sed





















## xargs









## expr

可以进行算术操作。









## exec



