# WIFI连接

## menuconfig

```
    Build type  ----------------------------> 编译类型
    Bootloader config  ---------------------> Bootloader 配置
    Security features  ---------------------> 安全特性
    Application manager  -------------------> 应用程序管理器
    Boot ROM Behavior ----------------------> Boot ROM 行为
    Serial flasher config  -----------------> 串行闪存配置
    Partition Table  -----------------------> 分区表
    Example Configuration  -----------------> 示例配置 --------------------> 选这个
    Example Connection Configuration  ------> 示例连接配置 -----------------> 选这个
    Compiler options  ----------------------> 编译器选项
    Component config  ----------------------> 组件配置
[ ] Make experimental features visible (default value)显示实验性功能  ---> （默认值）
```

### Example Connection Configuration

```
(Top) → Example Connection Configuration -----------------> 示例连接配置
                                       Espressif IoT 开发框架配置 <------------- Espressif IoT Development Framework Configuration
[*] connect using WiFi interface (default value) -------------------------> 使用 WiFi 接口连接（默认值）
[ ]     Get ssid and password from stdin (default value) -----------------> 从标准输入获取 SSID 和密码（默认值）
[*]     Provide wifi connect commands (default value) --------------------> 提供 WiFi 连接命令（默认值）
(myssid) WiFi SSID (default value) ---------------------------------------> WiFi SSID（默认值）----------------> 更改
(mypassword) WiFi Password (default value) -------------------------------> WiFi 密码（默认值）-----------------> 更改
(6)     Maximum retry (default value) ------------------------------------> 最大重试次数（默认值）
        WiFi Scan Method (All Channel)  ----------------------------------> WiFi 扫描方法（所有信道）
        WiFi Scan threshold  ---------------------------------------------> WiFi 扫描阈值
        WiFi Connect AP Sort Method (Signal)  ----------------------------> WiFi 连接 AP 排序方法（信号强度）
[ ] connect using Ethernet interface (default value) ---------------------> 使用以太网接口连接（默认值）
[ ] connect using Point to Point interface (default value) ---------------> 使用点对点接口连接（默认值）
[*] Obtain IPv6 address (default value) ----------------------------------> 获取 IPv6 地址（默认值）
        Preferred IPv6 Type (Local Link Address)  ------------------------> 首选 IPv6 类型（本地链接地址）
```

### Example Configuration

```
ample Configuration -----------------> 示例连接配置
                                Espressif IoT 开发框架配置 <------------- Espressif IoT Development Framework Configuration
(https://192.168.0.3:8070/hello_world.bin) firmware upgrade url endpoint (default value)---------->固件升级 URL 端点（默认值）
[*] Enable certificate bundle (default value) ----------------------------------------> 启用证书包（默认值）
[ ] Skip server certificate CN fieldcheck (default value) ----------------------------> 跳过服务器证书 CN 字段检查（默认值）
[ ] Support firmware upgrade bind specified interface (default value) ----------------> 支持固件升级绑定指定接口（默认值）
```

