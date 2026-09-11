# Options for Target

## Target

Floating Point Hardware：

-   Not Used：不使用硬件 FPU，所有浮点运算（`float` 和 `double`）都用软件库模拟。
-   Single Precision：启用单精度硬件 FPU。`float` 类型运算由硬件执行，`double` 类型仍用软件库模拟。
-   Double Precision：启用双精度硬件 FPU。`float` 和 `double` 类型运算都由硬件执行。



## User

可以在编译/构建过程的不同阶段，自动执行一些外部的用户命令或脚本。



## C/C++

### Optimization

-   