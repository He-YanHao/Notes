# 串口通信QtSerialPort

## 在项目中使用 Qt Serial Port

要使用 `QSerialPort`，首先需要在项目配置文件（`.pro` 文件）中添加对应的模块：

```qmake
QT += serialport
```

或者修改CMakeLists.txt文件

```cmake
# 添加 SerialPort 组件
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets SerialPort)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets SerialPort)

# 在链接库部分添加 SerialPort
target_link_libraries(Host_Computer PRIVATE 
    Qt${QT_VERSION_MAJOR}::Widgets 
    Qt${QT_VERSION_MAJOR}::SerialPort
)
```

然后在代码中包含头文件：

```cpp
#include <QSerialPort>
#include <QSerialPortInfo> // 用于获取串口信息
```

## 核心类介绍

该模块主要包含两个类：

1. **QSerialPort**：用于操作和控制一个具体的串口。

2. **QSerialPortInfo**：一个工具类，用于获取系统上可用的串口列表及其属性（如端口名、描述、制造商等）。





## 使用QSerialPort的基本流程

### 查找可用串口

使用 `QSerialPortInfo::availablePorts()` 来获取系统上所有可用的串口。

```cpp
QList<QSerialPortInfo> portList = QSerialPortInfo::availablePorts();
foreach (const QSerialPortInfo &portInfo, portList) {
    qDebug() << "Port Name:" << portInfo.portName();
    qDebug() << "Description:" << portInfo.description();
    qDebug() << "Manufacturer:" << portInfo.manufacturer();
    qDebug() << "-----------------------------------";
}
// 输出可能类似：
// Port Name: "COM3"
// Description: "USB-SERIAL CH340"
// Manufacturer: "wch.cn"
// -----------------------------------
// Port Name: "ttyUSB0"
// Description: "USB Serial"
// -----------------------------------
```

### 步骤 2：配置并打开串口

创建一个 `QSerialPort` 对象，设置相关参数，然后打开它。

```cpp
QSerialPort *serial = new QSerialPort(this); // 通常将parent设为this，便于内存管理

// 1. 设置端口名
serial->setPortName("COM3"); // Windows
// serial->setPortName("/dev/ttyUSB0"); // Linux

// 2. 尝试以读写模式打开串口
if (!serial->open(QIODevice::ReadWrite)) {
    qDebug() << "Failed to open port" << serial->portName() << "error:" << serial->error();
    return;
}

// 3. 配置串口参数（这是最关键的一步）
serial->setBaudRate(QSerialPort::Baud115200); // 设置波特率
serial->setDataBits(QSerialPort::Data8);      // 设置数据位为8位
serial->setParity(QSerialPort::NoParity);     // 设置无校验
serial->setStopBits(QSerialPort::OneStop);    // 设置停止位为1位
serial->setFlowControl(QSerialPort::NoFlowControl); // 设置无流控制

// 所有设置也可以在一次调用中完成（链式调用）
// serial->setBaudRate(QSerialPort::Baud115200)
//        .setDataBits(QSerialPort::Data8)
//        .setParity(QSerialPort::NoParity)
//        .setStopBits(QSerialPort::OneStop)
//        .setFlowControl(QSerialPort::NoFlowControl);
```

### 步骤 3：连接信号与槽（异步读取数据）

这是 Qt 串口编程最方便的地方。你不需要自己创建线程去循环读取数据，只需要连接 `readyRead()` 信号到一个槽函数即可。

```cpp
// 当串口缓冲区有数据可读时，readyRead()信号被发射，从而触发readData()槽函数
connect(serial, &QSerialPort::readyRead, this, &MyClass::readData);

// 在槽函数中读取并处理数据
void MyClass::readData() {
    // 读取所有可用数据
    QByteArray data = serial->readAll();
    
    // 或者也可以一次读取一行（如果对方是按行发送的）
    // while (serial->canReadLine()) {
    //    QByteArray line = serial->readLine();
    //    processLine(line);
    // }
    
    // 处理接收到的数据，例如转换为字符串并显示
    QString receivedText = QString::fromUtf8(data);
    ui->textEdit->append("Received: " + receivedText);
    
    // 也可以直接处理原始字节数据
    // processBytes(data);
}
```

### 步骤 4：向串口写入数据

使用 `write()` 方法发送数据。因为 `QSerialPort` 是 `QIODevice` 的子类，所以用法完全一致。

