# GPIO的控制

## GPIO4_C2的含义？

GPIO的驱动文件在 `/sys/class/gpio` ，在这里 `ls` 有：

```bash
he@lckfb /s/c/gpio> ls
export  unexport  gpiochip0@  gpiochip32@  gpiochip64@  gpiochip96@  gpiochip128@  gpiochip511@
```

`export` 用于导出 GPIO 引脚

`unexport` 用于取消导出 GPIO 引脚

GPIO(4)_(C)(2)

-   (4)：代表第四组GPIO控制器，每组GPIO控制器32个引脚。
-   (C)：A->0 B->1 C->2 D->3，每个群组包含从0~7共八个。
-   (2)：代表第二个GPIO口。

所以GPIO(4)_(C)(2)代表第 $4*32+3*8+2=154$

运行：

```bash
root@lckfb /s/c/gpio# ls
export  gpiochip0@  gpiochip128@  gpiochip32@  gpiochip511@  gpiochip64@  gpiochip96@  unexport
root@lckfb /s/c/gpio# echo 99 > export
root@lckfb /s/c/gpio# ls
# 新增 gpio99@
export  gpio99@  gpiochip0@  gpiochip128@  gpiochip32@  gpiochip511@  gpiochip64@  gpiochip96@  unexport
# 导出 GPIO3_A3
root@lckfb /s/c/gpio# cd ./gpio99/
root@lckfb /s/c/g/gpio99# ls
# active_low = 1 则GPIO输出相反
# direction = in 输入 = out 输出
# value 输出状态写入0或1设置输出电平 输入状态读取出0或1获得输入电平
active_low  device@  direction  edge  power/  subsystem@  uevent  value
root@lckfb /s/c/g/gpio99# echo "out" > ./direction
root@lckfb /s/c/g/gpio99# echo "1" > ./value
root@lckfb /s/c/g/gpio99# echo "0" > ./value
root@lckfb /s/c/g/gpio99# cd ..
# 卸载
root@lckfb /s/c/gpio# echo 99 > unexport
```

注意：对于有复用关系的GPIO不能直接这样设置。