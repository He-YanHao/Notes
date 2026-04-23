# add-dependency

可以使用下面的组件依赖管理命令，作用是把第三方组件加入工程（写入 `idf_component.yml`，并在构建时自动下载）。

```
idf.py add-dependency "espressif/esp_lvgl_port^2.3.0"
idf.py add-dependency "lvgl/lvgl^8.4.*"
```

 `idf_component.yml` 位于 `./main/` 目录下，不能放到根目录。



