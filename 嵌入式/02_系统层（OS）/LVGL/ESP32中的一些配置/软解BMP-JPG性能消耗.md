# 软解BMP-JPG性能消耗

测试硬件为ESP32S3，CPU主频配置为240MHz。

使用代码：

```c
int64_t t_start = esp_timer_get_time();
// 解码显示过程
int64_t t_end = esp_timer_get_time();
ESP_LOGI(TAG, "Image load & decode: %lld us", t_end - t_start);
```

确定：

- 软解240x240的BMP 需要 2616 us
- 软解240x240的JPG 需要 4139 us