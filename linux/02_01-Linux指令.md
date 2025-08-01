# Linux指令

`shutdown` 关机指令，`reboot` 重启。

## 终端指令

| 指令 / 快捷键    | 作用       | 说明 |
| ---------------- | ---------- | ---- |
| Ctrl + A         | 移动到行首 |      |
| Ctrl + E         | 移动到行尾 |      |
| clear / Ctrl + L | 清屏       |      |



## 工具指令（没啥用的）

| 指令 | 作用               | 补充 |
| ---- | ------------------ | ---- |
| date | 输出现在时间与日期 |      |
| cal  | 输出本月日历       |      |
|      |                    |      |
|      |                    |      |



## 通配符

| 符号        | 作用                   | 举例                                                     |
| ----------- | ---------------------- | -------------------------------------------------------- |
| *           | 匹配任意长度字符串     | *.cpp意为所有后缀为cpp的文件                             |
| ?           | 匹配单个字符           | ?.cpp意为所有后缀为cpp且文件名仅为一位的文件             |
| [ 字符集 ]  | 匹配指定范围内单个字符 | [1-5].cpp意为所有后缀为cpp且文件名在1-5范围内的的文件    |
| [ !字符集 ] | 匹配指定范围内单个字符 | [!1-5].cpp意为所有后缀为cpp且文件名不在1-5范围内的的文件 |

通配符可以帮助快速的定位和选择文件。

## 有关命令的命令

| 命令  | 格式                                   | 作用                           |
| ----- | -------------------------------------- | ------------------------------ |
| type  | type 命令                              | 输出 命令 的来源或类型         |
| which | which 命令                             | 输出 可执行文件命令 的具体位置 |
| help  | **help 命令** 或 **命令 --help**       | 输出 命令 的使用方式教程       |
| man   | man 命令                               | 输出 命令 的使用手册           |
| alias | alias 命令别名='命令1;命令2;命令3;...' | 给命令一个别名                 |

## 重定向

### 重定向

**重定向标准输出**

可以使用 ` > ` 将输出结果从终端重定向到某个文件，如：

` ls -l > ls.txt ` 

` ls -l ` 输出的结果就会被输出到 ` ls.txt ` 里。 

但是如果再次运行 ` ls -l > ls.txt ` 会发现 ` ls.txt ` 没有变化，那是因为重定向符会完全重写文件，如果想要附加到文件末尾需要用：

` ls -l >> ls.txt ` 

### 文件流

文件流包括 **标准输入** **标准输出** 以及 **标准错误**，在 Shell 里分别用文件描述符0，文件描述符1，文件描述符2来形容。

### 重定向标准错误

可以用

` ls -l /bin/usr 2>> bin.txt ` 来重定向标准错误输出。

**丢弃输出结果**

Linux中包含一个位桶目录 ` /dev/null ` ，接收输入结果但不进行任何处理，也不保存，只要把结果重定向到这，就相当于被丢弃。

### 管道



