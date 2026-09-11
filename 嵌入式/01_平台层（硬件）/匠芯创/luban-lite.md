# luban-lite

## 核心业务代码

```
| -- luban-lite
|    | -- /application：用户业务核心代码
|    | -- /bsp：板级支持包
|    | -- /kernel：RT-Thread 内核源码
|    | -- /packages：第三方软件包。存放通过 menuconfig 选中的组件，如 LVGL（图形库）、FATFS（文件系统）、LWIP（网络协议栈）等。
|    | -- /target：芯片/SoC 配置。包含针对 D12X 系列芯片的启动文件、链接脚本（.lds）和特定外设的底层寄存器定义。
|    | -- /tools：构建与辅助工具。存放 SCons 扩展脚本、烧录下载工具（如 artinchip-prog）以及编译时用到的 Python 辅助脚本。
|    | -- /toolchain：交叉编译工具链。
|    | -- /doc：文档目录。存放 API 说明、开发指南等 PDF/HTML 文档。
|    | -- /patches：补丁文件。用于对某些内核或包进行热修复的补丁集合。
|    | -- /output：编译输出目录。存放编译生成的 .o 文件、最终的固件镜像（.img）和反汇编文件。这个目录是动态生成的。
```

## 编译控制

```
| -- luban-lite
|    | -- /SConstruct：SCons 构建主入口。类似于 Makefile 的根文件，定义了编译流程、环境变量和全局规则。
|    | -- /SConscript：SCons 子构建脚本。通常分布在各个子目录下，告诉构建系统该目录下有哪些文件需要编译。
|    | -- /Kconfig：配置系统的根菜单。配合 scons --menuconfig 使用，定义了所有可选模块和特性的依赖关系。
|    | -- /.Kconfig.prj：作为被Kconfig引用的二级菜单
|    | -- /.config：当前生效的完整配置文件。由 menuconfig 生成，记录了当前选中了哪些内核组件、驱动和应用。非常重要，编译依据就是它。
|    | -- /.config.old：上一次配置的备份文件。当你重新配置时，系统会自动备份旧配置到这里。
|    | -- /.defconfig：默认配置的入口。里面通常只存了一行文本，指向 target/ 下某个具体的默认板级配置文件（如 d12x_demo_defconfig）。
|    | -- /rtconfig.h：C 语言头文件版的配置宏。由 .config 转换而来，源码中的预编译条件（#ifdef）依赖它来裁剪代码。
|    | -- /cconfig.h：类似 rtconfig.h，通常存放编译器相关的配置宏。
|    | -- /partition_table.h：
|    | -- /win_cmd.bat：
|    | -- /win_env.bat：
```

## 版本管理和软件

```
| -- luban-lite
|    | -- /.git：git历史记录
|    | -- /.gitignore：忽略版本管理
|    | -- /.log：日志
|    | -- /.vscode：vscode配置
```

## 文档

```
| -- luban-lite
|    | -- /AGENTS.md
|    | -- /CLAUDE.md
|    | -- /CODEMAP.md
|    | -- /CONTRIBUTING.md
|    | -- /ReadMe.md
|    | -- /ReleaseNote.md
```