```cpp
// 发送字符串
QString message = "Hello, Serial!\r\n"; // 注意换行符，取决于对方协议
serial->write(message.toUtf8());

// 发送原始字节数据
QByteArray data;
data.append(0x01);
data.append(0x02);
data.append(0x03);
serial->write(data);
```

### 步骤 5：错误处理

连接 `errorOccurred` 信号来处理运行时错误（如断开连接）。

```cpp
connect(serial, &QSerialPort::errorOccurred, this, &MyClass::handleError);

void MyClass::handleError(QSerialPort::SerialPortError error) {
    if (error == QSerialPort::ResourceError) {
        qDebug() << "Critical error, port disconnected?" << serial->errorString();
        serial->close();
    }
}
```

### 步骤 6：关闭串口

在不再需要时，关闭串口并清理资源。

```cpp
serial->close();
// 如果 serial 是动态创建且设置了parent（如this），则无需手动delete。
// 否则，需要 delete serial;
```

---

### 常用配置选项详解

*   **波特率 (BaudRate)**：常见值有 `9600`, `19200`, `38400`, `115200` 等。**必须与设备匹配**。
*   **数据位 (DataBits)**：`Data5`, `Data6`, `Data7`, `Data8`（最常用）。
*   **校验位 (Parity)**：`NoParity`（无校验，最常用）, `EvenParity`（偶校验）, `OddParity`（奇校验）等。
*   **停止位 (StopBits)**：`OneStop`（1位，最常用）, `OneAndHalfStop`, `TwoStop`。
*   **流控制 (FlowControl)**：`NoFlowControl`（无流控，最常用）, `HardwareControl`（RTS/CTS）, `SoftwareControl`（XON/XOFF`）。





## QSerialPort 类专有成员函数

### 串口配置函数

| 函数原型                                                     | 功能描述                        | 案例                                                         |
| ------------------------------------------------------------ | ------------------------------- | ------------------------------------------------------------ |
| `void setPortName(const QString &name)`                      | 设置串口设备名称                | `serial.setPortName("COM3");` // Windows<br />`serial.setPortName("/dev/ttyUSB0");` // Linux |
| `void setBaudRate(qint32 baudRate, Directions directions = AllDirections)` | 设置波特率                      | `serial.setBaudRate(QSerialPort::Baud115200);` <br> `serial.setBaudRate(9600, QSerialPort::Input);` // 仅设置输入方向 |
| `void setDataBits(DataBits dataBits)`                        | 设置数据位                      | `serial.setDataBits(QSerialPort::Data8);` // 8位数据位       |
| `void setParity(Parity parity)`                              | 设置校验位                      | `serial.setParity(QSerialPort::NoParity);` // 无校验         |
| `void setStopBits(StopBits stopBits)`                        | 设置停止位                      | `serial.setStopBits(QSerialPort::OneStop);` // 1位停止位     |
| `void setFlowControl(FlowControl flowControl)`               | 设置流控制                      | `serial.setFlowControl(QSerialPort::NoFlowControl);` // 无流控制 |
| `void setDataTerminalReady(bool set)`                        | 设置DTR(数据终端就绪)信号线状态 | `serial.setDataTerminalReady(true);` // 置高DTR信号          |
| `void setRequestToSend(bool set)`                            | 设置RTS(请求发送)信号线状态     | `serial.setRequestToSend(true);` // 置高RTS信号              |

#### 硬件流控制HardwareControl

- **工作原理**：
  - **CTS (Clear To Send)**：接收方→发送方，"我可以接收数据"
  - **RTS (Request To Send)**：发送方→接收方，"我想要发送数据"
- **接线要求**：需要连接RTS、CTS引脚（共5线制：TXD、RXD、RTS、CTS、GND）

### 返回串口配置函数

| 函数原型                                                     | 功能描述               | 案例                                                         |
| ------------------------------------------------------------ | ---------------------- | ------------------------------------------------------------ |
| `QString portName() const`                                   | 返回当前设置的串口名称 | `QString name = serial.portName();`                          |
| `qint32 baudRate(Directions direction = AllDirections) const` | 返回当前设置的波特率   | `qint32 rate = serial.baudRate();` <br>`qDebug() << "Baud rate:" << rate;` |
| `DataBits dataBits() const`                                  | 返回当前设置的数据位   | `QSerialPort::DataBits bits = serial.dataBits();` <br> `if(bits == QSerialPort::Data8) {...}` |
| `Parity parity() const`                                      | 返回当前设置的校验位   | `QSerialPort::Parity p = serial.parity();` <br> `if(p == QSerialPort::NoParity) {...}` |
| `StopBits stopBits() const`                                  | 返回当前设置的停止位   | `QSerialPort::StopBits sb = serial.stopBits();` <br> `if(sb == QSerialPort::OneStop) {...}` |
| `FlowControl flowControl() const`                            | 返回当前设置的流控制   | `QSerialPort::FlowControl fc = serial.flowControl();` <br> `if(fc == QSerialPort::NoFlowControl) {...}` |
| `bool isDataTerminalReady()`                                 | 返回DTR信号线状态      | `bool dtrState = serial.isDataTerminalReady();` <br> `qDebug() << "DTR state:" << dtrState;` |
| `bool isRequestToSend()`                                     | 返回RTS信号线状态      | `bool rtsState = serial.isRequestToSend();` <br> `qDebug() << "RTS state:" << rtsState;` |





# 还没看

### 2. 引脚状态函数

| 函数原型                              | 功能描述                                  |      |
| ------------------------------------- | ----------------------------------------- | ---- |
| `bool isDataCarrierDetected() const`  | 检查 DCD (Data Carrier Detect) 信号线状态 |      |
| `bool isDataSetReady() const`         | 检查 DSR (Data Set Ready) 信号线状态      |      |
| `bool isRingIndicator() const`        | 检查 RI (Ring Indicator) 信号线状态       |      |
| `bool isClearToSend() const`          | 检查 CTS (Clear To Send) 信号线状态       |      |
| `PinoutSignals pinoutSignals() const` | 返回所有信号线的状态                      |      |

### 3. 错误处理函数

| 函数原型                        | 功能描述               |      |
| ------------------------------- | ---------------------- | ---- |
| `SerialPortError error() const` | 返回最后发生的错误类型 |      |
| `void clearError()`             | 清除错误状态           |      |

### 4. 缓冲区管理函数

| 函数原型                              | 功能描述           |
| ------------------------------------- | ------------------ |
| `void setReadBufferSize(qint64 size)` | 设置读取缓冲区大小 |
| `qint64 readBufferSize() const`       | 返回读取缓冲区大小 |

### 5.  Break 控制函数

| 函数原型                                | 功能描述                |
| --------------------------------------- | ----------------------- |
| `bool isBreakEnabled() const`           | 检查 Break 状态是否启用 |
| `void setBreakEnabled(bool set = true)` | 设置或清除 Break 状态   |

### 6. 枚举类型

QSerialPort 定义了以下枚举类型，用于配置串口参数：

1. `enum BaudRate` - 波特率选项
2. `enum DataBits` - 数据位选项
3. `enum Parity` - 校验位选项
4. `enum StopBits` - 停止位选项
5. `enum FlowControl` - 流控制选项
6. `enum Direction` - 方向选项（输入/输出）
7. `enum PinoutSignal` - 引脚信号选项
8. `enum SerialPortError` - 串口错误类型

### 使用示例

```cpp
#include <QSerialPort>

