# LVGL时基

LVGL 只假设一件事：

* 有一个“当前时间（毫秒）”在单调递增



LVGL时基核心是这两个函数：

-   ```c
    void lv_tick_inc(uint32_t ms);
    ```

-   ```c
    uint32_t LV_ATTRIBUTE_TIMER_HANDLER lv_timer_handler(void);
    ```

其中 `lv_tick_inc()` 函数用于通知LVGL组件时间过去了多少毫秒。

`lv_timer_handler()` 函数用于通知LVGL组件可以开始处理该处理的事件了。



如：

```c
lv_tick_inc(1);
```

时间过去了 1 毫秒。

```c
lv_timer_handler();
```

开始顺序做这些事（简化版）：

1.  处理 `lv_timer` 链表
2.  推进动画
3.  处理输入设备
4.  判断是否需要重绘
5.  触发重绘（调用 flush）
6.  清理无效对象

