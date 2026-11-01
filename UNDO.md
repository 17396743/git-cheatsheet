# Git 撤销操作完全指南

撤销是 Git 里最容易搞混的部分。核心只有一句话：**先判断东西在哪个阶段，再用对应阶段的命令。**

## 一、先搞清楚三个区域和一条铁律

```
工作区 (Working)  --git add-->  暂存区 (Index)  --git commit-->  本地仓库 (HEAD)
```

一条铁律：**只要还没 `git push`，一切都可救；一旦推到远程且被别人拉走，就得靠 `revert` 而不是改写历史。**

## 二、按场景选命令

### 场景 1：改错了文件，还没 add

```bash
git restore <file>          # 丢弃工作区改动，恢复到 HEAD
git restore .               # 丢弃全部
```

### 场景 2：已经 add，想撤回暂存（改动保留）

```bash
git restore --staged <file> # 取消暂存，工作区内容不变
```

### 场景 3：commit 信息写错了

```bash
git commit --amend -m "新的信息"
```

`--amend` 会生成一个新提交替换旧的，**提交哈希会变**，所以只用于未推送的提交。

### 场景 4：多提交了一个文件，想从上一个 commit 里拿掉

```bash
git restore --staged secret.env     # 先从暂存区拿出来
git commit --amend --no-edit        # 重写上一个提交，信息不变
```

### 场景 5：想撤销刚才的 commit，改动保留

```bash
git reset --soft HEAD~1     # 撤销提交，改动回到暂存区
git reset HEAD~1            # 混合模式（默认），改动回到工作区
```

### 场景 6：想彻底扔掉刚才的 commit 和改动

```bash
git reset --hard HEAD~1
```

> ⚠️ `--hard` 会丢弃未提交的改动，且不进 reflog 的“内容”里。执行前先确认没有想留的东西。

### 场景 7：提交已经推送了，要反向抵消

```bash
git revert <commit>         # 生成一个反向提交，历史保留
```

这是**在共享分支上唯一安全的撤销方式**，因为它不改写已有历史。

### 场景 8：误删了分支 / reset 错了

```bash
git reflog                  # 查看 HEAD 的移动记录
git reset --hard HEAD@{3}   # 回到 reflog 里的某个位置
git switch -c rescue <sha>  # 或者从某个 sha 建个新分支救回来
```

`reflog` 默认保留 90 天，是最后的保险绳。

## 三、三种 reset 模式对比

| 命令 | HEAD 移动 | 暂存区 | 工作区 | 典型用途 |
| --- | --- | --- | --- | --- |
| `--soft` | ✅ | 保留改动 | 保留 | 想重新组合提交 |
| `--mixed`（默认） | ✅ | 重置 | 保留 | 想重新 add |
| `--hard` | ✅ | 重置 | **丢弃** | 彻底放弃 |

## 四、决策速查

- 只想丢弃某个文件的改动 → `git restore <file>`
- 提交信息打错字（未推送）→ `git commit --amend`
- 想退回上一版再重新提交 → `git reset --soft HEAD~1`
- 提交已在远程、别人在用 → `git revert <sha>`
- 闯祸了不知道在哪 → `git reflog` 先看一眼

## 五、几条经验

1. **动手前先 `git status`**，确认自己在哪个分支、有哪些改动。
2. **不确定就先用 `--soft` 或 `--mixed`**，这两个不会真的丢数据。
3. **共享分支永远别强推**，用 `revert`。
4. 慌乱时 `git stash -u` 把一切先存起来，安全了再回来处理。
