# git历史版本管理

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



## HEAD

### 移动 HEAD 引用

**HEAD** 是 Git 中一个特殊的指针，它指向**当前所在的提交记录**，表示工作目录的当前状态。

```bash
git checkout HEAD^     # 移动到父提交
git checkout HEAD^^    # 移动到祖父提交
git checkout HEAD~1    # 等同于 HEAD^
```

