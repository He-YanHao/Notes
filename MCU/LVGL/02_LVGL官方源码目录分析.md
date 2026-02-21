#  LVGL官方源码目录分析

## 8.x

```
|-- LVGL
|   |-- /demos：综合演示，展示LVGL强大功能的完整演示程序。
|   |-- /docs：LVGL文献，API 参考、使用指南、控件说明等。
|   |-- /env_support：环境适配，针对不同开发环境的配置文件。
|   |-- /examples：实用示例，包含各个功能的示例代码和驱动实现。
|   |   |-- /anim：
|   |-- /scripts：Python 绑定和脚本工具。
|   |-- /src：LVGL 核心源码 - 所有控件、图形渲染、基础框架的源代码。
|   |   |-- /core：
|   |-- /tests：官方人员的测试代码，该文件夹用户无需了解。
|   |-- lvgl.h：LVGL的剪裁文件 
|   |-- lv_conf_template.h：配置模板，复制并重命名为 lv_conf.h 来自定义配置。
```



## 9.x

```
|-- LVGL
|   |-- /configs：配置模板，各种平台的默认配置文件。
|   |-- /demos：综合演示，展示LVGL强大功能的完整演示程序。
|   |-- /docs：LVGL文献，API 参考、使用指南、控件说明等。
|   |-- /env_support：环境适配，针对不同开发环境的配置文件。
|   |-- /examples：实用示例，包含各个功能的示例代码和驱动实现。
|   |   |-- /porting：
|   |-- /libs：第三方图片解码器、字体文件等依赖库
|   |-- /scripts：Python 绑定和脚本工具。
|   |-- /src：LVGL 核心源码 - 所有控件、图形渲染、基础框架的源代码。
|   |   |-- /core：
|   |   |-- /display：
|   |   |-- /draw：
|   |   |   |-- /sw：
|   |   |   |   |-- /blend：
|   |   |-- /drivers：
|   |   |-- /layouts：
|   |   |   |-- /flex：
|   |   |   |-- /grid：
|   |   |-- /libs：
|   |   |   |-- /bin_decoder
|   |   |-- /misc：
|   |   |   |-- /cache：
|   |   |   |   |-- /class：
|   |   |   |   |-- /instance
|   |   |-- /stdlib：
|   |   |   |-- /builtin：
|   |   |-- /themes：
|   |   |   |-- /default
|   |   |   |-- /mono
|   |   |   |-- /simple
|   |   |-- /widgets：
|   |   |   |-- /span
|   |-- /tests：官方人员的测试代码，该文件夹用户无需了解。
|   |-- /xmls：界面布局或资源定义文件。
|   |-- /zephyr：针对 Zephyr 系统的集成。
|   |-- lv_conf_template.h：配置模板，复制并重命名为 lv_conf.h 来自定义配置。
```



