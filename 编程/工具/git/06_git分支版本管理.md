## 版本管理

### 创建分支 git branch

创建一个名叫 `new-feature` 的分区。

```bash
git branch new-feature
```

还可以使用

```bash
git branch -f 分支 HEAD~3
```

`-f` 强制选项（force）

强制将 `分支` 指向的提交位置。

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



