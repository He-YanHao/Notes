# LVGL 多线程安全简要指南

## 核心原则
- 所有 LVGL GUI 操作**只能在唯一的 LVGL 任务线程内执行**
- 外部线程绝不可直接调用 `lv_obj_set_xxx()` 等 API
- 两条安全路径：**`lv_async_call`**（推荐）或**持锁调用**

---

## 安全方法一：`lv_async_call`（推荐）

**适用场景**：网络回调、传感器数据、不可丢失的 UI 更新

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

---

## 安全方法二：持锁访问（次选）

**适用场景**：极高频率、可容忍偶尔丢失的更新

```c
// 外部线程带超时获取锁
void external_thread_update(void) {
    if (lv_lock()) {                       // v9
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

---

## 执行上下文：哪些地方已持有锁

以下回调**天然运行在 LVGL 任务内**，已受锁保护：

- 控件事件回调（`lv_obj_add_event_cb`）
- LVGL 定时器回调（`lv_timer_create`）
- `lv_async_call` 的回调

**关键禁忌**：在这些回调内直接使用 `lv_obj_set_xxx()` 等原生 API 即可，**严禁调用任何自制的加锁封装函数**（如 `safe_lv_label_set_text`），否则使用普通锁时必定死锁。

---

## ISR 约束

**中断中禁止调用任何 LVGL API（含 `lv_async_call`）**

唯一合法操作：通过信号量/任务通知唤醒 LVGL 任务，由其完成 UI 更新。

---

## 常见错误速查

| 错误                         | 正确                          |
| ---------------------------- | ----------------------------- |
| 外部线程直接调 LVGL API 无锁 | 用 `lv_async_call` 或持锁调用 |
| 回调内调自制加锁函数         | 回调直接调原生 API            |
| ISR 中调 `lv_obj_set_pos`    | ISR 发信号，任务内更新        |
| 持锁期间做文件/网络 I/O      | 数据预处理完再极短持锁        |

---

## 优先级建议

LVGL 任务优先级**高于所有向它提交 UI 更新的外部线程**，**低于**电机控制等关键实时任务。