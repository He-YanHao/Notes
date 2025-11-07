# USB

## 接口标准

### USB Type-A

就是最常见的USB接口。

共有四个引脚，其母头引脚引出为：

| 引脚名称           | 作用                                           | 导线颜色 |
| ------------------ | ---------------------------------------------- | -------- |
| VCC/5V             | 正极                                           | 红色     |
| D- / -3.3V         | 数据负                                         | 白色     |
| D+ / +3.3V         | 数据正                                         | 绿色     |
| ID（仅限 USB OTG） | A型：与地相连；B型：不接地(空)；用来判断主从； |          |
| GND                | 接地                                           | 黑色     |

USB Type-A同时有一个 9 线的版本。

| 符号       | 符号名称               | 功能     |
| ---------- | ---------------------- | -------- |
| VBUS       | 电源                   | 提供电源 |
| D-         | 数据传输端负           | 传输数据 |
| D+         | 数据传输端正           | 传输数据 |
| GND        | 地线                   |          |
| STDA_SSRX- | 超高速接收机差分对RX-  | 传输数据 |
| STDA_SSRX+ | 超高速接收机差分对RX+  | 传输数据 |
| GND_DRAIN  | 信号地                 |          |
| STDA_SSTX- | 超高速发射机差分对 TX- | 传输数据 |
| STDA_SSTX+ | 超高速发射机差分对 TX+ | 传输数据 |

### USB Type-B

使用较少，接口为方形，分为 4 线和 5 线版本。其引脚与USB Type-A只是排列上不同。

### Mini USB

USB Type-A和USB Type-B的缩小化。其引脚与USB Type-A只是排列上不同。

### Micro USB

安卓手机过去常用接口。其引脚与USB Type-A只是排列上不同。

### USB Type-C

插座引出引脚分布：

| 引脚 | 信号名称 | 功能描述                       | 引脚 | 信号名称 | 功能描述                       |
| ---- | -------- | ------------------------------ | ---- | -------- | ------------------------------ |
| A1   | GND      | 地线                           | B12  | GND      | 地线                           |
| A2   | SSTXp1   | SuperSpeed发送差分对+ (Lane 1) | B11  | SSTXp2   | SuperSpeed发送差分对+ (Lane 2) |
| A3   | SSTXn1   | SuperSpeed发送差分对- (Lane 1) | B10  | SSTXn2   | SuperSpeed发送差分对- (Lane 2) |
| A4   | VBUS     | 电源总线                       | B9   | VBUS     | 电源总线                       |
| A5   | CC1      | 配置通道1 (检测插入方向)       | B8   | CC2      | 配置通道2 (检测插入方向)       |
| A6   | Dp1      | USB 2.0差分对+ (Lane 1)        | B7   | Dp2      | USB 2.0差分对+ (Lane 2)        |
| A7   | Dn1      | USB 2.0差分对- (Lane 1)        | B6   | Dn2      | USB 2.0差分对- (Lane 2)        |
| A8   | SBU1     | 边带使用1                      | B5   | SBU2     | 边带使用2                      |
| A9   | VBUS     | 电源总线                       | B4   | VBUS     | 电源总线                       |
| A10  | SSRXn1   | SuperSpeed接收差分对- (Lane 1) | B3   | SSRXn2   | SuperSpeed接收差分对- (Lane 2) |
| A11  | SSRXp1   | SuperSpeed接收差分对+ (Lane 1) | B2   | SSRXp2   | SuperSpeed接收差分对+ (Lane 2) |
| A12  | GND      | 地线                           | B1   | GND      | 地线                           |





