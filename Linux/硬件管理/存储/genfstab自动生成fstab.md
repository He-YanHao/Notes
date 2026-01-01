

使用 `genfstab` 可以自动生成。

但 `genfstab` 不是独立的包，需要安装：

```bash
sudo pacman -S arch-install-scripts
```

验证安装：

```bash
# 检查genfstab是否可用
which genfstab
genfstab --help
```

使用即可。

```bash
sudo genfstab -U / >> /etc/fstab
```

