ICMP 是 **Internet Control Message Protocol（互联网控制报文协议）**，中文一般叫 **互联网控制消息协议**。

它属于 **网络层协议**，和 IP 同一层级，用于：

>   ✅ 传递网络中的“控制信息”和“错误报告”
>   ❌ 不负责传输应用数据

------

# 一、ICMP 在哪里？

在 TCP/IP 五层模型中：

```
应用层
传输层（TCP / UDP）
网络层（IP / ICMP）  ← 在这里
数据链路层
物理层
```

很多人误以为 ICMP 是“附属于 TCP”，其实不是。

它是 **IP 协议族的一部分**。

------

# 二、ICMP 是干什么的？

核心作用只有两个：

## 1️⃣ 报告错误

例如：

| 场景           | ICMP 报文               |
| -------------- | ----------------------- |
| 目标主机不存在 | Destination Unreachable |
| 端口未开启     | Port Unreachable        |
| TTL 变成 0     | Time Exceeded           |

比如：

```
ping 不通
```

本质上就是收到了 ICMP 错误报文。

------

## 2️⃣ 网络探测

### 1️⃣ ping 命令

使用的是：

-   ICMP Echo Request
-   ICMP Echo Reply

工作流程：

```
A → (Echo Request) → B
A ← (Echo Reply) ← B
```

这就是你在 Linux 里敲：

```
ping 8.8.8.8
```

背后发生的事情。

------

### 2️⃣ traceroute 命令

traceroute 的原理：

-   每发一个包
-   TTL +1
-   每经过一跳 TTL 减 1
-   TTL = 0 时
-   路由器返回 ICMP Time Exceeded

因此可以看到路径。

------

# 三、ICMP 报文结构

ICMP 报文在 IP 里面。

结构：

```
IP Header
    ↓
ICMP Header
    ↓
Data
```

ICMP Header 主要字段：

```
Type
Code
Checksum
```

------

# 四、常见 ICMP 类型

| Type | 含义                    |
| ---- | ----------------------- |
| 0    | Echo Reply              |
| 3    | Destination Unreachable |
| 8    | Echo Request            |
| 11   | Time Exceeded           |

------

# 五、ICMP 的特点

### ✔ 无连接

### ✔ 不保证可靠

### ✔ 不负责数据传输

### ✔ 用于“报告问题”

可以理解为：

>   ICMP 是 IP 的“报错机制”

------

# 六、现实网络中的注意点

很多服务器会：

-   禁止 ICMP
-   屏蔽 ping

因为：

-   防止网络探测
-   防止扫描

但这会导致排错困难。

------

# 七、你可以这样理解

| 协议 | 作用             |
| ---- | ---------------- |
| TCP  | 传输数据         |
| UDP  | 传输数据         |
| ICMP | 告诉你“出问题了” |

------

# 八、如果你想深入（推荐）

你最近在学网络，我建议你抓一次包看看。

用：

```
ping baidu.com
```

然后用 Wireshark 过滤：

```
icmp
```

你会看到：

-   Echo Request
-   Echo Reply

这对你建立“协议直觉”非常有帮助。

------

如果你愿意，我可以带你：

-   手写 ICMP 报文结构
-   用 C 构造 ICMP
-   或者分析一段抓包数据

你现在已经开始进入网络的核心了 👍