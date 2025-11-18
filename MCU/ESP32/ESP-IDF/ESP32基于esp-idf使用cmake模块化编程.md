# ESP32基于IDF模块化编程



## 案例结构

```.
├── CMakeLists.txt ---------------- 组件目录CMakeLists
├── common
│   ├── CMakeLists.txt ------------ mian目录CMakeLists
│   ├── inc
│   │   └── my_wifi.h
│   └── src
│       └── my_wifi.c
├── dependencies.lock
├── main
│   ├── CMakeLists.txt ------------ 根目录CMakeLists
│   ├── idf_component.yml
│   ├── Kconfig.projbuild
│   └── wifi_OTA.c
├── README.md
├── sdkconfig
└── sdkconfig.old
```



## 三个CMakeLists.txt的作用

### 根目录CMakeLists.txt - 项目配置
```cmake
cmake_minimum_required(VERSION 3.22)               # 指定构建该项目所需的 最低 CMake 版本
include($ENV{IDF_PATH}/tools/cmake/project.cmake)  # 使用 ESP-IDF 框架的安装路径/tools/cmake/下的project.cmake作为编译组件
set(EXTRA_COMPONENT_DIRS common)                   # 告诉ESP-IDF在哪里找自定义组件 若添加了自定义组件 需要自己添加在这里添加
project(wifi_OTA)                                  # 项目名称是 wifi_OTA
```
**作用**：
- 设置项目级别的配置
- 包含ESP-IDF构建系统
- 指定额外的组件目录
- 定义项目名称



### 组件目录CMakeLists.txt - **组件定义**

```cmake
idf_component_register(SRCS "src/my_wifi.c"        # 从组件目录开始的相对路径指定组件的源文件列表 可以指定多个文件 用空格分隔
                    INCLUDE_DIRS "inc"             # 从组件目录开始的相对路径指定头文件目录 其他组件可以通过 #include "my_wifi.h" 包含该目录下的头文件 可以指定多个目录
                    REQUIRES esp_wifi nvs_flash)   # 声明组件的依赖于 ESP-IDF的 esp_wifi/Wi-Fi驱动组件 nvs_flash/非易失性存储组件
```
**作用**：
- 定义`common`组件
- 指定组件的源文件和头文件目录
- 声明组件依赖（esp_wifi, nvs_flash）
- 管理组件的编译选项



### mian目录CMakeLists.txt - **主应用程序**

```cmake
idf_component_register(SRCS "wifi_OTA.c"               # 指定组件的唯一源文件 wifi_OTA.c 位于组件根目录下
                    PRIV_REQUIRES esp_wifi nvs_flash   # 声明私有依赖 这些依赖不会传递给其他依赖此组件的组件
                    REQUIRES common                    # 声明公共依赖 这些依赖会传递给其他依赖此组件的组件
                    INCLUDE_DIRS ".")                  # 指定头文件目录为当前目录 当前目录下的头文件对其他组件可见
```
**作用**：
- 定义主应用程序组件
- 指定主源文件
- 声明私有依赖和公共依赖
- 包含主应用程序的编译设置



