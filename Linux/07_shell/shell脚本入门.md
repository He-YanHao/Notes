# shell脚本入门

## shell介绍

通过 `cat /etc/shells` 可以产看电脑上安装了那些 shell：

```shell
hing@he-books ~> cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/usr/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/usr/bin/dash
/usr/bin/fish
```

其中

```shell
/bin/sh
/usr/bin/sh
```

这通常指向系统默认的Shell。在很多现代 Linux 系统（如 Debian、Ubuntu）上，`/bin/sh` 和 `/usr/bin/sh` 都是指向 `/bin/dash` 或 `/bin/bash` 的**符号链接**。它们不是独立的 Shell，只是为了保持兼容性而存在的链接。

```shell
/bin/bash
/usr/bin/bash
```

这是 **Bash Shell** 的两个常见安装路径。同样，它们通常是同一个程序的**硬链接**或分别安装在两个目录下的相同副本。Bash 是大多数 Linux 发行版的默认 Shell。

```shell
/bin/rbash
/usr/bin/rbash
```

这是 **受限模式的 Bash**。它仍然是 Bash 程序，但以一种受限的模式启动，限制了某些功能（如不能切换目录 `cd`，不能设置某些环境变量等），常用于创建权限受限制的用户账户。它通常是指向 `bash` 的链接。

```shell
/usr/bin/dash
```

这是 **Debian Almquist Shell**。它是一个轻量级、高效且符合 POSIX 标准的 Shell。它的启动速度比 Bash 快，因此被许多 Linux 发行版（如 Debian、Ubuntu）选为默认的系统脚本解释器（即 `/bin/sh` 指向它）。

```shell
/usr/bin/fish
```

这是 **Friendly Interactive Shell**。这是一个相对较新、注重用户体验和易用性的 Shell，拥有强大的自动建议、语法高亮等开箱即用的功能。它是一个独立于 Bash 的 Shell 程序。



## 入门shell脚本解析

先看一个入门 shell脚本 ，分析构成。

```shell
#!/bin/bash
# 这是一个注释：打印 Hello, World!
echo "Hello, World!"
```

`#!/bin/bash` 被称为 **shebang**。它告诉系统使用哪个解释器来执行这个脚本。

`#!`用于指定执行的 shell。

`/bin/bash` 表示使用 Bash Shell。

```shell
echo "Hello, World!"
```

表示输出 `Hello, World!`

shell脚本文件一般以 `.sh` 结尾。

## 执行shell脚本

执行shell脚本前需要赋予可执行权限：

```shell
chmod +x shell.sh
```



## 变量

**定义变量**：变量名=值（**等号两边不能有空格**）。

**使用变量**：在变量名前加上 `$` 符号，或者用 `${变量名}`

```bash
#!/bin/bash

name="Alice"
age=30

echo "Hello, $name"
echo "You are ${age} years old."
```

## 命令替换

可以使用 \$括号 或者 \$反引号触发命令替换。

```shell
$(date)
$`date`
```

shell会先执行命令替换里面的命令，再用命令的输出结果替换调原来的语句。

```shell
current_date=$(date)
# date指令会出输出当前时间 如 2025年 08月 31日 星期日 16:27:33 CST

echo "Today is: $current_date"
# 所以输出为：Today is: 2025年 08月 31日 星期日 16:27:33 CST
```



## 退出码

Unix程序退出时，会留一个退出码给启动该程序的父进程。它是一个数字，有时也叫错误码或退出值。当退出码是0时，通常表示程序运行正常。而如果是非正常结束，那么退出码通常会是非零数。





## shell脚本参数

### 从命令行接收参数

脚本内使用 `$1`, `$2`, `$3` ... 来获取第1、2、3...个参数。`$0` 代表脚本本身的名称。

```bash
#!/bin/bash
# 文件名为: greet.sh
echo "Hello, $1! You are from $2."
```

运行脚本：

```bash
./greet.sh Alice USA
```

输出：

```
Hello, Alice! You are from USA.
```

### shift

shell的内置命令shift能删除第一个参数$1，并用后面的补上。说具体一点，就是$2变成$1，
$3变成$2，如此类推。

### $#

