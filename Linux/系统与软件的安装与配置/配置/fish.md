# fish

## 下载

```shell
sudo apt install fish
```



## 更改为默认终端

### 确定安装路径

```shell
which fish
```

输出可能为：

```shell
/usr/bin/fish
```



### Fish 添加到合法 Shell 列表

使用管理员权限，将 Fish 的完整路径添加到 `/etc/shells` 文件末尾。例如：

```bash
echo "/usr/bin/fish" | sudo tee -a /etc/shells
```

- tee：它的 `-a` 参数会将标准输入追加到文件后面。



### 修改默认 Shell

使用 `chsh`（change shell）命令来修改当前用户的默认 Shell：

```shell
chsh -s $(which fish)
```

或者直接使用完整路径：

```shell
chsh -s /usr/bin/fish
```



执行后，系统会提示你输入用户密码。

**注意**：`chsh` 修改的是当前用户的登录 Shell。更改后，**需要完全注销当前会话再重新登录**，或者重启所有终端模拟器，更改才会生效。
