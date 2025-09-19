# FOC开环速度代码

## 基础流程

1. 确定 `U_q` 。

2. 令 `U_d` 为 0 。

3. 电机位置归零

4. 通过帕克逆变换将 `U_q` 和 `U_d` 转化为 `U_α` 和 `U_β` 。

5. 通过克拉克逆变换将 `U_α` 和 `U_β` 转化为 `U_a` 和 `U_b` 以及 `U_c` 。

6. 这样得到的 `U_a` 和 `U_b` 以及 `U_c` 是包含负数部分的值，一般来说没法输出负电压，所以需要进行转化。

    `U_d` 为 0 的情况下， `U_a` 和 `U_b` 以及 `U_c` 单个的绝对值的最大值等于 `U_q` ，因此 `U_a` +  `U_q` 的结果就是将 `U_a` 最小值向上平移到X轴上时的大小。

7. 同时需要对 `U_a` 和 `U_b` 以及 `U_c` 进行限制，避免过高烧毁电机。

8. 将 `U_a` 和 `U_b` 以及 `U_c` 通过转化，除于最大驱动电压，得到PWM占空比 `DC_a ` `DC_b ` `DC_c `。

9. 传递给 PWM 输出。

## 归零

供电电压为 $U_{max}$，则$U_{q_{\mathrm{max}}}$可表达为：
$$
U_{q_{\mathrm{max}}} = \frac{U_{max}}{2}
$$
$U_{q_{\mathrm{max}}}$代表系统允许的**最大q轴电压指令绝对值**。这个限制通常来自于电源总线电压、电机或逆变器的最大电流限制等因素。它是控制器输出的“力量”上限。



转子角度估算误差的最大范围为$\pm x^\circ$。

例如，$$\pm 5^\circ$$ 意味着希望观测器将角度误差维持在以真实角度为中心的 ±5° 的窗口内。这个值是一个**设计参数**，由工程师根据系统性能要求（如稳定性、转矩平滑度）来设定。较小的 `x` 意味着对角度估算的精度的要求更高。



由此可以计算出：
$$
K_p = \frac{|U_{qmax}|}{|\pm x^\circ|}
$$
系数$K_p$

$K_p$为无传感器FOC控制器中角度观测器（或锁相环PLL）的比例增益（P增益）。

它的核心作用是：**将电压指令的极限值映射到角度估算的允许误差范围上，从而确定控制器的响应强度。**

$K_p$的作用是：

- 这是比例控制器的增益。`K_p` 的值直接决定了观测器如何根据估算误差来修正其估算值。
- **`K_p` 越大**，观测器对误差的反应就越“猛烈”和快速，试图更快地将误差纠正为零。但如果过大，会导致系统震荡或不稳定。
- **`K_p` 越小**，观测器的反应就越“温和”和缓慢。如果过小，会导致系统响应迟钝，无法及时跟踪真实的转子角度。



### 为什么这样计算？

这个公式体现了一个经典的比例控制思想：

**“多大的误差（角度差），应该动用多大的修正力量（电压）。”**

1. **定义“满力”**：$U_{q_{\mathrm{max}}}$ 就是能使出的“满力”。
2. **定义“最大允许误差”**：$\pm x^\circ$ 就是定义的“需要使出满力来纠正的最大偏差”。
3. **计算“增益”**：`K_p` 就是这个比例系数。
    - `K_p = 最大输出力 / 最大允许误差`

**举个例子：**
假设系统最大允许输出 $U_{q_{\mathrm{max}}} = 10V$，并且要求角度误差不能超过 $\pm 5°$。

根据公式：
$$
K_p = \frac{10V}{|\pm 5^\circ|} = 2{V/^\circ}
$$
这意味着：

- 当观测器检测到 `1°` 的估算误差时，它会产生一个 `2V` 的修正电压指令 (`U_q`)。
- 当误差达到最大的 `5°` 时，修正指令恰好达到极限值 `10V`。
- 如果误差超过 `5°`，由于电压已经被限幅，修正能力不会再增加。



$K_p$的计算公式是无传感器FOC算法中调试角度观测器的一个关键设计准则。

它通过一个简单的比例关系，将系统的**物理限制**（最大电压）和**性能要求**（最大允许角度误差）结合起来，计算出观测器的**比例增益（Kp）**，从而确保观测器既能有足够快的动态响应来跟踪转子，又能在稳态时保持稳定和精确。在实际应用中，通常还会有一个积分增益（Ki），与Kp配合使用（构成PI控制器），以消除稳态误差。



## 函数案例

### 结构体定义

```c
typedef struct{     //定义FOC所需数据的结构体
    double U_a;     //三相电压A
    double U_b;     //三相电压B
    double U_c;     //三相电压C
    double U_alpha; //坐标系α
    double U_beta;  //坐标系β
    double U_d;     //坐标系d
    double U_q;     //坐标系q
    double theta;   //角度
    double DC_a;    //A相PWM占空比
    double DC_b;    //B相PWM占空比
    double DC_c;    //C相PWM占空比
}FOC_NUM;
```



### 初始变量定义

```c
FOC_NUM My_FOC = {0};       //申请结构体

float DRIVE_MAX = 12;      //定义最大供电电压
float ZeroElectricAngle = 0;       //零电角度
float ShaftAngle = 0;              //轴角度
```



### 初始化

```c
//根据平台不同而不同
/******************************/
//初始化PWM
//初始化
******************************/
```



### 电角度求解

```c
/*电磁角度 = 机械角度x极对数*/
float _electricalAngle(float ShaftAngle, int pole_pairs) {
  return (ShaftAngle * pole_pairs);
}
```



### 转化函数

```c
void NOT_Ud_Change(double U_q, double theta)//反变化
{
    MyFOC_NUM.U_alpha = - U_q * sin(theta);
    MyFOC_NUM.U_beta = U_q * cos(theta);
    MyFOC_NUM.U_a = MyFOC_NUM.U_alpha;
    MyFOC_NUM.U_b = (sqrt(3)*MyFOC_NUM.U_beta - MyFOC_NUM.U_alpha)/2;
    MyFOC_NUM.U_c = (- MyFOC_NUM.U_alpha - (sqrt(3))*MyFOC_NUM.U_beta)/2;
    MyFOC_NUM.DC_a = constrain((MyFOC_NUM.U_a+MyFOC_NUM.U_q)/DRIVE_MAX, 0, 1);
    MyFOC_NUM.DC_b = constrain((MyFOC_NUM.U_b+MyFOC_NUM.U_q)/DRIVE_MAX, 0, 1);
    MyFOC_NUM.DC_c = constrain((MyFOC_NUM.U_c+MyFOC_NUM.U_q)/DRIVE_MAX, 0, 1);
}
```



