# git

## 基础概念

### 区域

分为工作区，暂存区，本地仓库。

工作区位于`.git`目录，用于实际操作。

暂存区`.git/index`目录，用于存放即将提交的代码。

本地仓库`.git/objects`目录，用于存放代码和版本控制的地方。

## 配置信息 \首次使用需配置

配置信息使用`git config`指令，其拥有三个优先级

- --loca：表示使用本地配置文件，仅对当前的git仓库有效。

- --global：表示使用全局配置文件，对所有的git仓库有效。

- --system：表示使用系统级配置文件，对所有的用户有效。

	> 三者不能同时出现。

### 配置信息

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

## git指令

### 新建仓库

#### 初始化仓库 git init 

```c
git init 路径
```

不包含路径则为当前目录。

```
git init
```

#### 克隆仓库 git clone

```
git clone ULR地址
```



### 文件管理

#### 添加文件 git add

```makefile
git add 路径
#添加指定文件
git add .
#添加全部文件
```

#### 获取状态 git status

```c
git status
```

#### 提交到本地仓库 git commit
```
git commit -m "提交说明"
```

#### 添加远程仓库 git remote

```
git remote add origin https://github.com/yourname/repo.git
```

或使用 SSH：

```
git remote add origin git@github.com:yourname/repo.git
```

验证远程仓库

```
git remote -v
```

#### 推送到远程仓库

##### 首次推送

###### 推送到远程 main 分支

```
git push -u origin main
```

###### 如果本地是 master 分支

```
git push -u origin master:main
```

| `git push`    | 将本地提交推送到远程仓库                                     |
| ------------- | ------------------------------------------------------------ |
| `-u`          | 设置上游跟踪分支（后续可直接用 `git push` 代替完整命令）     |
| `origin`      | 远程仓库的别名（默认名称）                                   |
| `master:main` | 本地分支 `master` 推送到远程分支 `main`（用冒号 `:` 分隔本地和远程分支） |

##### 后续推送

仅需运行

```
git push
```

### 其它

#### 回退版本 git reset

```c
git reset
```

#### 查看差异 git diff

```c
git diff
```

#### 删除 git rm

```c
git rm
```

## .gitignore

忽略版本控制的文件。

## 默认主分支名称

Git 2.28之前默认主分支名称 `master` ，之后为 `main` 。

GitHub 2020 年 10 月 1 日之前默认主分支名称 `master` ，之后为 `main` 。

## 更改默认分支名称

```
git branch -m master main
```











