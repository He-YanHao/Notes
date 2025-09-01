# 双系统时间同步

## UTC时间

- **Linux (以及 macOS)** 默认将**硬件时间（RTC）视为 UTC 时间（协调世界时）**。系统启动时，它会从硬件时间（UTC）读取，然后根据你设置的时区，换算成你的本地时间（Local Time）来显示。
- **Windows** 默认将**硬件时间（RTC）直接视为本地时间（Local Time）**。它不会进行时区换算，认为硬件上显示的时间就是当前应该显示的时间。

所以出问题了，可以修改Windows注册表或者在Linux终端修改。



**在 Linux 终端中执行以下命令：**

```shell
timedatectl set-local-rtc 1 --adjust-system-clock
```

- `set-local-rtc 1`：告诉 Linux 硬件时钟是本地时间。
- `--adjust-system-clock`：这个选项会立即根据当前设置来校正系统时钟和硬件时钟。

**验证设置**

- 运行

    ```shell
    timedatectl
    ```

    查看输出。你会看到类似这样的一行：

- ```
    RTC in local TZ: yes
    ```

    这表示设置已成功。

