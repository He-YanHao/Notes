# lv_async_call

`lv_async_call` 是 LVGL 多线程安全的一个利器，它其他线程将一个任务投递到 LVGL 的主任务中去执行，从而解决多任务环境下的线程安全问题。

与 `lv_async_call` 配套的还有一个 `lv_async_call_cancel` 函数，用于在回调函数被执行前取消已经投递的异步任务。

## 函数原型

```c
lv_result_t lv_async_call(lv_async_cb_t async_xcb, void * user_data);
lv_result_t lv_async_call_cancel(lv_async_cb_t async_xcb, void * user_data);
```

-   async_xcb 是要执行的回调函数指针，类型为 `typedef void (*lv_async_cb_t)(void *);`
-   user_data 是传递给回调函数的自定义数据指针。
-   两个函数的返回值均为成功返回 LV_RES_OK，失败返回 LV_RES_INV。



## lv_async_call 的工作机制

当你调用 lv_async_call 时，系统并不会立即执行回调函数，而是将回调函数指针和 user_data 封装成一个任务，放入一个内部队列中。LVGL 的核心循环，即不断调用的 lv_timer_handler 函数，每次运行时都会检查这个内部队列。如果队列中有待处理的任务，lv_timer_handler 会按顺序取出它们，并在主循环的上下文中依次执行这些回调函数。这个过程遵循先进先出的原则，但具体执行时机取决于 lv_timer_handler 的调用频率。



## lv_async_call_cancel 的作用与用法

lv_async_call_cancel 用于在回调函数尚未被执行时，将其从内部队列中移除。它需要传入与调用 lv_async_call 时完全相同的回调函数指针和 user_data 指针，系统会根据这两个参数在队列中查找匹配的任务并删除。

该函数的主要用途包括：

-   防止内存泄漏：如果动态分配了 user_data 内存，但相关的 UI 操作已不再需要，可以先取消任务再释放内存。
-   避免无效操作：例如用户快速连续触发了多个更新，但只有最后一次有效，可以取消之前排队的任务。
-   处理对象销毁：如果目标 UI 对象已被删除，执行回调可能导致访问无效指针，此时应取消相关任务。

关于回调函数的返回值：

-   lv_async_call 的回调函数有一个返回值，类型为 lv_result_t。如果回调返回 LV_RES_OK，该任务会被正常移除。如果返回 LV_RES_INV，则任务会重新排队等待下次执行。这在需要重试操作时非常有用，但需要注意防止无限循环。

关键注意事项：

-   user_data 的生命周期管理是使用这两个函数时最重要的原则。lv_async_call 只是传递了指针，它不会复制也不会释放 user_data 指向的内存。开发者必须自己确保回调执行时该内存地址仍然有效。如果动态分配了内存，通常应在回调函数内部负责释放。绝对不要传入栈空间的局部变量，因为当回调执行时，该栈帧可能早已被销毁。

## 注意

`lv_async_call` 本身并不是线程安全的。如果在非 LVGL 任务中调用它，而该任务与运行 `lv_timer_handler` 的任务之间没有加锁，可能会导致内部数据竞争。在多任务系统中，建议在调用 `lv_async_call` 时使用互斥锁进行保护。

`lv_async_call` 没有免除加锁的责任，它的核心价值在于**改变执行上下文**。它像一个“任务投递员”，允许其他线程能安全地将 UI 操作“投递”到 LVGL 的主线程去执行，从而遵循 LVGL 的单线程模型，避免直接在其它线程操作 UI 带来的风险



## 完整示例代码

以下示例展示了如何在中断服务函数中安全地更新标签文本，并在对象被删除前取消未执行的任务。

```c
// 定义回调函数，负责更新 UI 并释放内存
void my_async_callback(void * user_data) {
    char * text = (char *)user_data;
    lv_label_set_text(my_label, text);
    free(text);
}
```

```c
// 在其他任务中调用
void some_interrupt_handler(void) {
    char * msg = strdup("Hello from interrupt!");
    if (lv_async_call(my_async_callback, msg) != LV_RES_OK) {
        free(msg);
    }
}
```

```c
// 在删除 UI 对象前取消未执行的任务
void delete_ui_object(void) {
    if (lv_async_call_cancel(my_async_callback, last_msg) == LV_RES_OK) {
        free(last_msg);
    }
    lv_obj_del(my_label);
}
```
