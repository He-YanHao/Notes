# 字符串和内存操作 string

## 头文件

需要包含

```c
#include <string.h>
```

## 字符串处理函数

### 字符串长度
```c
size_t strlen(const char *str);
```
**功能**：计算字符串长度（不包括结尾的 `\0`）
```c
char str[] = "Hello";
printf("%zu\n", strlen(str)); // 输出: 5
```



### 字符串复制

```c
char *strcpy(char *dest, const char *src);
char *strncpy(char *dest, const char *src, size_t n);
```
**功能**：将源字符串复制到目标字符串
```c
char dest[20];
char src[] = "Hello World";
strcpy(dest, src);
printf("%s\n", dest); // 输出: Hello World

// 安全版本，指定最大复制长度
strncpy(dest, src, sizeof(dest)-1);
dest[sizeof(dest)-1] = '\0'; // 确保以\0结尾
```



### 字符串连接

```c
char *strcat(char *dest, const char *src);
char *strncat(char *dest, const char *src, size_t n);
```
**功能**：将源字符串连接到目标字符串末尾
```c
char str[20] = "Hello";
strcat(str, " World");
printf("%s\n", str); // 输出: Hello World
```



### 字符串比较

```c
int strcmp(const char *str1, const char *str2);
int strncmp(const char *str1, const char *str2, size_t n);
```
**功能**：比较两个字符串
- 返回值 < 0：str1 < str2
- 返回值 = 0：str1 = str2  
- 返回值 > 0：str1 > str2

```c
char str1[] = "apple";
char str2[] = "banana";
int result = strcmp(str1, str2);
printf("%d\n", result); // 输出: 负数，因为"apple" < "banana"
```



### 字符串查找

```c
char *strchr(const char *str, int c);    // 查找字符第一次出现
char *strrchr(const char *str, int c);   // 查找字符最后一次出现
char *strstr(const char *haystack, const char *needle); // 查找子串
```
**功能**：在字符串中查找字符或子串
```c
char str[] = "Hello World";
char *p = strchr(str, 'o');
printf("%s\n", p); // 输出: o World

p = strstr(str, "World");
printf("%s\n", p); // 输出: World
```



### 查找字符在字符串中第一次出现的位置

```c
size_t strcspn(const char *str1, const char *str2);
```
**功能**：返回str1开头连续不在str2中出现的字符数
```c
char str[] = "Hello123";
size_t len = strcspn(str, "0123456789");
printf("%zu\n", len); // 输出: 5 (数字前有5个字母)
```

### 字符串分割

```c
char *strtok(char *str, const char *delimiters);
```

**功能**：将字符串按分隔符分割

```c
char str[] = "apple,banana,cherry";
char *token = strtok(str, ",");
while(token != NULL) {
    printf("%s\n", token);
    token = strtok(NULL, ",");
}
// 输出:
// apple
// banana
// cherry
```





## 内存操作函数

### 内存设置
```c
void *memset(void *ptr, int value, size_t num);
```
**功能**：将内存块设置为特定值
```c
char buffer[10];
memset(buffer, 0, sizeof(buffer)); // 全部初始化为0
memset(buffer, 'A', 5); // 前5个字节设为'A'
```



### 内存复制

```c
void *memcpy(void *dest, const void *src, size_t num);
```
**功能**：复制内存块（比strcpy更通用，可以复制任意数据）
```c
int src[5] = {1, 2, 3, 4, 5};
int dest[5];
memcpy(dest, src, sizeof(src));
```



### 内存比较

```c
int memcmp(const void *ptr1, const void *ptr2, size_t num);
```
**功能**：比较两个内存块
```c
int arr1[] = {1, 2, 3};
int arr2[] = {1, 2, 3};
int result = memcmp(arr1, arr2, sizeof(arr1));
printf("%d\n", result); // 输出: 0 (相等)
```



## 其他实用函数



## string系函数的局限性

1. **缓冲区溢出**：`strcpy`、`strcat` 等函数不安全，推荐使用 `strncpy`、`strncat`
2. **字符串结尾**：确保字符串以 `\0` 结尾
3. **内存重叠**：`memcpy` 不能处理内存重叠，需要时使用 `memmove`









## 简介

**string .h** 头文件定义了一个变量类型、一个宏和各种操作字符数组的函数。

`<string.h>` 是 C 标准库中的一个头文件，提供了一组用于处理字符串和内存块的函数。这些函数涵盖了字符串复制、连接、比较、搜索和内存操作等。

## 库变量

下面是头文件 string.h 中定义的变量类型：

| 序号 | 变量 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | **size_t** 这是无符号整数类型，它是 **sizeof** 关键字的结果。 |

## 库宏

下面是头文件 string.h 中定义的宏：

| 序号 | 宏 & 描述                             |
| :--- | :------------------------------------ |
| 1    | **NULL** 这个宏是一个空指针常量的值。 |

## 库函数

