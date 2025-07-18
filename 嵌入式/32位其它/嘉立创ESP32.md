# ESP32

## 安装

下载链接

https://www.arduino.cc/en/software

添加离线工具，路径为：

```
C:\Users\{用户名}\AppData\Local\Arduino15\packages\
```

在【File】->【 Preferences】修改中文显示。

嘉立创开发板为ESP32S3 Dev Moudule；

## 基本介绍

Arduino 程序（被称为 “sketch”，草图）通常包括两个主要函数：

- `setup()`: 这个函数在程序启动时运行一次。通常用于初始化引脚模式、启动库等。
- `loop()`: 这个函数在 `setup()` 函数执行后就**反复**运行。用于执行程序的主要逻辑。
- 通常创建一个新工程，都是以`setup()`和`loop()`为模板。

## 引脚的工作模式

**`pinMode()`**是 Arduino 编程语言中的一个函数，用于设置指定引脚的工作模式。它的语法如下：

`pinMode(pin(引脚号), mode(工作模式));`

**工作模式mode有四种，分别是**：**`INPUT`** **输入模式**，**`INPUT_PULLUP`** **内置上拉输入模式**，**`INPUT_PULLDOWN`** **内置下拉输入模式**，**`OUTPUT` ** **输出模式**。

> **前三种为输入模式，具体介绍是：**
>
> - **`INPUT`**: 将指定引脚设置为**输入模式**，用于接收外部信号或传感器数据。在此模式下，引脚会读取外部信号的电平。需要注意的是，在此模式下，引脚可能会处于悬空状态，导致不稳定的读数。为解决此问题，可以使用外部上拉或下拉电阻或者改为使用内置的上拉电阻（见下文）。
> - **`INPUT_PULLUP`**：将引脚设置为**内置上拉输入模式**。在此模式下，引脚连接到一个内部的上拉电阻，它会将悬空引脚保持在高电平状态。当外部电平为低电平时，读数会切换到`LOW`。
> - **`INPUT_PULLDOWN`**：将引脚设置为**内置下拉输入模式**，在此模式下，Arduino会在输入端接入一个将引脚连接到地的电阻，以确保输入端始终处于低电平状态。当外部电路未连接或者处于高阻状态时，Arduino输入引脚会仍然保持在低电平状态。
>
> **第四种是输出，介绍为：**
>
> - **`OUTPUT`**: 将指定引脚设置为**输出模式**，用于发送电信号或控制外部设备。在此模式下，引脚可以输出高电平（`HIGH`）或低电平（`LOW`）。可用于驱动LED、继电器等外部设备。
>

**读取引脚电平和设置引脚电平的函数：**

**读取引脚电平：**

使用`digitalRead()` 函数读取引脚电平状态。当引脚设置为输入模式（`INPUT` 或 `INPUT_PULLUP`）时，你可以使用这个函数来读取该引脚的当前电平状态。

`digitalRead()`函数只有一个参数：

- 引脚编号（Pin number）：这是你要读取的数字引脚的编号。

`digitalRead()` 函数返回的值为 `HIGH`（高电平）或 `LOW`（低电平）。

以一个使用了 `digitalRead()`的示例为例：

`int buttonState = digitalRead(2);`

在此例子中，我们从编号为2的引脚上（GPIO2）读取值。返回的值存储在变量`buttonState`中，它将会是`HIGH`或者`LOW`，分别表示在该引脚上读取的电平是高还是低。

**设置引脚电平：**

使用**`digitalWrite()`** 函数设置数字引脚电平。它用来将数字引脚设置为 HIGH 或 LOW。当引脚设置为 OUTPUT 模式时，使用该函数可以改变引脚电平从而影响连接到该引脚的组件。

函数的语法为：`digitalWrite(pin, value);`

**可以设计为:**

- `pin`： 是你想要写入的数字引脚编号；
- `value`： 是你要设置的电平，可以是 `HIGH` 或` LOW`。其中`HIGH`表示高电平，`LOW`表示低电平。

## 中断

在 `setup()` 函数中，使用 `attachInterrupt(digitalPinToInterrupt(pin), ISR, mode);` 函数将中断服务程序绑定到引脚上。可以选择触发中断的方式，例如上升沿、下降沿或状态变化。

其中 `pin` 是要配置为中断引脚的GPIO引脚编号。`ISR` 是要绑定的中断服务程序的函数名或指针。 `mode` 是中断触发条件，即满足何种条件时触发中断。常见的触发条件有上升沿、下降沿、状态变化等。相关参数如下：

1. `CHANGE`：触发条件为引脚电平变化，即从低电平到高电平或从高电平到低电平都会触发中断。
2. `RISING`：触发条件为引脚从低电平变为高电平时触发中断。
3. `FALLING`：触发条件为引脚从高电平变为低电平时触发中断。
4. `LOW`：触发条件为引脚保持低电平时触发中断。
5. `HIGH`：触发条件为引脚保持高电平时触发中断。

当中断发生时执行中断服务程序 `button_ISR()` 。`button_ISR()`写在`setup()`函数前。

注意，`attachInterrupt()`函数只适用于 `digitalPinToInterrupt()` 所支持的GPIO引脚，而不是所有的GPIO引脚都能用于外部中断。

此外，在中断服务函数进行中断处理时，一定要避免使用占用 CPU 大量时间的操作（例如延时函数），以确保中断响应速度和精度。

**`attachInterrupt()` 函数有两种参数传递方式，可以传递函数指针或者函数名。如果是函数名，可以直接传递函数名，不需要加上括号。如果函数名被加上了括号，那就相当于调用该函数，传递的则是函数的返回值。如果使用函数指针，那么需要在函数名前加上 `&` 符号。**

## 串口



## 计时器

可以使用`delay()`函数进行延时，单位为秒，如：`delay(1000);` 即为延时1000ms，也就是1s。

但`delay()`函数是一种阻塞性延时，可能影响其他部分的运行。

可以使用 `millis()` 函数来获取自程序启动以来的毫秒数并进行计时，`millis()` 函数为自程序启动以来的毫秒为单位计时的数字。

## PWM



## ADC



## I2C



## SPI



