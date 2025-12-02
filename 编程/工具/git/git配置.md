# git配置

## 配置信息 \首次使用需配置

配置信息使用 `git config` 指令，其拥有三个优先级

- --loca：表示使用本地配置文件，仅对当前的git仓库有效。

- --global：表示使用全局配置文件，对所有的git仓库有效。

- --system：表示使用系统级配置文件，对所有的用户有效。

    > 三者不能同时出现。



## 配置信息

**配置基本信息**

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



## 查看信息

```markdown
# 列出所有配置（包括所有级别）
git config --list
# 列出全局配置
git config --global --list
# 查看特定配置项的值
git config user.name
```



## 保存信息

```
git config --global credential.helper store
```

将本地永久保存Git凭证。

```
git config --global credential.helper cache --timeout=3600
```

将本地缓存区保存1小时Git凭证。

查看已配置的信息

```
git config --global --list
```

会被保存到

```
~/.gitconfig
```

目录下。



## .gitignore

忽略版本控制的文件。



## 默认主分支名称

Git 2.28之前默认主分支名称 `master` ，之后为 `main` 。

GitHub 2020 年 10 月 1 日之前默认主分支名称 `master` ，之后为 `main` 。



## 更改默认分支名称

```
git branch -m master main
```


