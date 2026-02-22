# fstab

fstab文件在 `/etc/fstab` 目录下。

## fstab文件格式

```
# UUID=???             挂载路径     文件系统驱动名称  文件的所有者 文件的所有者组  权限  挂载选项  备份  检查顺序
# <file system>     <mount point>  <type>          <options>                            <dump>  <pass>
UUID=xxxxxxx         /mnt/mydata    ext4           defaults                              0       2
```

*   **UUID=...**: 使用 `blkid` 查询 UUID。
*   **/mnt/mydata**: 挂载点目录必须已存在，不会自动创建。
*   **ext4**: 文件系统类型必须匹配。
*   **权限**：`umask=022`: 设置默认权限。对于文件是 755（rwxr-xr-x），对于文件夹是 644（rw-r--r--）。如果你想获得完全控制（文件为 755，文件夹为 755），可以使用 `umask=000`，但安全性稍低。
*   **挂载选项**：defaults：这是一个综合选项，包含了 `rw, suid, dev, exec, auto, nouser, async`。对于大多数情况，这样写就够了。
    *   `nofail`：增加`nofail`之后如果设备不存在也不阻塞启动。

*   **备份**: **`0`**: dump 备份工具是否备份此文件系统。0 表示不备份，1 表示备份。通常设为 0。
*   **检查顺序**：**`2`**: 系统启动时 `fsck` 磁盘检查的顺序。根目录 `/` 必须为 1，其他文件系统应为 2。0 表示不检查。



## 获取UUID

**获取分区的 UUID** (推荐使用UUID，比设备名更稳定)

```bash
sudo blkid /dev/sdb1
```

输出类似：`/dev/sdb1: UUID="a1b2c3d4-..." TYPE="ext4" PARTUUID="..."`



## fstab案例

```shell
#Ubuntu
UUID=edc0b12d-811a-4f7c-8726-04d98b9da753  /                           xfs       defaults   0 1
UUID=9bc2a7d5-e20f-4a13-94d9-402b3e196d78  /home                       xfs       defaults   0 2
UUID=7010048c-b612-4834-b2f0-73afdf6ad7ca  none                        swap      sw         0 0

#Arch
UUID=5f526c7e-7f43-494e-9c6f-69ae856537df  /home/hing/Arch/System      xfs       defaults   0 2
UUID=bfb0c79d-343b-4b82-87dd-41cdba13b487  /home/hing/Arch/Data        xfs       defaults   0 2

#Windows
UUID=CA9A27EB9A27D2AD                      /home/hing/Windows/System   ntfs3     uid=1000, gid=1000, umask=022 defaults   0 2
UUID=60FA2097FA206B8A                      /home/hing/Windows/Data     ntfs3     uid=1000, gid=1000, umask=022 defaults   0 2
```

