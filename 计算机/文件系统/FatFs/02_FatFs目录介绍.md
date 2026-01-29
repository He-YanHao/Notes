# FatFs目录介绍





```
|-- FatFs-R0.16
|   |-- /documents
|   |   |-- /doc：官方技术文档（API 说明、移植说明、配置说明）
|   |   |   |-- /
|   |   |-- /res：文档中用到的图片、示例资源
|   |   |   |-- /
|   |-- /source
|   |   |-- 00history.txt：介绍了FatFs的版本更新情况。
|   |   |-- 00readme.txt：说明了当前目录下文件的功能。
|   |   |-- diskio.c：包含底层存储介质的操作函数，这些函数需要用户自己实现，主要添加底层驱动函数。
|   |   |-- diskio.h：磁盘I/O接口声明
|   |   |-- ff.c：FatFs核心文件，文件管理的实现方法。该文件独立于底层介质操作文件的函数，利用这些函数实现文件的读写。
|   |   |-- ff.h：FatFs API 头文件
|   |   |-- ffconf.h：这个头文件包含了对FatFs功能配置的宏定义，通过修改这些宏定义就可以裁剪FatFs的功能。
|   |   |-- ffsystem.c：与系统相关（同步、内存管理）
|   |   |-- ffunicode.c：Unicode / 长文件名支持
|   |-- LICENSE.txt
```

