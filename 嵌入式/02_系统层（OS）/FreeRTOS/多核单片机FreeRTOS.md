在 ESP-IDF 中，使用 `xTaskCreatePinnedToCore`。  
参数 `xCoreID` 设为 `0` 或 `1` 即指定运行核心。  
伪代码：

```
xTaskCreatePinnedToCore(任务函数, 任务名, 栈深度, 参数, 优先级, 任务句柄, 核心编号)
```

若希望任务不固定，核心编号可填 `tskNO_AFFINITY`。