$#变量持有传给脚本的变量的数量

### $@

$@变量代表脚本接收的所有参数

### 进程号 $$

$$变量持有shell的进程号。

### 退出码 $?

$?变量持有shell执行的最后一条命令的退出码。





## 引号

在 Shell 脚本中，单引号 (`'`) 和双引号 (`"`) 都用于定义字符串，但它们的行为有重要区别。

### 区别对比表

| 特性         | 单引号 `'`                 | 双引号 `"`                   |
| ------------ | -------------------------- | ---------------------------- |
| **变量扩展** | ❌ 不进行变量扩展           | ✅ 进行变量扩展               |
| **命令替换** | ❌ 不进行命令替换           | ✅ 进行命令替换               |
| **转义字符** | ❌ 不解释转义字符           | ✅ 解释转义字符               |
| **嵌套引号** | ✅ 可以在内部使用双引号     | ✅ 可以在内部使用单引号       |
| **性能**     | ⚡ 稍快（无需解析）         | ⚡ 稍慢（需要解析内容）       |
| **使用场景** | 固定文本、不需要解析的内容 | 需要变量插值或特殊字符的文本 |

### 变量扩展

```bash
name="World"

# 单引号：不进行变量扩展
echo 'Hello $name'    # 输出: Hello $name

# 双引号：进行变量扩展
echo "Hello $name"    # 输出: Hello World
```

### 命令替换

```bash
# 单引号：不进行命令替换
echo 'Today is $(date)'    # 输出: Today is $(date)

# 双引号：进行命令替换
echo "Today is $(date)"    # 输出: Today is Mon Aug 31 14:20:35 CST 2025
```

### 转义字符处理

```bash
# 单引号：不解释转义字符
echo 'Line 1\nLine 2'    # 输出: Line 1\nLine 2

# 双引号：解释转义字符
echo "Line 1\nLine 2"    # 输出: Line 1
                         #        Line 2
```

### 嵌套引号

```bash
# 单引号内可以包含双引号
echo 'He said: "Hello!"'    # 输出: He said: "Hello!"

# 双引号内可以包含单引号
echo "It's a beautiful day" # 输出: It's a beautiful day
```

### 特殊字符处理

```bash
# 单引号内所有字符都按字面意义处理
echo '$100 * 2 = $200'      # 输出: $100 * 2 = $200

# 双引号内需要转义特殊字符
echo "\$100 * 2 = \$200"    # 输出: $100 * 2 = $200
```



### 特殊情况：无法在单引号内包含单引号

这是一个重要的限制：**无法在单引号字符串内直接包含单引号**。

```bash
# 这会报错！
echo 'It's a problem'

# 解决方案1：使用双引号
echo "It's a solution"

# 解决方案2：拼接字符串
echo 'It'"'"'s a workaround'  # 输出: It's a workaround
# 分解: 'It' + "'" + 's a workaround'
```



### 使用建议

1. **使用单引号当**：
   - 字符串内容完全是静态文本
   - 不需要变量扩展或命令替换
   - 包含大量特殊字符（如 `$`, `\`, `` ` `` 等）

2. **使用双引号当**：
   - 需要变量插值
   - 需要命令替换
   - 字符串中包含空格（防止单词拆分）
   - 保护字符串中的大多数特殊字符，但允许变量和命令扩展

3. **最佳实践**：
   - 对于变量引用，即使在使用双引号时，也使用完整形式 `${variable}` 而不是 `$variable`，以提高可读性和避免歧义
   - 当传递可能包含空格的参数时，总是使用引号

```bash
# 好习惯：使用双引号保护变量，特别是当值可能包含空格时
filename="my file.txt"
rm "$filename"  # 正确：删除一个名为 "my file.txt" 的文件
rm $filename    # 危险：尝试删除 "my" 和 "file.txt" 两个文件

# 好习惯：使用大括号明确变量边界
echo "${name}script"  # 输出: Worldscript
echo "$namescript"    # 输出: (空，因为变量 `namescript` 未定义)
```

### 总结

- **单引号**：看到什么就是什么，完全字面值
- **双引号**：会进行变量和命令扩展，但其他字符大多保持原样











