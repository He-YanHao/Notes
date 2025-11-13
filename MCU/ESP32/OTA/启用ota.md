# 启用ota

## 2

调出菜单

```
idf.py menuconfig
```



```
    Build type  --->
    Bootloader config  --->
    Security features  --->
    Application manager  --->
    Boot ROM Behavior  --->
    Serial flasher config  --->
    Partition Table  --->         选这个
    Example Configuration  --->
    Example Connection Configuration  --->
    Compiler options  --->
    Component config  --->
[ ] Make experimental features visible (default value)
```



```
(Top) → Partition Table
    Espressif IoT Development Framework Configuration
    Partition Table (Custom partition table CSV)  --->   选这个 默认名称
    (partitions.csv) Custom partition CSV file (default value)
    (0x8000) Offset of partition table (default value)
    [*] Generate an MD5 checksum for the partition table (default value)
```







## 快捷键



