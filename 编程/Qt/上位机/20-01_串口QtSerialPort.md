# 串口QtSerialPort

## 基本

继承自 `QIODevice`，这意味着它可以无缝集成到 Qt 的事件循环中，支持**异步**（非阻塞）操作，这是 Qt 的核心优势。你可以使用信号槽机制来处理数据接收、错误通知等，避免复杂的线程管理或轮询。

## 类

1. **QSerialPort**：用于操作和控制一个具体的串口。

2. **QSerialPortInfo**：一个工具类，用于获取系统上可用的串口列表及其属性（如端口名、描述、制造商等）。

## 功能

*   **端口枚举：** 列出系统上所有可用的串行端口及其基本信息（端口名、描述、制造商、序列号等）。
*   **端口配置：** 设置波特率、数据位、停止位、奇偶校验、流控制（RTS/CTS, DTR/DSR, XON/XOFF）。
*   **数据读写：** 提供 `read()`, `write()`, `readAll()`, `readLine()` 等同步方法，以及利用 `readyRead()` 信号和 `readAll()`/`bytesAvailable()` 的异步读取模式（推荐）。
*   **控制信号：** 控制数据终端就绪（DTR）、请求发送（RTS）信号线的状态，以及读取清除发送（CTS）、数据设备就绪（DSR）、载波检测（DCD）、振铃指示（RI）信号线的状态。
*   **错误处理：** 通过 `errorOccurred()` 信号报告各种串口通信错误（如权限错误、资源错误、奇偶校验错误、帧错误、溢出错误、超时等）。
*   **超时设置：** 设置读写操作的超时时间。

## 函数

## QSerialPort类成员函数



## QSerialPortInfo类成员函数

`QSerialPortInfo` 类提供了访问串行端口信息的方法。以下是您代码中使用的所有成员函数的详细解释：

## 1. `portName()`
- **功能**: 返回串行端口的名称
- **返回值**: `QString` 类型，表示端口名称
- **说明**: 
  - 在 Windows 上通常是 "COM1", "COM2" 等
  - 在 Linux 上通常是 "/dev/ttyS0", "/dev/ttyUSB0" 等
  - 在 macOS 上通常是 "/dev/cu.usbserial", "/dev/cu.Bluetooth" 等

## 2. `description()`
- **功能**: 返回串行端口的描述信息
- **返回值**: `QString` 类型，描述字符串
- **说明**: 
  - 通常包含设备类型或用途的简短描述
  - 例如 "USB-SERIAL CH340" 表示这是一个基于 CH340 芯片的 USB 转串口设备

## 3. `manufacturer()`
- **功能**: 返回设备制造商的名称
- **返回值**: `QString` 类型，制造商名称
- **说明**: 
  - 提供设备制造商的标识信息
  - 例如 "wch.cn" 表示南京沁恒电子有限公司

## 4. `systemLocation()`
- **功能**: 返回串行端口的系统位置路径
- **返回值**: `QString` 类型，系统路径
- **说明**: 
  - 提供操作系统级别的设备路径
  - 在 Windows 上格式为 "\\\\.\\COMx"
  - 在 Unix-like 系统上格式为 "/dev/ttyXx" 或 "/dev/cu.xx"

## 5. `hasVendorIdentifier()`
- **功能**: 检查端口是否有厂商标识符 (Vendor ID)
- **返回值**: `bool` 类型，true 表示有厂商ID，false 表示没有
- **说明**: 
  - USB 设备通常有厂商ID，但某些类型的串口可能没有
  - 在使用 `vendorIdentifier()` 前应先调用此函数检查

## 6. `vendorIdentifier()`
- **功能**: 返回 USB 设备的厂商标识符
- **返回值**: `quint16` 类型，16位无符号整数表示的厂商ID
- **说明**: 
  - 厂商ID由 USB 实施者论坛分配
  - 通常以十六进制形式显示（如 0x1A86）
  - 需要先使用 `hasVendorIdentifier()` 检查是否有有效值

## 7. `hasProductIdentifier()`
- **功能**: 检查端口是否有产品标识符 (Product ID)
- **返回值**: `bool` 类型，true 表示有产品ID，false 表示没有
- **说明**: 
  - 与厂商ID配对使用，唯一标识特定类型的USB设备
  - 在使用 `productIdentifier()` 前应先调用此函数检查

## 8. `productIdentifier()`
- **功能**: 返回 USB 设备的产品标识符
- **返回值**: `quint16` 类型，16位无整数表示的产品ID
- **说明**: 
  - 与厂商ID一起唯一标识设备类型
  - 通常以十六进制形式显示（如 0x7523）
  - 需要先使用 `hasProductIdentifier()` 检查是否有有效值

## 9. `serialNumber()`
- **功能**: 返回设备的序列号
- **返回值**: `QString` 类型，序列号字符串
- **说明**: 
  - 对于有唯一序列号的设备，返回其序列号
  - 许多低成本设备没有烧写序列号，返回空字符串
  - 序列号可用于区分多个相同类型的设备

## 其他常用成员函数

除了您代码中使用的函数外，`QSerialPortInfo` 还提供了一些其他有用的函数：

### `isNull()`
- **功能**: 检查 QSerialPortInfo 对象是否为空
- **返回值**: `bool` 类型
- **说明**: 如果对象没有关联任何串口信息，返回 true

### `isBusy()`
- **功能**: 检查串口是否正被其他应用程序使用
- **返回值**: `bool` 类型
- **说明**: 在某些 Qt 版本中可能不可用或需要特定平台支持

### `standardBaudRates()`
- **功能**: 返回端口支持的标准波特率列表
- **返回值**: `QList<qint32>` 类型，包含支持的波特率
- **说明**: 可用于确定可以与设备通信的合适波特率

### `static availablePorts()`
- **功能**: 静态函数，返回系统上所有可用串口的列表
- **返回值**: `QList<QSerialPortInfo>` 类型
- **说明**: 这是获取所有串口信息的主要方法

## 使用示例

```cpp
// 获取所有可用串口
QList<QSerialPortInfo> ports = QSerialPortInfo::availablePorts();

// 遍历每个端口
foreach (const QSerialPortInfo &port, ports) {
    if (!port.isNull()) {
        qDebug() << "Port:" << port.portName();
        
        // 检查并显示厂商ID和产品ID
        if (port.hasVendorIdentifier()) {
            qDebug() << "Vendor ID:" << QString::number(port.vendorIdentifier(), 16);
        }
        
        if (port.hasProductIdentifier()) {
            qDebug() << "Product ID:" << QString::number(port.productIdentifier(), 16);
        }
        
        // 显示支持的波特率
        qDebug() << "Supported baud rates:" << port.standardBaudRates();
    }
}
```

这些函数共同提供了访问串口设备信息的完整接口，使您能够识别设备类型、检查设备状态并确定如何与设备通信。







```cpp

```

