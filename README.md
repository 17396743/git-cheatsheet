# Git 命令速查表

日常开发里最常用的 Git 操作，按场景分类，一页看完。命令以 Git 2.40+ 为准。

## 一、配置

```bash
git config --global user.name "你的名字"
git config --global user.email "you@example.com"

# 查看所有配置及其来源文件
git config --list --show-origin

# 让 Git 正确显示中文文件名（Windows 建议开）
git config --global core.quotepath false
```

## 二、提交

```bash
git status                     # 工作区状态
git diff                       # 未暂存的改动
git diff --staged              # 已暂存的改动

git add <file>                 # 暂存单个文件
git add -p                     # 交互式分块暂存，推荐
git commit -m "feat: 说明"      # 提交
git commit --amend             # 修改上一条提交（未推送时用）
```

## 三、分支

```bash
git branch                     # 本地分支
git branch -a                  # 含远程分支
git switch -c feature/x        # 新建并切换
git switch main                # 切换
git branch -d feature/x        # 删除已合并分支
git branch -D feature/x        # 强制删除
git branch -m old new          # 重命名
```

## 四、同步远程

```bash
git fetch --prune              # 拉取并清理已删除的远程分支引用
git pull --rebase              # 拉取并变基，提交历史更干净
git push -u origin feature/x   # 首次推送并建立追踪
git push --force-with-lease    # 安全强推：远程有新提交时会拒绝
```

> `--force-with-lease` 比 `--force` 安全得多，强推时优先用它。

## 五、查看历史

```bash
git log --oneline --graph --all
git log -p <file>              # 某文件的改动历史
git log -S "关键词"             # 搜索引入/删除该字符串的提交
git blame <file>               # 逐行看是谁改的
git show <commit>              # 查看某次提交的内容
```

## 六、撤销操作

见 [UNDO.md](UNDO.md)。

## 七、储藏

```bash
git stash                      # 暂存当前改动
git stash -u                   # 包含未追踪文件
git stash list                 # 列表
git stash pop                  # 恢复并删除最近一条
git stash apply stash@{2}      # 恢复指定条目（保留）
```

## 八、常见坑

| 现象 | 原因 | 解决 |
| --- | --- | --- |
| 中文文件名显示成 `\344\270\255` | `core.quotepath` 默认开启 | `git config --global core.quotepath false` |
| 拉取后出现多余的 merge commit | 用了 `git pull` 默认合并 | 改用 `git pull --rebase` |
| 换行符警告 `LF will be replaced by CRLF` | Windows 换行符设置 | `git config --global core.autocrlf true` |
| 提交了不该提交的文件 | 已在工作区 | 加到 `.gitignore` 后 `git rm --cached <file>` |

## License

MIT
