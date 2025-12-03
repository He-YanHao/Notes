# git配置

git首次使用配置信息。

## 优先级

配置信息使用 `git config` 指令，其拥有三个优先级

- --loca：表示使用本地配置文件，仅对当前的git仓库有效。

- --global：表示使用全局配置文件，对所有的git仓库有效。

- --system：表示使用系统级配置文件，对所有的用户有效。

    > 三者不能同时出现。



## 配置信息

### 配置基本信息

使用

```
git config --global user.name "名字"
git config --global user.email 邮箱
```

配置用户基本信息。

> 对于有空格的参数加双引号。

**配置编辑器信息**


```markdown
# 设置 VS Code 为默认编辑器
git config --global core.editor "code --wait"

# 设置 Vim 为默认编辑器
git config --global core.editor "vim"

# 设置 nano 为默认编辑器
git config --global core.editor "nano"
```

**配置换行信息**

```markdown
# Windows 推荐设置（检出时转 CRLF，提交时转 LF）
git config --global core.autocrlf true

# Linux/macOS 推荐设置（提交时转 LF）
git config --global core.autocrlf input

# 完全禁用转换
git config --global core.autocrlf false
```



### 查看信息

```markdown
# 列出所有配置（包括所有级别）
git config --list
# 列出全局配置
git config --global --list
# 查看特定配置项的值
git config user.name
```



### 保存信息

```bash
git config --global credential.helper store
```

将本地永久保存Git凭证。

```bash
git config --global credential.helper cache --timeout=3600
```

将本地缓存区保存1小时Git凭证。

查看已配置的信息

```bash
git config --global --list
```

会被保存到

```bash
~/.gitconfig
```

目录下。



## .gitignore

忽略版本控制的文件。



## 主分支名称

### 默认主分支名称

Git 2.28之前默认主分支名称 `master` ，之后为 `main` 。

GitHub 2020 年 10 月 1 日之前默认主分支名称 `master` ，之后为 `main` 。



### 更改默认分支名称

```bash
git branch -m master main
```



## 编码

有时候会有：

```bash
"Linux/02_\347\263\273\347\273\237\347\232\204\345\256\211\350\243\205\344\270\216\351\205\215\347\275\256/\345\256\211\350\243\205/Arch\347\263\273\347\273\237\345\256\211\350\243\205.md"
```

这样的编码错误，可以配置 Git 使用 UTF-8 编码，以确保它能够正确处理所有文件名。

执行以下命令来修改 Git 配置：

```bash
git config --global core.quotepath false
git config --global i18n.commitEncoding utf-8
git config --global i18n.logOutputEncoding utf-8
```

这些配置会确保：

-   `core.quotepath false`：Git 在显示文件名时不会进行编码转义，直接显示文件名。
-   `i18n.commitEncoding utf-8`：提交时使用 UTF-8 编码。
-   `i18n.logOutputEncoding utf-8`：日志输出使用 UTF-8 编码。

这样乱码就会被修正为：

```
Linux/02_系统的安装与配置/安装/Arch系统安装.md
```



## 权限

git 是多平台通用的，但 Linux 有着严格的权限管理，Windows 相对宽松。

因此，Windows 下的仓库在 Linux 环境中可能会出现

```bash
he@he-books:~/Windows/D/Documents/Notes$ git status 
fatal: detected dubious ownership in repository at '/home/he/Windows/D/Documents/Notes'
To add an exception for this directory, call:
        git config --global --add safe.directory /home/he/Windows/D/Documents/Notes
```

可以按照推荐的方式将此目录设立安全目录，忽略权限检查：

```bash
git config --global --add safe.directory /home/he/Windows/D/Documents/Notes
```

也可以直接禁用权限检查：

```bash
git config --global --add safe.directory '*'
```

但是不推荐。

