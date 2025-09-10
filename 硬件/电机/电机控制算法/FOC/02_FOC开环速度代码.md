# FOC开环速度代码

## 2

帕克逆变换和克拉克逆变换，我将把它直接写在一个代码函数里，这个代码函数我命名为`setPhaseVoltage`，`setPhaseVoltage`会计算出电机控制需求的ua,ub,ucua,ub,uc，并通过`setPWM`函数传递给电机驱动器硬件，接着，硬件就会产生对应的三相电压波形进行电机的控制。
  除了`setPhaseVoltage`函数之外，我们还需要一个`velocityOpenloop`函数，这个函数主要是根据用户输入的目标电机转速`target_velocity`计算出在这个速度下的UqUq值和电角度值，进而传递给`setPhaseVoltage`函数进行下一步的计算。