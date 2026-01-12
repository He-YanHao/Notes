# LVGL移植流程

在存放移植层的 `Middlewares` 文件夹下新建对应的文件夹。

```
|-- Middlewares
|   |-- /LVGL
|   |   |-- /GUI
|   |   |-- /GUI_APP
```



找到主目录下的 `lv_conf_template.h` 文件，复制并重命名为 `lv_conf.h` 来自定义配置。

将刚刚复制并重命名的 `lv_conf.h` 文件打开，将开头的：

```c
/* clang-format off */
#if 0 /* Set this to "1" to enable content */
```

修改为

```c
/* clang-format off */
#if 1 /* Set this to "1" to enable content */
```

开启条件编译。



```

SW文件夹要加
```

