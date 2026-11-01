# 常用 Git 别名配置

把这几个别名加到全局配置里，每天能少敲几百次键盘。一次性生效，所有仓库通用。

## 一、直接写入

Windows 下建议在 Git Bash 里执行，避免 PowerShell 的引号转义问题：

```bash
git config --global alias.st   "status -sb"
git config --global alias.co   "checkout"
git config --global alias.br   "branch"
git config --global alias.ci   "commit"
git config --global alias.unstage "restore --staged"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.lg   "log --oneline --graph --decorate --all"
git config --global alias.undo "reset --soft HEAD~1"
git config --global alias.amend "commit --amend --no-edit"
```

## 二、别名速查

| 别名 | 等价命令 | 用途 |
| --- | --- | --- |
| `git st` | `git status -sb` | 紧凑状态，一行看完 |
| `git co` | `git checkout` | 切分支 / 恢复文件 |
| `git br` | `git branch` | 列分支 |
| `git ci` | `git commit` | 提交 |
| `git unstage <f>` | `git restore --staged <f>` | 取消暂存 |
| `git last` | `git log -1 HEAD --stat` | 看最后一次提交改了啥 |
| `git lg` | `git log --oneline --graph --all` | 图形化历史，排查分叉神器 |
| `git undo` | `git reset --soft HEAD~1` | 撤销提交但保留改动 |
| `git amend` | `git commit --amend --no-edit` | 补文件到上一次提交 |

## 三、为什么 `git st` 用 `-sb`

`git status` 默认输出有十几行提示文字，`-sb` 之后只剩：

```
## main...origin/main [ahead 2]
 M README.md
?? UNDO.md
```

`M` 是已修改，`??` 是未追踪，`## ` 那行直接告诉你和远程差几个提交。日常看状态完全够用。

## 四、`git undo` 的坑

`git undo` 用的是 `reset --soft`，改动会保留在**暂存区**。如果你只是想撤销提交、然后重新组合：

```bash
git undo            # 提交没了，改动还在暂存区
git restore --staged .   # 需要的话退回工作区
git add -p          # 重新分块暂存
git commit -m "新的提交信息"
```

它**不会**丢代码，可以放心用。真正危险的是 `reset --hard`，那个不进别名是有意为之。

## 五、查看已配置的别名

```bash
git config --global --get-regexp alias
```

想删掉某个：

```bash
git config --global --unset alias.st
```

## 六、一个小建议

别名别贪多。上面九个已经覆盖了日常 90% 的高频操作，再加就容易记混——尤其是你自己定义的、和网上教程不一样的别名，换台机器就想不起来了。

配置完顺手导出一份：

```bash
git config --global --get-regexp alias > ~/git-aliases.txt
```

新机器上逐行 `git config` 回去就行。
