# LVGL 多线程安全简要指南

## 核心原则
- 所有 LVGL GUI 操作**只能在唯一的 LVGL 任务线程内执行**，如果使用多核单片机，需要使用RTOS将其固定到特定的核心。
- 外部线程绝不可直接调用 `lv_obj_set_xxx()` 等 API
- 两条安全路径：**`lv_async_call`**（推荐）或**持锁调用**

## lv_timer_handler() 的独占性与连续性

- **仅允许一个固定线程** 调用 `lv_timer_handler()`，称为“LVGL 任务”。
- 即使通过锁串行化，也**不允许两个线程交替**调用该函数。其内部动画状态机、输入事件处理、脏区域追踪严重依赖调用线程的连续性，交替调用将导致画面闪烁、动画撕裂，甚至触发断言。
- 在 v9 中，若用户违反此规则，甚至可能因内部线程 ID 检查而直接崩溃。



## lv_async_call

```c
// 回调在 LVGL 任务内执行，可直接操作控件
void my_callback(void *user_data) {
    char *text = (char *)user_data;
    lv_label_set_text(my_label, text);
    free(text);
}

// 外部线程调用，立即返回，非阻塞
void external_thread_func(void) {
    char *data = strdup("Hello");
    lv_async_call(my_callback, data);
}
```

**要点**：

- 调用方无需加锁，不阻塞
- 更新不会丢失（除非内存极度紧张）
- 回调内禁止耗时操作、禁止再次调用 `lv_async_call` 形成依赖链
- **绝对禁止在中断服务程序（ISR）中使用**



## 持锁访问

**适用场景**：极高频率、可容忍偶尔丢失的更新

```c
// 外部线程带超时获取锁
void external_thread_update(void) {
    if (lv_lock()) {                        // v9
    // if (xSemaphoreTake(mutex, timeout))  // v8
        lv_obj_set_x(obj, 10);
        lv_unlock();
    } else {
        // 超时，丢弃本次更新
    }
}
```

**要点**：

- 必须使用**有限超时**，超时即丢弃，绝不死等
- 外部线程更新可能因锁竞争而丢失



## ISR 约束

**中断中禁止调用任何 LVGL API（含 `lv_async_call`）**

## 常见错误速查

| 错误                         | 正确                          |
| ---------------------------- | ----------------------------- |
| 外部线程直接调 LVGL API 无锁 | 用 `lv_async_call` 或持锁调用 |
| 回调内调自制加锁函数         | 回调直接调原生 API            |
| ISR 中调 `lv_obj_set_pos`    | ISR 发信号，任务内更新        |
| 持锁期间做文件/网络 I/O      | 数据预处理完再极短持锁        |
