# menuconfig

## menuconfig

```
    Build type  ----------------------------> 编译类型
    Bootloader config  ---------------------> Bootloader 配置
    Security features  ---------------------> 安全特性
    Application manager  -------------------> 应用程序管理器
    Boot ROM Behavior ----------------------> Boot ROM 行为
    Serial flasher config  -----------------> 串行闪存配置
    Partition Table  -----------------------> 分区表
    Example Configuration  -----------------> 示例配置
    Example Connection Configuration  ------> 示例连接配置
    Compiler options  ----------------------> 编译器选项
    Component config  ----------------------> 组件配置
[ ] Make experimental features visible (default value)显示实验性功能  ---> （默认值）
```



### Example Connection Configuration  ------> 示例连接配置

```
(Top) → Example Connection Configuration -----------------> 示例连接配置
                                       Espressif IoT 开发框架配置 <------------- Espressif IoT Development Framework Configuration
[*] connect using WiFi interface (default value) -------------------------> 使用 WiFi 接口连接（默认值）
[ ]     Get ssid and password from stdin (default value) -----------------> 从标准输入获取 SSID 和密码（默认值）
[*]     Provide wifi connect commands (default value) --------------------> 提供 WiFi 连接命令（默认值）
(myssid) WiFi SSID (default value) ---------------------------------------> WiFi SSID（默认值）
(mypassword) WiFi Password (default value) -------------------------------> WiFi 密码（默认值）
(6)     Maximum retry (default value) ------------------------------------> 最大重试次数（默认值）
        WiFi Scan Method (All Channel)  ----------------------------------> WiFi 扫描方法（所有信道）
        WiFi Scan threshold  ---------------------------------------------> WiFi 扫描阈值
        WiFi Connect AP Sort Method (Signal)  ----------------------------> WiFi 连接 AP 排序方法（信号强度）
[ ] connect using Ethernet interface (default value) ---------------------> 使用以太网接口连接（默认值）
[ ] connect using Point to Point interface (default value) ---------------> 使用点对点接口连接（默认值）
[*] Obtain IPv6 address (default value) ----------------------------------> 获取 IPv6 地址（默认值）
        Preferred IPv6 Type (Local Link Address)  ------------------------> 首选 IPv6 类型（本地链接地址）
```















## 快捷键



## 基本操作键

### **[Space/Enter] Toggle/enter**

- **空格键** 或 **回车键**：切换选项状态或进入子菜单
- 对于 `[ ]` 复选框：空格切换选中/取消
- 对于 `( )` 单选按钮：空格选择该项
- 对于菜单项：回车进入子菜单

### **[ESC] Leave menu**

- **ESC键**：返回上一级菜单
- 长按 ESC 可以快速退出到主菜单

## 文件操作

### **[S] Save**

- **S键**：保存当前配置到 `sdkconfig` 文件
- 会覆盖之前的配置

### **[O] Load**

- **O键**：从文件加载配置
- 可以导入其他配置文件

### **[Q] Quit (prompts for save)**

- **Q键**：退出 menuconfig
- 退出前会询问是否保存更改

### **[D] Save minimal config (advanced)**

- **D键**：保存最小配置（只保存修改的选项）
- 适合高级用户使用

## 查看和搜索

### **[?] Symbol info**

- **?键**：显示当前选中配置项的详细信息
- 包括帮助文档、依赖关系等

### **[/] Jump to symbol**

- **/键**：搜索符号/配置项
- 输入关键字快速定位配置

## 显示模式切换

### **[F] Toggle show-help mode**

- **F键**：切换显示帮助模式
- 显示/隐藏每个配置项的详细帮助信息

### **[C] Toggle show-name mode**

- **C键**：切换显示名称模式
- 显示配置项的符号名称（如 `CONFIG_PARTITION_TABLE_CUSTOM`）

### **[A] Toggle show-all mode**

- **A键**：切换显示所有模式
- 显示所有配置项，包括隐藏的或依赖未满足的

## 其他功能

### **[R] Reset to default value**

- **R键**：重置当前选项为默认值
- 只对当前选中的配置项有效

## 实用操作技巧

1. **快速导航**：使用方向键上下移动
2. **快速保存退出**：按 `Q` → 按 `Y` 确认保存
3. **搜索配置**：按 `/` 输入关键字如 `partition` 快速定位
4. **查看详情**：选中选项后按 `?` 了解作用

这些快捷键让配置 ESP-IDF 项目变得更加高效！

