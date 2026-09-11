```
# 1. 安装 IBus 和 Rime 引擎
sudo apt install ibus ibus-rime -y

# 2. 下载雾凇拼音配置文件
git clone --depth=1 https://github.com/iDvel/rime-ice.git /tmp/rime-ice

# 3. 复制到 Rime 的用户配置目录
mkdir -p ~/.config/ibus/rime
cp -r /tmp/rime-ice/* ~/.config/ibus/rime/

# 4. 重启 IBus（让 Rime 生效）
ibus-daemon -drx

# 5. 将系统输入法切换回 IBus（确保统一）
im-config -n ibus

```

```bash
echo 'patch:' > ~/.config/ibus/rime/default.custom.yaml
echo '  "menu/page_size": 9' >> ~/.config/ibus/rime/default.custom.yaml
rm -rf ~/.config/ibus/rime/build
ibus-daemon -drx
```





修改候选词

```
好的，Rime 的候选词数量默认是 5 个，改成 9 个只需要修改一个配置文件。我直接给你一条命令完成所有操作，不用手动编辑文件。

一条命令搞定（复制粘贴到终端执行）
fish
# 创建或覆盖 default.custom.yaml，设置候选词为 9 个
echo 'patch:' > ~/.config/ibus/rime/default.custom.yaml
echo '  "menu/page_size": 9' >> ~/.config/ibus/rime/default.custom.yaml
然后让 Rime 重新加载配置：

fish
# 删除旧编译文件并重启 IBus
rm -rf ~/.config/ibus/rime/build
ibus-daemon -drx
```

