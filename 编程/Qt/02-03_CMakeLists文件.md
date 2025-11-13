# CMakeLists文件

```cmake
cmake_minimum_required(VERSION 3.16)
#指定构建此项目所需的最低CMake版本为3.16 若低于会报错

project(Host_Computer VERSION 0.1 LANGUAGES CXX)
#定义项目名称为"Host_Computer"
#定义项目版本为0.1
#定义项目语言为C++

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
#AUTOUIC: 自动处理Qt的UI文件(.ui)
#AUTOMOC: 自动处理Qt的元对象编译器(MOC)
#AUTORCC: 自动处理Qt的资源文件(.qrc)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
#设置C++语言标准为C++17
#要求必须支持C++17标准，如果不支持则报错

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets SerialPort)
#查找Qt库，优先查找Qt6，如果找不到则查找Qt5
#REQUIRED表示必须找到，否则配置失败
#指定需要Widgets组件
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets SerialPort)
#根据找到的Qt主版本号(QT_VERSION_MAJOR)再次查找特定版本的Qt
#确保找到对应版本的Widgets组件

set(PROJECT_SOURCES
        main.cpp
        mainwindow.cpp
        mainwindow.h
        mainwindow.ui
)
#定义项目源文件列表，包含:
# main.cpp: 主程序入口
# mainwindow.cpp: 主窗口实现
# mainwindow.h: 主窗口头文件
# mainwindow.ui: Qt设计师创建的UI文件

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(Host_Computer
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
#如果Qt版本大于等于6 使用Qt6特有的qt_add_executable命令创建可执行文件


# Define target properties for Android with Qt 6 as:
#    set_property(TARGET Host_Computer APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
# 说明如何为Android平台设置Qt6目标属性


else()
    if(ANDROID)
        add_library(Host_Computer SHARED
            ${PROJECT_SOURCES}
        )
#否则（Qt版本小于6）
#如果是Android平台
#创建共享库而不是可执行文件（Android应用的特殊要求）

# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
# 说明Qt5中如何设置Android包源目录

    else()
        add_executable(Host_Computer
            ${PROJECT_SOURCES}
        )
    endif()
endif()
#如果不是Android平台 使用标准的add_executable命令创建可执行文件

target_link_libraries(Host_Computer PRIVATE
    Qt${QT_VERSION_MAJOR}::Widgets
    Qt${QT_VERSION_MAJOR}::SerialPort
)
#将Qt Widgets库链接到目标可执行文件 PRIVATE表示这个依赖关系仅用于构建可执行文件本身

# Qt for iOS sets MACOSX_BUNDLE_GUI_IDENTIFIER automatically since Qt 6.1.
# If you are developing for iOS or macOS you should consider setting an
# explicit, fixed bundle identifier manually though.
#说明从Qt 6.1开始，iOS会自动设置bundle标识符

if(${QT_VERSION} VERSION_LESS 6.1.0)
  set(BUNDLE_ID_OPTION MACOSX_BUNDLE_GUI_IDENTIFIER com.example.Host_Computer)
endif()
#如果Qt版本小于6.1.0 设置bundle标识符选项为"com.example.Host_Computer"

set_target_properties(Host_Computer PROPERTIES
    ${BUNDLE_ID_OPTION}
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)
#设置目标属性：
# bundle标识符（如果Qt版本<6.1.0）
# MacOSX bundle版本号
# MacOSX短版本字符串
# 在Mac上创建bundle
# 在Windows上创建GUI应用程序（不显示控制台窗口）

include(GNUInstallDirs)
#包含GNU安装目录定义模块，提供标准安装目录变量

install(TARGETS Host_Computer
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
#定义安装规则：
# BUNDLE安装到当前目录
# 库文件安装到标准库目录
# 可执行文件安装到标准二进制目录

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(Host_Computer)
endif()
#如果是Qt6，执行最终化步骤（与之前的MANUAL_FINALIZATION对应）
```

