## 版本管理

### 创建分支 git branch

创建一个名叫 `new` 的分区。

```bash
git branch new
```



### 切换分支 git checkout

切换到 `new` 分区。

```bash
git checkout new
```

可以使用 `-b` 参数，若要切换的分支不存在，则直接创建。

```bash
git checkout -b new
```



### 合并版本 git merge （危险）

当存在多个分支，可以使用 `git merge` 合并这些分支。

这个指令存在不安全性，因此大企业**禁用**。

```bash
git merge 分支名
```

会将参数中的分支合并到当前分支里。



### 移动提交 git rebase

会把当前分支合并到参数分支后面，所以一般是把分支开发合并到main分支。

```bash
git rebase main
```



```bash
git rebase -i <提交>
git rebase -i HEAD~3        # 交互式重写最近3个提交
git rebase -i --root        # 从第一个提交开始rebase
```



如何把本地 `he` 分支合并到 `master` 分支

```bash
# 确保本地he分支干净
git status he
# 同步 origin 这个远程仓库的的所有最新分支 而且不动本地的代码
git fetch origin
# 切换到 master 并更新到最新
git checkout master
# 将我当前所在的本地分支，强制重置到和远程 origin/master 分支一模一样的状态，并丢弃所有未提交的更改
git reset --hard origin/master
# 回到 he 并变基到 master
git checkout he
git rebase master
# 将 master 移动到 he（不创建合并提交）
git checkout master
# 把你的当前分支和所有文件强制变成和 he 这个引用一模一样的状态，并永久丢弃所有未提交的修改。
git reset --hard he
# 推送到远程 master
git push origin master
```





### 合并多个版本 git cherry-pick

可以将 `commit1` `commit3` `commit5` 都合并到当前分支下。

```bash
git cherry-pick commit1 commit3 commit5
```



## 整理提交

```bash
git rebase -i HEAD~5
```

