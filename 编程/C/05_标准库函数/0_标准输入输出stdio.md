# C的输入输出

## 字符类型

使用 `char` 申请一个单字符类型，通过

```c
char 变量名[数字] = {};
```

来决定char的长度。

`char`定义的字符要用单引号`‘’`将其包围，字符串需要用双引号`“”`包围。

其区别如下：

```c
char bianliang[] = 'A';
//则bianliang仅有一位A。
char bianliang[] = "A";
//则bianliang有两位，包含字符A和字符串的结束0x00。
```



## printf

C语言中`printf(“输出内容和输出内容的格式控制符”,参数);`表示输出。



### 格式控制符

在`" "`里面的格式控制符，可以控制输入输出的数据类型，如：

- 字符及字符串：

	- `%c` ：输出一个单一的字符。char的简写。
	- `%s` ：输出一个字符串。string的简写。

- 整数数据：

  - `%o` ：以八进制的形式输出。0 的另类写法，因为数字被占用了。
- `%d` ：以十进制的形式输出。
  
  - `%i` ：也是以十进制的形式输出。在表示输出时二者无区别。
- `%x` ：以十六进制的形式输出。0x的简写。
  - `%lld` ：对于 `long long` 类型的数据这样表示。

  > 标准库中不包含`%b`输出二进制。

- `%(X)格式控制符` ：可以输出（X）位的整数，位数不足的用空格靠右补齐。

    ```c
    
    ```

- 浮点数类型：

	- `%f` ：双精度浮点数类型，一般整数全部输出，小数保留六位输出。
	- `%lf` ：表示双精度浮点数，仅在表示输入时与 `%f` 有区别，在输出时作用相同。
	- `%(X).(Y)f` ：输出（X）位的数据（小数点占一位），小数点后保留（Y）位。
	- `%e` ：科学计数法形式。
	- `%g` ：自动选择小数形式或科学计数法形式，并舍弃小数末尾的 0 。

针对多个参数的语句，比如：

```c
a = 1;
b = 2;
c = 3;
//要输出 a = c - b;
//那就是：
printf(“%d = %d - %d;”,a,c,b);
//将输出表达式中的参数用其对应的格式控制符代替，然后在后面按顺序跟上参数。
```

## scanf

使用`scanf(“格式控制字符串”,&地址列表);`表示输入。

可以连续使用`scanf(“格式控制字符串1格式控制字符串2”, &地址列表1,&地址列表2);`

还使用`get(字符串变量名);`表示输入。

**`%d` `%i` 区别**

在表示输出时二者无区别。

在表示输入时，`%d`只可以和十进制整数类型匹配，`%i`可以匹配任意进制的整数数据类型。

### scanf_s

在VS里面，微软定义了`scanf_s`，参数包含了三个`scanf_s("格式控制符", &arr1, sizeof(arr1))`

## 字符串

C语言中使用`strcpy(字符变量名,"字符")`来对字符变量进行赋值。

使用`strcat(字符变量名,另一个字符变量名)`来对字符变量附加到原变量后方。

C 库函数`strlen(str)` 计算字符串 `str `的长度，直到空结束字符，但不包括空结束字符，会返回一个数字。

## 字符有关函数

| 序号 | 函数 & 目的                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | **strcpy(s1, s2);** 复制字符串 s2 到字符串 s1。              |
| 2    | **strcat(s1, s2);** 连接字符串 s2 到字符串 s1 的末尾。       |
| 3    | **strlen(s1);** 返回字符串 s1 的长度。                       |
| 4    | **strcmp(s1, s2);** 如果 s1 和 s2 是相同的，则返回 0；如果 s1<s2 则返回小于 0；如果 s1>s2 则返回大于 0。 |
| 5    | **strchr(s1, ch);** 返回一个指针，指向字符串 s1 中字符 ch 的第一次出现的位置。 |
| 6    | **strstr(s1, s2);** 返回一个指针，指向字符串 s1 中字符串 s2 的第一次出现的位置。 |

## getchar() & putchar() 函数

**int getchar(void)** 

函数从屏幕读取下一个可用的字符，并把它返回为一个整数。这个函数在同一个时间内只会读取一个单一的字符。可以在循环内使用这个方法，以便从屏幕上读取多个字符。

**int putchar(int c)** 

函数把字符输出到屏幕上，并返回相同的字符。这个函数在同一个时间内只会输出一个单一的字符。可以在循环内使用这个方法，以便在屏幕上输出多个字符。

## gets() & puts() 函数

**char \*gets(char \*s)** 

函数从 **键盘stdin** 读取一行到 **s** 所指向的缓冲区，直到一个终止符或 EOF。

**int puts(const char \*s)** 

函数把字符串 s 和一个尾随的换行符写入到 **屏幕stdout**





## 简介

**stdio .h** 头文件定义了三个变量类型、一些宏和各种函数来执行输入和输出。

`<stdio.h>` 是 C 标准库中的一个头文件，定义了处理文件和标准输入/输出流的各种函数和类型。

## 库变量

下面是头文件 stdio.h 中定义的变量类型：

