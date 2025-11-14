# git版本管理指令

## 初始化新仓库 git init 

```bash
git init 路径
```

不包含路径则为当前目录。

```bash
git init
```



## 文件管理

### 添加文件 git add

```makefile
git add 路径
#添加指定文件
git add .
#添加全部文件
```

### 删除 git rm

```c
git rm
```



## 版本管理

### 创建分支 git branch

创建一个名叫 `new-feature` 的分区。

```bash
git branch new-feature
```



### 切换分支 git checkout

切换到 `new-feature` 分区。

```bash
git checkout new-feature
```

可以使用 `-b` 参数，创建的同时并切换分支。

```bash
git checkout -b new-feature
```



### 合并版本 git merge

当存在多个分支，可以使用 `git merge` 合并这些分支。

```bash
git merge feature
```

会将参数中的分支合并到当前分支里。



### 移动提交 git rebase

会把当前分支合并到参数分支后面，所以一般是把分支开发合并到main分支。

```bash
git rebase main
```



### 合并多个版本 git cherry-pick

可以将 `commit1` `commit3` `commit5` 都合并到当前分支下。

```bash
git cherry-pick commit1 commit3 commit5
```

### 交互式整理提交记录 git  rebase

```bash
git rebase -i <提交>
git rebase -i HEAD~3        # 交互式重写最近3个提交
git rebase -i --root        # 从第一个提交开始rebase
```



## HEAD

### 移动 HEAD 引用

**HEAD** 是 Git 中一个特殊的指针，它指向**当前所在的提交记录**，表示工作目录的当前状态。

```bash
git checkout HEAD^     # 移动到父提交
git checkout HEAD^^    # 移动到祖父提交
git checkout HEAD~1    # 等同于 HEAD^
```



## 撤回或删除提交

### revert 工作流

```bash
# 1. 查看要回退的提交
git log --oneline

# 2. 回退特定提交（创建新提交）
git revert -n abc1234      # 不自动提交
git add .                  # 检查更改后添加
git commit -m "Revert: 回退问题提交"

# 3. 推送到远程
git push origin main
```



### reset 工作流

```bash
# 1. 查看当前状态
git status
git log --oneline

# 2. 重置到之前的状态
git reset --soft HEAD~1    # 保留所有更改在暂存区

# 3. 重新提交
git commit -m "重新提交，修复问题"

# 注意：如果已推送，需要强制推送（不推荐）
git push -f origin main
```



# 1



### 获取状态 git status

```c
git status
```

### 提交到本地仓库 git commit
```
git commit -m "提交说明"
```



### 查看差异 git diff

```c
git diff
```



