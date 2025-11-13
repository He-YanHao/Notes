# 字符串和内存操作string

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

