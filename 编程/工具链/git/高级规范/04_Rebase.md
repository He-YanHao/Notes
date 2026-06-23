### 3.1 Rebase 的基本原则

#### ✅ 使用 Rebase 的场景

1. **个人分支同步主分支**：保持提交历史线性
2. **合并多个提交**：整理提交历史
3. **修改提交信息**：修正错误或不规范的提交
4. **重新排序提交**：优化提交顺序

#### ❌ 禁止使用 Rebase 的场景

1. **公共分支**：不要对 `main`、`develop` 等公共分支进行 rebase
2. **他人正在使用的分支**：不要 rebase 他人可能正在基于此分支工作的分支
3. **已合并到主分支的历史**：不要修改已经合并的历史

### 3.2 何时使用 Rebase

#### 场景 1：同步主分支到功能分支

```bash
# 在功能分支上
git checkout feature/user-authentication
git fetch origin
git rebase origin/develop
```

**优势**：保持线性历史，避免不必要的合并提交

#### 场景 2：提交 Pull Request 前整理历史

```bash
# 合并多个小提交为一个大提交
git rebase -i HEAD~5  # 整理最近 5 个提交
```

**优势**：让 PR 更容易审查，历史更清晰

### 3.3 交互式 Rebase 操作

#### 常用操作

```bash
git rebase -i <commit-id>  # 或 HEAD~n（n 为提交数量）
```

#### 交互式 Rebase 命令

| 命令     | 说明                        | 使用场景             |
| -------- | --------------------------- | -------------------- |
| `pick`   | 使用该提交（默认）          | 保留提交             |
| `reword` | 使用并修改提交信息          | 修正提交信息         |
| `edit`   | 使用但暂停以修改            | 修改提交内容         |
| `squash` | 使用但合并到上一个提交      | 合并多个提交         |
| `fixup`  | 类似 squash，但丢弃提交信息 | 合并并丢弃提交信息   |
| `drop`   | 删除提交                    | 删除不需要的提交     |
| `exec`   | 执行 shell 命令             | 在每个提交后执行命令 |

#### 操作示例

**示例：合并最近 3 个提交**

```bash
git rebase -i HEAD~3
```

编辑器内容：

```
pick abc1234 feat: 添加用户登录
squash def5678 fix: 修复登录验证
squash ghi9012 style: 格式化代码
```

结果：3 个提交合并为 1 个提交

**示例：修改提交信息**

```bash
git rebase -i HEAD~3
```

编辑器内容：

```
pick abc1234 feat: 添加用户登录
reword def5678 fix: 修复登录验证
pick ghi9012 style: 格式化代码
```

结果：第二个提交的信息会被打开编辑器供修改

### 3.4 Rebase 注意事项

#### ⚠️ 冲突处理

1. **解决冲突**：

    ```bash
    # 发生冲突后，Git 会暂停
    # 1. 手动解决冲突
    # 2. 标记为已解决
    git add <resolved-files>
    # 3. 继续 rebase
    git rebase --continue
    ```

2. **中止 Rebase**：

    ```bash
    git rebase --abort  # 回到 rebase 前的状态
    ```

3. **跳过当前提交**：

    ```bash
    git rebase --skip  # 跳过当前提交（谨慎使用）
    ```

#### ⚠️ 强制推送

**重要**：Rebase 会改写提交历史，推送到远程仓库需要使用强制推送。

**安全做法**：

```bash
# 使用 --force-with-lease（推荐）
git push --force-with-lease origin feature/user-authentication
```

**区别**：

- `--force`：强制推送，可能覆盖他人的提交
- `--force-with-lease`：仅在远程分支没有新的提交时强制推送，更安全

#### ⚠️ 团队协作建议

1. **沟通**：在 rebase 公共分支前，通知团队成员
2. **时间窗口**：选择团队活动较少的时间进行 rebase
3. **通知机制**：通过团队通信工具通知 rebase 操作

---

## 