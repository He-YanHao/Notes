# TWAI

## CAN

ESP32没有CAN设备，取而代之的是与CAN完全兼容的TWAI(双线汽车接口)。



## 创建流程

初始化硬件

```c
#include "esp_twai.h"
#include "esp_twai_onchip.h"

twai_node_handle_t node_hdl = NULL;
twai_onchip_node_config_t node_config = {
    .io_cfg.tx = 4,                // twai tx GPIO
    .io_cfg.rx = 5,                // twai rx GPIO
    .bit_timing.bitrate = 200000,  // 200k 波特率
    .tx_queue_depth = 5,           // 发送队列深度为5
};
// 创建 TWAI 控制器驱动实例
ESP_ERROR_CHECK(twai_new_node_onchip(&node_config, &node_hdl));
// 启动 TWAI 控制器
ESP_ERROR_CHECK(twai_node_enable(node_hdl));
```

函数 `twai_node_enable()` 将启动 TWAI 控制器，此时 TWAI 控制器就连接到了总线，可以向总线发送报文。如果收到了总线上其他节点发送的报文，或者检测到了总线错误，也将产生相应事件。

与之对应的函数是 `twai_node_disable()` ，该函数将立即停止节点工作并与总线断开，正在进行的传输将被中止。当下次重新启动时，如果发送队列中有未完成的任务，驱动将立即发起新的传输。



为减少拷贝带来的性能损失，TWAI 驱动使用指针进行传递。以下代码展示了如何发送一条典型的数据帧报文：

```c
uint8_t send_buff[8] = {0};
twai_frame_t tx_msg = {
    .header.id = 0x1,       // 报文ID
    .header.ide = true,     // 29 位扩展ID格式
    .buffer = send_buff,    // 发送数据的地址
    .buffer_len = sizeof(send_buff),    // 发送数据的长度
};
ESP_ERROR_CHECK(twai_node_transmit(node_hdl, &tx_msg, 0));  // 超时为0，队列满则直接返回超时
```

其中 `twai_frame_t::header::id` 指示了该文的 ID 为 0x01。报文的 ID 通常用于表示报文在应用中的类型，并在发送过程中起到总线竞争仲裁的作用，其数值越小，在总线上的优先级越高。

`twai_frame_t::buffer` 则指向要发送数据所在的内存地址，并由 `twai_frame_t::buffer_len` 给出数据长度。

需要注意的是 `twai_frame_t::header::dlc` 同样可以指定一个数据帧中数据的长度，dlc(data length code) 与具体长度的对应兼容 ISO11898-1 规定。可使用  `twaifd_dlc2len()` 进行转换，选择其一即可，如果 dlc 和 buffer_len 都不为 0 ，那他们所代表的长度必须一致。



接收报文必须在接收事件回调中进行，因此，要接收报文需要在控制器启动前注册接收事件回调 [`twai_event_callbacks_t::on_rx_done`](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32c3/api-reference/peripherals/twai.html#_CPPv4N22twai_event_callbacks_t10on_rx_doneE) ，从而在事件发生时接收报文。以下代码分别展示了如何注册接收事件回调，以及如何在回调中接收报文：

注册接收事件回调（在控制器启动前）：

```
twai_event_callbacks_t user_cbs = {
    .on_rx_done = twai_rx_cb,
};
ESP_ERROR_CHECK(twai_node_register_event_callbacks(node_hdl, &user_cbs, NULL));
```



在事件中接收报文：

```
static bool twai_rx_cb(twai_node_handle_t handle, const twai_rx_done_event_data_t *edata, void *user_ctx)
{
    uint8_t recv_buff[8];
    twai_frame_t rx_frame = {
        .buffer = recv_buff,
        .buffer_len = sizeof(recv_buff),
    };
    if (ESP_OK == twai_node_receive_from_isr(handle, &rx_frame)) {
        // receive ok, do something here
    }
    return false;
}
```



同样，驱动使用指针进行传递，因此需要在接收前配置 [`twai_frame_t::buffer`](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32c3/api-reference/peripherals/twai.html#_CPPv4N12twai_frame_t6bufferE) 的指针及其内存长度 [`twai_frame_t::buffer_len`](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32c3/api-reference/peripherals/twai.html#_CPPv4N12twai_frame_t10buffer_lenE)



