# 虚拟机问题

## 安装open-vm-tools

```bash
sudo apt install open-vm-tools
sudo apt install open-vm-tools-desktop 
```



## 挂载共享文件夹

在设置共享文件夹之后还需要进行挂载。

```bash
sudo mount -t fuse.vmhgfs-fuse .host:/ /mnt/ -o allow_other
```



```bash
.host:/ /mnt/hgfs fuse.vmhgfs-fuse allow_other,defaults 0 0
```

