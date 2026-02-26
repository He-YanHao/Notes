# LwIP

lwIP 是一个：

>   轻量级 TCP/IP 协议栈（软件实现）

主要用于：

-   MCU
-   嵌入式系统
-   资源受限设备（几十 KB RAM）

------

# 它解决什么问题？

很多单片机：

-   只有 CPU
-   没有操作系统
-   没有网络协议栈

但你想：

-   连 Wi-Fi
-   用 TCP
-   发 HTTP
-   建 Socket

这时就需要 LwIP。

它负责：

-   IP 封包
-   TCP 三次握手
-   UDP 收发
-   ARP 地址解析
-   DHCP 获取 IP
-   ICMP（ping）

------

# 它在哪一层？

对应 TCP/IP 五层模型：

```
第3层：IP（网络层）
第4层：TCP/UDP（传输层）
```

它不包含：

-   物理层（Wi-Fi、以太网）
-   应用层（HTTP、MQTT）

------

# 典型结构（以 ESP32 为例）

```
Wi-Fi 驱动
   ↓
LwIP
   ↓
Socket API
   ↓
你的代码
```

------

# 为什么叫“Lightweight”？

相比 Linux 内核协议栈：

-   代码小
-   内存占用低
-   可裁剪
-   可关闭不需要的协议

可以运行在：

-   几十 KB RAM
-   无操作系统（裸机）
-   或 RTOS（如 FreeRTOS）

------

# 两种 API 模式

LwIP 提供：

1.  Raw API（性能高，嵌入式常用）
2.  Socket API（类似 Linux，用起来简单）

在 FreeRTOS + ESP32 中通常用 Socket API。

------

# 它常出现在哪？

-   ESP8266 / ESP32
-   STM32 + Ethernet
-   GD32 + W5500
-   各种 IoT 设备

------

# 一句话总结

>   LwIP 是嵌入式设备里的“网络核心大脑”，负责 TCP/IP 协议逻辑，但不负责无线信号传输。

------

如果你愿意，我可以下一步讲：

-   LwIP 内部结构（pbuf / netif / tcp_pcb）
-   或对比 Linux 内核协议栈
-   或结合你现在的 ESP32 实战讲一次完整数据流

你现在已经进入“理解协议栈实现”的阶段了。