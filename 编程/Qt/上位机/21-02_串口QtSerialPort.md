# 打开串口并发送 0xAA 0x01 的完整代码

以下是使用 Qt 的 QSerialPort 打开串口并发送十六进制数据 `0xAA 0x01` 的完整实现：

## 1. 头文件配置

首先确保在 `.pro` 或 `CMakeLists.txt` 中包含 SerialPort 模块：

**CMakeLists.txt:**
```cmake
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS SerialPort)
target_link_libraries(your_target PRIVATE Qt${QT_VERSION_MAJOR}::SerialPort)
```

## 2. 头文件包含

在头文件中声明串口对象和函数：

```cpp
#include <QMainWindow>
#include <QSerialPort>
#include <QSerialPortInfo>

class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    explicit MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

private slots:
    void onOpenSerialButtonClicked();    // 打开串口按钮点击
    void onSendDataButtonClicked();      // 发送数据按钮点击
    void onSerialDataReceived();         // 接收数据
    void onSerialErrorOccurred();        // 串口错误处理

private:
    QSerialPort *serial;                 // 串口对象
    void openSerialPort();               // 打开串口函数
    void sendHexData();                  // 发送十六进制数据函数
};
```

## 3. 实现代码

### 初始化串口对象

```cpp
MainWindow::MainWindow(QWidget *parent) : QMainWindow(parent)
{
    // 初始化串口对象
    serial = new QSerialPort(this);
    
    // 连接信号槽
    connect(serial, &QSerialPort::readyRead, this, &MainWindow::onSerialDataReceived);
    connect(serial, &QSerialPort::errorOccurred, this, &MainWindow::onSerialErrorOccurred);
    
    // 假设有打开和发送按钮
    connect(ui->openButton, &QPushButton::clicked, this, &MainWindow::onOpenSerialButtonClicked);
    connect(ui->sendButton, &QPushButton::clicked, this, &MainWindow::onSendDataButtonClicked);
}
```

### 打开串口函数

```cpp
void MainWindow::openSerialPort()
{
    // 设置串口参数
    serial->setPortName("COM4");  // 根据实际情况修改端口名
    serial->setBaudRate(QSerialPort::Baud9600);
    serial->setDataBits(QSerialPort::Data8);
    serial->setParity(QSerialPort::NoParity);
    serial->setStopBits(QSerialPort::OneStop);
    serial->setFlowControl(QSerialPort::NoFlowControl);

    // 尝试打开串口
    if (serial->open(QIODevice::ReadWrite)) {
        qDebug() << "串口打开成功:" << serial->portName();
        ui->statusLabel->setText("串口已打开: " + serial->portName());
    } else {
        qDebug() << "串口打开失败:" << serial->errorString();
        ui->statusLabel->setText("打开失败: " + serial->errorString());
    }
}
```

### 发送十六进制数据 0xAA 0x01

```cpp
void MainWindow::sendHexData()
{
    // 检查串口是否打开
    if (!serial->isOpen()) {
        qDebug() << "串口未打开";
        ui->statusLabel->setText("请先打开串口");
        return;
    }

    // 准备要发送的十六进制数据: 0xAA 0x01
    QByteArray data;
    data.append(0xAA);  // 第一个字节: 0xAA
    data.append(0x01);  // 第二个字节: 0x01

    // 发送数据
    qint64 bytesWritten = serial->write(data);
    
    if (bytesWritten == -1) {
        qDebug() << "发送失败:" << serial->errorString();
        ui->statusLabel->setText("发送失败: " + serial->errorString());
    } else if (bytesWritten != data.size()) {
        qDebug() << "发送不完整，已发送:" << bytesWritten << "字节，总共:" << data.size() << "字节";
        ui->statusLabel->setText("发送不完整");
    } else {
        qDebug() << "成功发送" << bytesWritten << "字节数据:" << data.toHex(' ');
        ui->statusLabel->setText("发送成功: AA 01");
    }
    
    // 确保数据被发送（刷新缓冲区）
    serial->flush();
}
```

### 按钮点击槽函数

```cpp
void MainWindow::onOpenSerialButtonClicked()
{
    openSerialPort();
}

void MainWindow::onSendDataButtonClicked()
{
    sendHexData();
}
```

