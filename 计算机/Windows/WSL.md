# WSL

下载：

在管理员模式下打开 PowerShell，输入

```
wsl --install
```

此命令将启用运行 WSL 并安装 Linux 的 Ubuntu 分发所需的功能。



使用：

```
wsl --list --online
```

查看可通过在线商店下载的可用 Linux 分发版列表。



替换使用默认 Linux 分发版：

```
wsl.exe --set-default <Distro>
```



```
sudo apt update && sudo apt install -y libgtk-3-0t64 libnss3 libasound2t64
```

