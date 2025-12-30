# 版本管理

## 查看分支 git branch

```bash
# 查看本地分支
git branch

# 查看远程分支
git branch -r

# 查看所有本地和远程分支
git branch -a
```





## 创建分支 git branch

查看分支的指令也可以用来创建分支。

创建一个名叫 `new` 的分区。

```bash
git branch new
```



## 切换分支 git checkout

切换到 `new` 分区。

```bash
git checkout new
```

可以使用 `-b` 参数，若要切换的分支不存在，则直接创建。

```bash
git checkout -b new
```

### 临时保存 git stash

在切换分支时有时需要临时保存文件，使用：

```bash

```

保存，之后使用

```bash
git stash pop
```

回复。



## 合并版本 git merge （危险）

当存在多个分支，可以使用 `git merge` 合并这些分支。

这个指令存在不安全性，会使提交历史交叉，后续不易排查，因此大企业**禁用**。

```bash
git merge 分支名
```

会将参数中的分支合并到当前分支里。



## 移动提交 git rebase

会把当前分支合并到参数分支后面，所以一般是把分支开发合并到 `main` 分支。

```bash
git rebase main
```

假设原来是：

```
A4   B2
|    |
A3   B1
|    |
A2---/ --- new分支
|
A1
main分支
```

在  `new` 分支使用

```bash
git rebase main
```

会变成：

```
     B2*
     |
     B1*
     |
A4---/ --- new分支
|
A3
|
A2
|
A1
main分支
```

需要注意，这个操作不是移动提交，而是重新提交。

新的 `B1*` `B2*` 和 `B1` `B2` 的提交哈希不同。

```
he@he-books:~/esp/esp32-c3-charger$ git rebase master 
自动合并 main/main.c
冲突（内容）：合并冲突于 main/main.c
error: 不能应用 e89aef9... 测试用例写完
提示：Resolve all conflicts manually, mark them as resolved with
提示："git add/rm <conflicted_files>", then run "git rebase --continue".
提示：You can instead skip this commit: run "git rebase --skip".
提示：To abort and get back to the state before "git rebase", run "git rebase --abort".
不能应用 e89aef9... 测试用例写完
he@he-books:~/esp/esp32-c3-charger$ git checkout --theirs main/main.c
从索引区更新了 1 个路径
he@he-books:~/esp/esp32-c3-charger$ git add main/main.c
he@he-books:~/esp/esp32-c3-charger$ git rebase --continue
[分离头指针 5048fb0] 测试用例写完
 3 files changed, 81 insertions(+), 1 deletion(-)
成功变基并更新 refs/heads/he_nvs。
```





## 整理提交 git rebase

除了移动提交外，`git rebase` 还要整理提交的作用。

使用：

```bash
git rebase -i HEAD~5
```

即可交互式重写最新的5个提交，删掉对应的提交短哈希和commit说明就行。

注意：在编辑文档过程中不要保存！！！

```bash
git rebase -i <提交>
git rebase -i --root        # 从第一个提交开始rebase
```

```
1b5d0a868b41241301f007d452371e596d7fa49f
```



## 合并多个版本 git cherry-pick

可以将 `commit1` `commit3` `commit5` 都合并到当前分支下。

```bash
git cherry-pick commit1 commit3 commit5
```



