# LVGL 定时器 Timer

LVGL定时器是一个基于毫秒时基的软定时器系统，由 `lv_timer_handler()` 驱动，不依赖硬件中断。

## 一、核心特性

| 特性       | 说明                                           |
| ---------- | ---------------------------------------------- |
| 软定时器   | 基于软件实现，不占用硬件定时器资源             |
| 单线程驱动 | 所有定时器由 `lv_timer_handler()` 统一调度     |
| 线程安全   | 定时器回调与LVGL主循环在同一线程执行，无需加锁 |
| 周期可设   | 支持单次或周期性执行                           |

## 二、与其他定时器的区别

| 类型           | 执行环境           | 线程安全性               | 使用场景         |
| -------------- | ------------------ | ------------------------ | ---------------- |
| LVGL定时器     | LVGL主线程         | 安全，可直接调用LVGL API | UI更新、动画控制 |
| FreeRTOS定时器 | RTOS定时器服务任务 | 不安全，需加锁           | 系统级延时任务   |
| 硬件定时器     | 中断上下文         | 不安全，禁止LVGL API     | 精确硬件时序     |

LVGL定时器最大的优势是线程安全。因为它由 `lv_timer_handler()` 驱动，回调始终与LVGL操作在同一线程执行，无需担心多线程竞争问题。

## 三、API函数

### 创建定时器

```c
lv_timer_t * lv_timer_create(timer_cb, period_ms, user_data);
```

参数说明：
- timer_cb：定时器回调函数
- period_ms：周期时间（毫秒），0表示只执行一次
- user_data：传递给回调的用户数据

### 控制定时器

| 函数                | 作用         |
| ------------------- | ------------ |
| lv_timer_del        | 删除定时器   |
| lv_timer_pause      | 暂停定时器   |
| lv_timer_resume     | 恢复定时器   |
| lv_timer_set_period | 修改周期     |
| lv_timer_ready      | 立即触发一次 |

### 状态查询

| 函数                      | 作用             |
| ------------------------- | ---------------- |
| lv_timer_get_paused       | 检查是否暂停     |
| lv_timer_get_repeat_count | 获取剩余重复次数 |

## 四、回调函数格式

```c
void timer_cb(lv_timer_t * timer)
{
    void * user_data = timer->user_data;
    // 执行定时任务
    // 可直接调用任何LVGL API，线程安全
}
```

## 五、示例

### 周期性定时器

```c
static void refresh_cb(lv_timer_t * timer)
{
    static int counter = 0;
    counter++;
    lv_label_set_text_fmt(label, "Count: %d", counter);
}

void init_timer(void)
{
    lv_timer_t * timer = lv_timer_create(refresh_cb, 1000, NULL);
}
```

### 单次定时器（延时执行）

```c
static void delay_cb(lv_timer_t * timer)
{
    lv_obj_t * obj = (lv_obj_t *)timer->user_data;
    lv_obj_fade_in(obj, 300, 0);
    lv_timer_del(timer);  // 删除自己
}

void show_with_delay(lv_obj_t * obj)
{
    lv_timer_create(delay_cb, 500, obj);
}
```

## 六、使用要点

| 要点               | 说明                               |
| ------------------ | ---------------------------------- |
| 回调函数保持简短   | 避免阻塞其他定时器和UI刷新         |
| 可安全调用LVGL API | 与lv_timer_handler同一线程         |
| 定时器不是实时中断 | 精度取决于lv_timer_handler调用频率 |
| 删除定时器         | 使用lv_timer_del，避免内存泄漏     |

## 七、驱动原理

LVGL定时器需要应用层持续调用 `lv_timer_handler()`：

```c
while(1) {
    lv_timer_handler();
    usleep(5000);  // 5ms
}
```

`lv_timer_handler()` 检查所有定时器，执行到期的回调。调用频率决定了定时器的最小分辨率和响应精度。通常5-10ms的调用周期即可满足大多数UI需求。

## 八、总结

LVGL定时器是一个运行在UI线程中的软定时器系统，核心优势是线程安全，可直接在回调中操作UI。理解它与其他定时器的区别，有助于在LVGL+RTOS混合系统中做出正确的选择。