| 序号 | 变量 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | **size_t** 这是无符号整数类型，它是 **sizeof** 关键字的结果，表示对象大小。 |
| 2    | **FILE** 文件流类型，适合存储文件流信息的对象类型。          |
| 3    | **fpos_t** 文件位置类型，适合存储文件中任何位置的对象类型。  |

## 库宏

下面是头文件 stdio.h 中定义的宏：

| 序号 | 宏 & 描述                                                    |
| :--- | :----------------------------------------------------------- |
| 1    | **NULL** 这个宏是一个空指针常量的值。                        |
| 2    | **_IOFBF、_IOLBF** 和 **_IONBF** 这些宏扩展了带有特定值的整型常量表达式，并适用于 **setvbuf** 函数的第三个参数。 |
| 3    | **BUFSIZ** 这个宏是一个整数，该整数代表了 **setbuf** 函数使用的缓冲区大小。 |
| 4    | **EOF** 这个宏是一个表示已经到达文件结束的负整数。           |
| 5    | **FOPEN_MAX** 这个宏是一个整数，该整数代表了系统可以同时打开的文件数量。 |
| 6    | **FILENAME_MAX** 这个宏是一个整数，该整数代表了字符数组可以存储的文件名的最大长度。如果实现没有任何限制，则该值应为推荐的最大值。 |
| 7    | **L_tmpnam** 这个宏是一个整数，该整数代表了字符数组可以存储的由 tmpnam 函数创建的临时文件名的最大长度。 |
| 8    | **SEEK_CUR、SEEK_END** 和 **SEEK_SET** 这些宏是在 **fseek** 函数中使用，用于在一个文件中定位不同的位置。 |
| 9    | **TMP_MAX** 这个宏是 tmpnam 函数可生成的独特文件名的最大数量。 |
| 10   | **stderr、stdin** 和 **stdout** 这些宏是指向 FILE 类型的指针，分别对应于标准错误、标准输入和标准输出流。 |

## 库函数

下面是头文件 stdio.h 中定义的函数：

>   为了更好地理解函数，请按照下面的序列学习这些函数，因为第一个函数中创建的文件会在后续的函数中使用到。

