# FreeRTOS编程命名习惯

## 数据类型重命名

FreeRTOS里对数据类型进行了重命名：

- ` char ` 改为 ` portCHAR ` 。

- ` short ` 改为 `portSHORT ` 。

- ` long `  改为 ` portLONG ` 。

  > port是端口的意思。

  > FreeRTOS中不使用int类型。

## 变量类型

在 FreeRTOS 中，定义变量的时候往往会把变量的类型当作前缀加在变量上，这样的好处是让用户一看到这个变量就知道该变量的类型。

- uint8_t/unsigned char 型变量的前缀是 uc

- int8_t/char 型变量的前缀是 c

- uint16_t/unsigned short 型变量的前缀是 us

- int16_t/short 型变量的前缀是 s

- uint32_t/unsigned long 型变量的前缀是 ul

- int32_t/long 型变量的前缀是 l

- FreeRTOS自定义变量的前缀是 x

  > 还有其他的数据类型，比如数据结构，任务句柄，队列句柄等定义的变量名的前缀也是 x。 

- 枚举变量以 e 为前缀 

- 如果是一个指针变量则会有一个前缀 p

## 函数名

函数名包含了**函数返回值的类型**、**函数所在的文件名**和**函数的功能**，如果是**私有的函数则会加一个 prv**（private）的前缀。

特别的，在函数名中加入了函数所在的文件名，这大大的帮助了用户提高寻找函数定义的效率和了解函数作用的目的，具体的举例如下：

```c
vTaskPrioritySet ( )         //函数的返回值为 void 型，在task.c 这个文件中定义。
xQueueReceive ( )            //函数的返回值为 portBASE_TYPE 型，在 queue.c 这个文件中定义。
vSemaphoreCreateBinary ( )   //函数的返回值为 void 型，在 semphr.h 这个文件中定义。
```

也就是用一个字母作为前缀代表返回值类型，然后跟上首字母大写的文件名，再跟上首字母大写的函数名，组成完整的函数名。

## 宏

宏名只使用大写字母，并跟上小写字母的文件名作为前缀。

几个常量宏

```
pdTRUE    1
pdFALSE   0
pdPASS    1
pdFAIL    0
```