下面是头文件 string.h 中定义的函数：

| 序号 | 函数 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | [void *memchr(const void *str, int c, size_t n)](https://www.runoob.com/cprogramming/c-function-memchr.html) 在参数 *str* 所指向的字符串的前 n 个字节中搜索第一次出现字符 c（一个无符号字符）的位置。 |
| 2    | [int memcmp(const void *str1, const void *str2, size_t n)](https://www.runoob.com/cprogramming/c-function-memcmp.html) 把 *str1* 和 *str2* 的前 n 个字节进行比较。 |
| 3    | [void *memcpy(void *dest, const void *src, size_t n)](https://www.runoob.com/cprogramming/c-function-memcpy.html) 从 src 复制 n 个字符到 *dest*。 |
| 4    | [void *memmove(void *dest, const void *src, size_t n)](https://www.runoob.com/cprogramming/c-function-memmove.html) 另一个用于从 *src* 复制 n 个字符到 *dest* 的函数。 |
| 5    | [void *memset(void *str, int c, size_t n)](https://www.runoob.com/cprogramming/c-function-memset.html) 将指定的值 c 复制到 str 所指向的内存区域的前 n 个字节中。 |
| 6    | [char *strcat(char *dest, const char *src)](https://www.runoob.com/cprogramming/c-function-strcat.html) 把 *src* 所指向的字符串追加到 *dest* 所指向的字符串的结尾。 |
| 7    | [char *strncat(char *dest, const char *src, size_t n)](https://www.runoob.com/cprogramming/c-function-strncat.html) 把 *src* 所指向的字符串追加到 *dest* 所指向的字符串的结尾，直到 n 字符长度为止。 |
| 8    | [char *strchr(const char *str, int c)](https://www.runoob.com/cprogramming/c-function-strchr.html) 在参数 *str* 所指向的字符串中搜索第一次出现字符 c（一个无符号字符）的位置。 |
| 9    | [int strcmp(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strcmp.html) 把 *str1* 所指向的字符串和 *str2* 所指向的字符串进行比较。 |
| 10   | [int strncmp(const char *str1, const char *str2, size_t n)](https://www.runoob.com/cprogramming/c-function-strncmp.html) 把 *str1* 和 *str2* 进行比较，最多比较前 n 个字节。 |
| 11   | [int strcoll(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strcoll.html) 把 *str1* 和 *str2* 进行比较，结果取决于 LC_COLLATE 的位置设置。 |
| 12   | [char *strcpy(char *dest, const char *src)](https://www.runoob.com/cprogramming/c-function-strcpy.html) 把 *src* 所指向的字符串复制到 *dest*。 |
| 13   | [char *strncpy(char *dest, const char *src, size_t n)](https://www.runoob.com/cprogramming/c-function-strncpy.html) 把 *src* 所指向的字符串复制到 *dest*，最多复制 n 个字符。 |
| 14   | [size_t strcspn(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strcspn.html) 检索字符串 str1 开头连续有几个字符都不含字符串 str2 中的字符。 |
| 15   | [char *strerror(int errnum)](https://www.runoob.com/cprogramming/c-function-strerror.html) 从内部数组中搜索错误号 errnum，并返回一个指向错误消息字符串的指针。 |
| 16   | [size_t strlen(const char *str)](https://www.runoob.com/cprogramming/c-function-strlen.html) 计算字符串 str 的长度，直到空结束字符，但不包括空结束字符。 |
| 17   | [char *strpbrk(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strpbrk.html) 检索字符串 *str1* 中第一个匹配字符串 *str2* 中字符的字符，不包含空结束字符。也就是说，依次检验字符串 str1 中的字符，当被检验字符在字符串 str2 中也包含时，则停止检验，并返回该字符位置。 |
| 18   | [char *strrchr(const char *str, int c)](https://www.runoob.com/cprogramming/c-function-strrchr.html) 在参数 *str* 所指向的字符串中搜索最后一次出现字符 c（一个无符号字符）的位置。 |
| 19   | [size_t strspn(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strspn.html) 检索字符串 *str1* 中第一个不在字符串 *str2* 中出现的字符下标。 |
| 20   | [char *strstr(const char *haystack, const char *needle)](https://www.runoob.com/cprogramming/c-function-strstr.html) 在字符串 *haystack* 中查找第一次出现字符串 *needle*（不包含空结束字符）的位置。 |
| 21   | [char *strtok(char *str, const char *delim)](https://www.runoob.com/cprogramming/c-function-strtok.html) 分解字符串 *str* 为一组字符串，*delim* 为分隔符。 |
| 22   | [size_t strxfrm(char *dest, const char *src, size_t n)](https://www.runoob.com/cprogramming/c-function-strxfrm.html) 根据程序当前的区域选项中的 LC_COLLATE 来转换字符串 **src** 的前 **n** 个字符，并把它们放置在字符串 **dest** 中。 |