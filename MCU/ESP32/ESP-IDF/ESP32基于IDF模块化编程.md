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
cmake_minimum_required(VERSION 3.22)
include($ENV{IDF_PATH}/tools/cmake/project.cmake)
set(EXTRA_COMPONENT_DIRS common)  # 告诉ESP-IDF在哪里找自定义组件 若添加了自定义组件 需要自己添加在这里添加
project(wifi_OTA)
```
**作用**：
- 设置项目级别的配置
- 包含ESP-IDF构建系统
- 指定额外的组件目录
- 定义项目名称



### 组件目录CMakeLists.txt - **组件定义**

```cmake
idf_component_register(SRCS "src/my_wifi.c"
                    INCLUDE_DIRS "inc"
                    REQUIRES esp_wifi nvs_flash)
```
**作用**：
- 定义`common`组件
- 指定组件的源文件和头文件目录
- 声明组件依赖（esp_wifi, nvs_flash）
- 管理组件的编译选项



### mian目录CMakeLists.txt - **主应用程序**

```cmake
idf_component_register(SRCS "wifi_OTA.c"
                    PRIV_REQUIRES esp_wifi nvs_flash
                    REQUIRES common
                    INCLUDE_DIRS ".")
```
**作用**：
- 定义主应用程序组件
- 指定主源文件
- 声明私有依赖和公共依赖
- 包含主应用程序的编译设置



