# git多人协作





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