### 接收数据和处理错误

```cpp
void MainWindow::onSerialDataReceived()
{
    // 读取所有可用数据
    QByteArray data = serial->readAll();
    
    // 将数据转换为十六进制字符串显示
    QString hexData = data.toHex(' ').toUpper();
    qDebug() << "接收到数据:" << hexData;
    
    // 在UI中显示接收到的数据
    ui->receiveTextEdit->append("接收: " + hexData);
}

void MainWindow::onSerialErrorOccurred(QSerialPort::SerialPortError error)
{
    if (error != QSerialPort::NoError) {
        qDebug() << "串口错误:" << serial->errorString();
        ui->statusLabel->setText("错误: " + serial->errorString());
        
        // 如果串口意外关闭，尝试重新打开或进行其他处理
        if (error == QSerialPort::ResourceError) {
            serial->close();
            qDebug() << "串口已关闭";
        }
    }
}
```

## 4. 完整的工具函数

### 自动检测并选择串口

```cpp
QString MainWindow::autoDetectSerialPort()
{
    QList<QSerialPortInfo> ports = QSerialPortInfo::availablePorts();
    
    if (ports.isEmpty()) {
        qDebug() << "没有找到可用串口";
        return "";
    }
    
    // 优先选择 CH340 设备（常见USB转串口）
    foreach (const QSerialPortInfo &port, ports) {
        if (port.hasVendorIdentifier() && port.vendorIdentifier() == 0x1A86 &&
            port.hasProductIdentifier() && port.productIdentifier() == 0x7523) {
            qDebug() << "找到 CH340 设备:" << port.portName();
            return port.portName();
        }
    }
    
    // 如果没有特定设备，选择第一个可用端口
    qDebug() << "使用第一个可用端口:" << ports.first().portName();
    return ports.first().portName();
}
```

### 改进的打开串口函数

```cpp
void MainWindow::openSerialPort()
{
    // 如果串口已打开，先关闭
    if (serial->isOpen()) {
        serial->close();
    }
    
    // 自动检测或使用指定端口
    QString portName = ui->portComboBox->currentText(); // 从UI选择
    // 或者自动检测: QString portName = autoDetectSerialPort();
    
    if (portName.isEmpty()) {
        qDebug() << "没有可用串口";
        return;
    }
    
    serial->setPortName(portName);
    serial->setBaudRate(ui->baudRateComboBox->currentText().toInt());
    serial->setDataBits(QSerialPort::Data8);
    serial->setParity(QSerialPort::NoParity);
    serial->setStopBits(QSerialPort::OneStop);
    serial->setFlowControl(QSerialPort::NoFlowControl);

    if (serial->open(QIODevice::ReadWrite)) {
        qDebug() << "串口打开成功:" << portName;
        ui->statusLabel->setText("已连接: " + portName);
        ui->sendButton->setEnabled(true);
    } else {
        qDebug() << "打开失败:" << serial->errorString();
        ui->statusLabel->setText("打开失败: " + serial->errorString());
    }
}
```

## 5. 使用示例

```cpp
// 发送特定协议的示例
void MainWindow::sendCustomProtocol()
{
    if (!serial->isOpen()) return;
    
    // 构建协议帧: 头 + 命令 + 数据 + 校验
    QByteArray frame;
    frame.append(0xAA);      // 帧头
    frame.append(0x01);      // 命令字
    frame.append(0x00);      // 数据长度
    frame.append(0x00);      // 数据（空）
    
    // 计算校验和（简单示例）
    char checksum = 0;
    for (char byte : frame) {
        checksum ^= byte;    // 异或校验
    }
    frame.append(checksum);
    
    // 发送数据
    serial->write(frame);
    serial->flush();
    
    qDebug() << "发送协议帧:" << frame.toHex(' ');
}
```

## 注意事项

1. **权限问题**：在 Linux 上可能需要将用户添加到 dialout 组
2. **端口占用**：确保串口没有被其他程序占用
3. **参数匹配**：串口参数（波特率、数据位等）必须与设备匹配
4. **错误处理**：总是检查串口操作的成功状态
5. **线程安全**：串口操作最好在单独的线程中进行

这样您就可以成功打开串口并发送十六进制数据 `0xAA 0x01` 了。