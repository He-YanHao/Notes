# Linux系统GPIO口控制

## 扫描可用IO口

### 方法一：使用gpiod工具扫描
```bash
# 安装gpiod工具
sudo apt update
sudo apt install gpiod

# 扫描所有可用的GPIO芯片
gpiodetect

# 查看每个GPIO芯片的引脚状态
gpioinfo
```

### 方法二：通过sysfs查看（传统方式）
```bash
# 查看GPIO状态
cat /sys/kernel/debug/gpio

# 查看引脚复用状态（需要先安装配置工具）
sudo apt install wiringpi
gpio readall
```

### 方法三：直接查看设备树信息
```bash
# 查看GPIO控制器
ls /sys/class/gpio/

# 查看具体GPIO信息
cat /proc/device-tree/pinctrl/pinctrl@ff5f0000/gpio* 2>/dev/null | strings
```

## 选择合适的GPIO点灯

从你的IO分布图中，推荐使用以下GPIO点灯（这些引脚通常不会被系统占用）：

**推荐GPIO引脚：**
- `GPIO3_A1` (引脚11) - 通用IO，复用功能少
- `GPIO3_C4` (引脚12) - 通用IO
- `GPIO3_A7` (引脚26) - 通用IO
- `GPIO3_B5` (引脚28) - I2C3_SCL_M1，但可作为GPIO
- `GPIO3_B4` (引脚40) - I2C5_SDA_M0，但可作为GPIO

## 点灯操作步骤

### 方法一：使用sysfs（简单易用）
```bash
# 以GPIO3_A1为例，计算GPIO编号：
# GPIO3_A1 = 3组 × 32 + A(0) × 8 + 1 = 96 + 0 + 1 = 97

# 导出GPIO
echo 97 > /sys/class/gpio/export

# 设置方向为输出
echo out > /sys/class/gpio/gpio97/direction

# 点亮LED（输出高电平）
echo 1 > /sys/class/gpio/gpio97/value

# 熄灭LED（输出低电平）
echo 0 > /sys/class/gpio/gpio97/value

# 取消导出
echo 97 > /sys/class/gpio/unexport
```

### 方法二：使用gpiod工具（推荐）
```bash
# 点亮LED
gpioset gpiochip0 97=1

# 熄灭LED
gpioset gpiochip0 97=0

# 闪烁LED
while true; do
    gpioset gpiochip0 97=1
    sleep 0.5
    gpioset gpiochip0 97=0
    sleep 0.5
done
```

### 方法三：使用Python脚本
```python
#!/usr/bin/env python3
import time
import gpiod

# GPIO编号计算：GPIO3_A1 = 96 + 0*8 + 1 = 97
LED_PIN = 97

# 配置GPIO
chip = gpiod.Chip('gpiochip0')
led_line = chip.get_line(LED_PIN)
led_line.request(consumer="LED", type=gpiod.LINE_REQ_DIR_OUT)

try:
    while True:
        # 点亮
        led_line.set_value(1)
        print("LED ON")
        time.sleep(1)
        
        # 熄灭
        led_line.set_value(0)
        print("LED OFF")
        time.sleep(1)
        
except KeyboardInterrupt:
    print("\n程序退出")
finally:
    # 清理GPIO
    led_line.release()
```

### 方法四：使用C语言
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <gpiod.h>

int main() {
    const char *chipname = "gpiochip0";
    unsigned int line_num = 97; // GPIO3_A1
    struct gpiod_chip *chip;
    struct gpiod_line *line;
    int ret;
    
    // 打开GPIO芯片
    chip = gpiod_chip_open_by_name(chipname);
    if (!chip) {
        perror("打开GPIO芯片失败");
        return -1;
    }
    
    // 获取GPIO线
    line = gpiod_chip_get_line(chip, line_num);
    if (!line) {
        perror("获取GPIO线失败");
        gpiod_chip_close(chip);
        return -1;
    }
    
    // 设置为输出模式
    ret = gpiod_line_request_output(line, "LED", 0);
    if (ret < 0) {
        perror("设置输出模式失败");
        gpiod_chip_close(chip);
        return -1;
    }
    
    // 闪烁LED
    for (int i = 0; i < 10; i++) {
        gpiod_line_set_value(line, 1);
        printf("LED ON\n");
        sleep(1);
        
        gpiod_line_set_value(line, 0);
        printf("LED OFF\n");
        sleep(1);
    }
    
    // 清理
    gpiod_line_release(line);
    gpiod_chip_close(chip);
    
    return 0;
}
```

编译运行：
```bash
gcc -o led_blink led_blink.c -lgpiod
sudo ./led_blink
```

## 4. GPIO编号计算方法

对于泰山派（Rockchip芯片），GPIO编号计算公式：
```
GPIO编号 = (bank × 32) + (port × 8) + index
```

**示例计算：**
- `GPIO3_A1` = (3 × 32) + (0 × 8) + 1 = 96 + 0 + 1 = 97
- `GPIO3_C4` = (3 × 32) + (2 × 8) + 4 = 96 + 16 + 4 = 116
- `GPIO0_B6` = (0 × 32) + (1 × 8) + 6 = 0 + 8 + 6 = 14

## 5. 硬件连接建议

```
GPIO引脚 → 220Ω电阻 → LED正极 → LED负极 → GND
```

**推荐连接方式：**
- 使用引脚11 (GPIO3_A1) 控制LED
- 使用任意GND引脚（如引脚9, 14, 20等）作为接地

## 6. 注意事项

1. **权限问题**：操作GPIO需要root权限
2. **引脚复用**：确保所选GPIO没有被其他功能占用
3. **电流限制**：GPIO输出电流有限，建议串联220-1kΩ电阻
4. **电平匹配**：泰山派IO电压为3.3V，不要连接5V设备

先尝试使用GPIO3_A1（引脚11），这个引脚功能简单，适合初学者点灯实验。