| 序号 | 函数 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | [int fclose(FILE *stream)](https://www.runoob.com/cprogramming/c-function-fclose.html) 关闭流 stream。刷新所有的缓冲区。 |
| 2    | [void clearerr(FILE *stream)](https://www.runoob.com/cprogramming/c-function-clearerr.html) 清除给定流 stream 的文件结束和错误标识符。 |
| 3    | [int feof(FILE *stream)](https://www.runoob.com/cprogramming/c-function-feof.html) 测试给定流 stream 的文件结束标识符。 |
| 4    | [int ferror(FILE *stream)](https://www.runoob.com/cprogramming/c-function-ferror.html) 测试给定流 stream 的错误标识符。 |
| 5    | [int fflush(FILE *stream)](https://www.runoob.com/cprogramming/c-function-fflush.html) 刷新流 stream 的输出缓冲区。 |
| 6    | [int fgetpos(FILE *stream, fpos_t *pos)](https://www.runoob.com/cprogramming/c-function-fgetpos.html) 获取流 stream 的当前文件位置，并把它写入到 pos。 |
| 7    | [FILE *fopen(const char *filename, const char *mode)](https://www.runoob.com/cprogramming/c-function-fopen.html) 使用给定的模式 mode 打开 filename 所指向的文件。 |
| 8    | [size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fread.html) 从给定流 stream 读取数据到 ptr 所指向的数组中。 |
| 9    | [FILE *freopen(const char *filename, const char *mode, FILE *stream)](https://www.runoob.com/cprogramming/c-function-freopen.html) 把一个新的文件名 filename 与给定的打开的流 stream 关联，同时关闭流中的旧文件。 |
| 10   | [int fseek(FILE *stream, long int offset, int whence)](https://www.runoob.com/cprogramming/c-function-fseek.html) 设置流 stream 的文件位置为给定的偏移 offset，参数 *offset* 意味着从给定的 *whence* 位置查找的字节数。 |
| 11   | [int fsetpos(FILE *stream, const fpos_t *pos)](https://www.runoob.com/cprogramming/c-function-fsetpos.html) 设置给定流 stream 的文件位置为给定的位置。参数 *pos* 是由函数 fgetpos 给定的位置。 |
| 12   | [long int ftell(FILE *stream)](https://www.runoob.com/cprogramming/c-function-ftell.html) 返回给定流 stream 的当前文件位置。 |
| 13   | [size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fwrite.html) 把 ptr 所指向的数组中的数据写入到给定流 stream 中。 |
| 14   | [int remove(const char *filename)](https://www.runoob.com/cprogramming/c-function-remove.html) 删除给定的文件名 filename，以便它不再被访问。 |
| 15   | [int rename(const char *old_filename, const char *new_filename)](https://www.runoob.com/cprogramming/c-function-rename.html) 把 old_filename 所指向的文件名改为 new_filename。 |
| 16   | [void rewind(FILE *stream)](https://www.runoob.com/cprogramming/c-function-rewind.html) 设置文件位置为给定流 stream 的文件的开头。 |
| 17   | [void setbuf(FILE *stream, char *buffer)](https://www.runoob.com/cprogramming/c-function-setbuf.html) 定义流 stream 应如何缓冲。 |
| 18   | [int setvbuf(FILE *stream, char *buffer, int mode, size_t size)](https://www.runoob.com/cprogramming/c-function-setvbuf.html) 另一个定义流 stream 应如何缓冲的函数。 |
| 19   | [FILE *tmpfile(void)](https://www.runoob.com/cprogramming/c-function-tmpfile.html) 以二进制更新模式(wb+)创建临时文件。 |
| 20   | [char *tmpnam(char *str)](https://www.runoob.com/cprogramming/c-function-tmpnam.html) 生成并返回一个有效的临时文件名，该文件名之前是不存在的。 |
| 21   | [int fprintf(FILE *stream, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-fprintf.html) 发送格式化输出到流 stream 中。 |
| 22   | [int printf(const char *format, ...)](https://www.runoob.com/cprogramming/c-function-printf.html) 发送格式化输出到标准输出 stdout。 |
| 23   | [int sprintf(char *str, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-sprintf.html) 发送格式化输出到字符串。 |
| 24   | [int vfprintf(FILE *stream, const char *format, va_list arg)](https://www.runoob.com/cprogramming/c-function-vfprintf.html) 使用参数列表发送格式化输出到流 stream 中。 |
| 25   | [int vprintf(const char *format, va_list arg)](https://www.runoob.com/cprogramming/c-function-vprintf.html) 使用参数列表发送格式化输出到标准输出 stdout。 |
| 26   | [int vsprintf(char *str, const char *format, va_list arg)](https://www.runoob.com/cprogramming/c-function-vsprintf.html) 使用参数列表发送格式化输出到字符串。 |
| 27   | [int fscanf(FILE *stream, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-fscanf.html) 从流 stream 读取格式化输入。 |
| 28   | [int scanf(const char *format, ...)](https://www.runoob.com/cprogramming/c-function-scanf.html) 从标准输入 stdin 读取格式化输入。 |
| 29   | [int sscanf(const char *str, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-sscanf.html) 从字符串读取格式化输入。 |
| 30   | [int fgetc(FILE *stream)](https://www.runoob.com/cprogramming/c-function-fgetc.html) 从指定的流 stream 获取下一个字符（一个无符号字符），并把位置标识符往前移动。 |
| 31   | [char *fgets(char *str, int n, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fgets.html) 从指定的流 stream 读取一行，并把它存储在 str 所指向的字符串内。当读取 **(n-1)** 个字符时，或者读取到换行符时，或者到达文件末尾时，它会停止，具体视情况而定。 |
| 32   | [int fputc(int char, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fputc.html) 把参数 char 指定的字符（一个无符号字符）写入到指定的流 stream 中，并把位置标识符往前移动。 |
| 33   | [int fputs(const char *str, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fputs.html) 把字符串写入到指定的流 stream 中，但不包括空字符。 |
| 34   | [int getc(FILE *stream)](https://www.runoob.com/cprogramming/c-function-getc.html) 从指定的流 stream 获取下一个字符（一个无符号字符），并把位置标识符往前移动。 |
| 35   | [int getchar(void)](https://www.runoob.com/cprogramming/c-function-getchar.html) 从标准输入 stdin 获取一个字符（一个无符号字符）。 |
| 36   | [char *gets(char *str)](https://www.runoob.com/cprogramming/c-function-gets.html) 从标准输入 stdin 读取一行，并把它存储在 str 所指向的字符串中。当读取到换行符时，或者到达文件末尾时，它会停止，具体视情况而定。 |
| 37   | [int putc(int char, FILE *stream)](https://www.runoob.com/cprogramming/c-function-putc.html) 把参数 char 指定的字符（一个无符号字符）写入到指定的流 stream 中，并把位置标识符往前移动。 |
| 38   | [int putchar(int char)](https://www.runoob.com/cprogramming/c-function-putchar.html) 把参数 char 指定的字符（一个无符号字符）写入到标准输出 stdout 中。 |
| 39   | [int puts(const char *str)](https://www.runoob.com/cprogramming/c-function-puts.html) 把一个字符串写入到标准输出 stdout，直到空字符，但不包括空字符。换行符会被追加到输出中。 |
| 40   | [int ungetc(int char, FILE *stream)](https://www.runoob.com/cprogramming/c-function-ungetc.html) 把字符 char（一个无符号字符）推入到指定的流 stream 中，以便它是下一个被读取到的字符。 |
| 41   | [void perror(const char *str)](https://www.runoob.com/cprogramming/c-function-perror.html) 把一个描述性错误消息输出到标准错误 stderr。首先输出字符串 str，后跟一个冒号，然后是一个空格。 |
| 42   | [int snprintf(char *str, size_t size, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-snprintf.html) 格式字符串到 str 中。 |