// 创建串口对象
QSerialPort serial;

// 设置串口名称
serial.setPortName("COM3"); // Windows
// serial.setPortName("/dev/ttyUSB0"); // Linux

// 配置串口参数
serial.setBaudRate(QSerialPort::Baud115200);
serial.setDataBits(QSerialPort::Data8);
serial.setParity(QSerialPort::NoParity);
serial.setStopBits(QSerialPort::OneStop);
serial.setFlowControl(QSerialPort::NoFlowControl);

// 打开串口
if (serial.open(QIODevice::ReadWrite)) {
    // 设置信号线状态
    serial.setDataTerminalReady(true);
    serial.setRequestToSend(true);
    
    // 读取信号线状态
    bool dcd = serial.isDataCarrierDetected();
    bool cts = serial.isClearToSend();
    
    // 获取当前配置
    QSerialPort::BaudRate baud = serial.baudRate();
    QSerialPort::DataBits dataBits = serial.dataBits();
    
    // 处理错误
    if (serial.error() != QSerialPort::NoError) {
        // 错误处理
        serial.clearError();
    }
    
    // 设置Break状态
    serial.setBreakEnabled(true);
    
    // 关闭串口
    serial.close();
}
```

这些是 QSerialPort 类自身的成员函数，不包括从 QIODevice 继承的函数。这些函数专门用于配置和控制串口通信的各个方面。