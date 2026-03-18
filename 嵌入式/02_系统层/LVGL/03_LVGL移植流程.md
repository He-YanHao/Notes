# LVGL移植流程

在存放移植层的 `Middlewares` 文件夹下新建对应的文件夹。

```
|-- lvgl
|   |-- /examples
|   |   |-- /porting
|   |-- /src：
|   |   |-- /core
|   |   |-- /draw
|   |   |-- /extra
|   |   |-- /font
|   |   |-- /hal
|   |   |-- /misc
|   |   |-- /widgets
```



然后在官方库的 `lvgl/examples/porting` 里面将：

```
lv_port_disp_template.c/h
lv_port_fs_template.c/h
lv_port_indev_template.c/h
```

改为：

```
lv_port_disp.c/h
lv_port_fs.c/h
lv_port_indev.c/h
```

并添加进去。



同样添加 `lvgl/examples/src` 目录下的，不做任何修改。

```
lvgl.h
lv_api_map.h
lv_conf_internal.h
lv_conf_kconfig.h
```



找到主目录下的 `lv_conf_template.h` 和`lvgl.h`文件，并将前者并重命名为 `lv_conf.h` 来自定义配置，然后一起添加进入项目里。

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
|-- lvgl
|   |-- /examples
|   |   |-- /porting
|   |   |   |-- /lv_port_disp.c/h
|   |   |   |-- /lv_port_fs.c/h
|   |   |   |-- /lv_port_indev.c/h
|   |-- /src：
|   |   |-- /core
|   |   |-- /draw
|   |   |-- /extra
|   |   |-- /font
|   |   |-- /hal
|   |   |-- /misc
|   |   |-- /widgets
|   |   |-- lvgl.h：Arduino的兼容层 指向外层lvgl.h
|   |   |-- lv_api_map.h
|   |   |-- lv_conf_internal.h
|   |   |-- lv_conf_kconfig.h
|   |-- lvgl.h：实际起到作用的lvgl.h
|   |-- lv_conf.h
```


