# FOC开环速度代码

## 需要



## 基础流程

1. 确定 `U_q` 
2. 令 `U_d` 为 0 。
3. 通过帕克逆变换将 `U_q` 和 `U_d` 转化为 `U_α` 和 `U_β` 。
4. 通过克拉克逆变换将 `U_α` 和 `U_β` 转化为 `U_a` 和 `U_b` 以及 `U_c` 。
5. 这样得到的 `U_a` 和 `U_b` 以及 `U_c` 是包含负数部分的值，一般来说没法输出负电压，所以需要进行转化。
6. 同时需要对 `U_a` 和 `U_b` 以及 `U_c` 进行限制，避免过高烧毁电机。
7. 将 `U_a` 和 `U_b` 以及 `U_c` 传递给 PWM 输出。
8. 
9. 
10. 









## 函数案例

### 初始变量定义

```c
//及函数
#define _constrain(amt,low,high) ((amt)<(low)?(low):((amt)>(high)?(high):(amt)))
//宏定义实现的一个约束函数,用于限制一个值不小于low，不大于high。

struct FOC_NUM{     //定义FOC所需数据的结构体
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
};
struct FOC_NUM My_FOC = {0};       //申请结构体

float VoltagePowerSupply = 12.6;   //定义供电电压
float ZeroElectricAngle = 0;       //零电角度
float ShaftAngle = 0;              //轴角度
float OpenLoopTimestamp = 0;       //开环时间戳
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
void setPhaseVoltage(float Uq,float Ud, float angle_el)
{
	angle_el = _normalizeAngle(angle_el + zero_electric_angle);
    // 帕克逆变换
    Ualpha =  -Uq*sin(angle_el); 
    Ubeta =   Uq*cos(angle_el); 
    // 克拉克逆变换
    Ua = Ualpha + VoltagePowerSupply/2;
    Ub = (sqrt(3)*Ubeta-Ualpha)/2 + VoltagePowerSupply/2;
    Uc = (-Ualpha-sqrt(3)*Ubeta)/2 + VoltagePowerSupply/2;
    setPwm(Ua,Ub,Uc);
}
```



### 函数

``````````c
// 归一化角度到 [0,2PI]
float _normalizeAngle(float angle){
  float a = fmod(angle, 2*PI);   //取余运算可以用于归一化，列出特殊值例子算便知
  return a >= 0 ? a : (a + 2*PI);  
}




//开环速度函数
float velocityOpenloop(float target_velocity){
  unsigned long now_us = micros();  //获取从开启芯片以来的微秒数，它的精度是 4 微秒。 micros() 返回的是一个无符号长整型（unsigned long）的值
  
  //计算当前每个Loop的运行时间间隔
  float Ts = (now_us - open_loop_timestamp) * 1e-6f;

  //由于 micros() 函数返回的时间戳会在大约 70 分钟之后重新开始计数，在由70分钟跳变到0时，TS会出现异常，因此需要进行修正。如果时间间隔小于等于零或大于 0.5 秒，则将其设置为一个较小的默认值，即 1e-3f
  if(Ts <= 0 || Ts > 0.5f) Ts = 1e-3f;
  

  // 通过乘以时间间隔和目标速度来计算需要转动的机械角度，存储在 ShaftAngle 变量中。在此之前，还需要对轴角度进行归一化，以确保其值在 0 到 2π 之间。
  ShaftAngle = _normalizeAngle(ShaftAngle + target_velocity*Ts);
  //以目标速度为 10 rad/s 为例，如果时间间隔是 1 秒，则在每个循环中需要增加 10 * 1 = 10 弧度的角度变化量，才能使电机转动到目标速度。
  //如果时间间隔是 0.1 秒，那么在每个循环中需要增加的角度变化量就是 10 * 0.1 = 1 弧度，才能实现相同的目标速度。因此，电机轴的转动角度取决于目标速度和时间间隔的乘积。

  // 使用早前设置的VoltagePowerSupply的1/3作为Uq值，这个值会直接影响输出力矩
  // 最大只能设置为Uq = VoltagePowerSupply/2，否则ua,ub,uc会超出供电电压限幅
  float Uq = VoltagePowerSupply/3;
  
  setPhaseVoltage(Uq,  0, _electricalAngle(ShaftAngle, 7));
  
  open_loop_timestamp = now_us;  //用于计算下一个时间间隔

  return Uq;
}


void loop() {
  // put your main code here, to run repeatedly:
   velocityOpenloop(5);
}
``````````

