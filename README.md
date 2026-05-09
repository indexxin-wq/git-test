# Git 实战学习手册

> 本项目用于学习 Git 版本管理，从零开始掌握 Git 基本操作。

---

## 目录

1. [Git 基础概念](#git-基础概念)
2. [初始化仓库](#初始化仓库)
3. [暂存与提交](#暂存与提交)
4. [查看历史和状态](#查看历史和状态)
5. [撤销操作](#撤销操作)
6. [分支管理](#分支管理)
7. [GitHub 远程仓库](#github-远程仓库)

---

## Git 基础概念

### Git 是什么？

Git = 版本管理工具，用来记录代码的每一次变更，可以随时回到任意历史版本。

```
没有 Git：
今天改代码 → 明天改代码 → 后天改坏了 → 完了

有 Git：
今天 commit → 存档1
明天 commit → 存档2
后天改坏了 → git checkout 存档1 → 恢复了
```

### 三个区域

```
工作区（Working Directory）  ← 你正在修改的文件
        ↓ git add
暂存区（Staging Area）     ← 准备提交的文件
        ↓ git commit
仓库（Repository）         ← 正式存档的地方 (.git 目录)
```

### Git 和 GitHub 的区别

| 概念 | 位置 | 说明 |
|------|------|------|
| Git | 本地 | 存在你电脑的 .git 目录里 |
| GitHub | 云端 | 托管 Git 仓库的网站 |

---

## 初始化仓库

### git init

在项目文件夹里运行，创建 .git 目录：

```bash
cd ~/git-test
git init
```

结果：
```
Initialized empty Git repository in /Users/bjy/git-test/.git/
```

### .git 目录

```
.git/
├── config          # Git 配置
├── objects/       # 存储代码快照
├── refs/          # 存储分支指针
├── HEAD           # 指向当前分支
└── index         # 暂存区索引
```

---

## 暂存与提交

### git add - 添加到暂存区

```bash
git add 文件名       # 添加单个文件
git add .           # 添加所有文件
```

### git commit - 提交到仓库

```bash
git commit -m "提交说明"
```

### 完整流程

```bash
# 1. 修改代码（工作区）
# 2. 添加到暂存区
git add .
# 3. 提交到仓库
git commit -m "添加了新功能"
```

### 工作流程图

```
工作区 → git add → 暂存区 → git commit → 仓库(.git)
```

---

## 查看历史和状态

### git status - 查看状态

```bash
git status
```

常见状态：

| 状态 | 说明 |
|------|------|
| Untracked files | 新文件，Git 未跟踪 |
| Changes not staged for commit | 文件被修改，未暂存 |
| Changes to be committed | 已暂存，待提交 |
| nothing to commit, working tree clean | 工作区干净，无改动 |

### git log - 查看历史

```bash
git log                  # 完整信息
git log --oneline        # 简洁版（一行）
git log --graph          # 分支图
```

输出示例：

```
commit c8ad719 (HEAD -> feature-login)
Author: indexxin-wq <indexxin@gmail.com>
Date:   Sat May 9 19:00:00 2026 +0800
    分支1提交明
```

### git diff - 查看改动

```bash
git diff                # 工作区 vs 暂存区
git diff --staged       # 暂存区 vs 上次提交
git diff HEAD~1         # 当前 vs 上上次
```

---

## 撤销操作

### git restore - 丢弃工作区改动

```bash
git restore 文件名           # 丢弃文件的修改
git restore .              # 丢弃所有修改
```

### git restore --staged - 取消暂存

```bash
git restore --staged 文件名   # 从暂存区移出
git restore --staged .       # 移出所有
```

### git checkout - 切换到某个版本

```bash
git checkout 版本号
```

注意：会进入 detached HEAD 状态，用完记得 `git switch` 切回来。

### git reset - 回退版本

```bash
git reset --hard HEAD~1      # 回退到上一个版本
git reset --hard HEAD~2      # 回退到上两个版本
git reset --hard 版本号       # 回退到指定版本
```

⚠️ 危险操作：会丢失后面的提交记录。

---

## 分支管理

### 什么是分支？

分支 = 代码的平行宇宙

```
main 主线：    A → B → C → D
                    ↘
              feature-login：    E → F → G
```

### 创建分支

```bash
git branch 分支名
```

### 切换分支

```bash
git switch 分支名       # 新命令（推荐）
git checkout 分支名    # 老命令
```

### 创建并切换

```bash
git switch -c 分支名   # 创建并切换
git checkout -b 分支名  # 老命令
```

### 查看分支

```bash
git branch            # 查看本地分支
git branch -a         # 查看所有分支（包括远程）
```

`*` 表示当前分支。

### 删除分支

```bash
git branch -d 分支名   # 安全删除（未合并会提示）
git branch -D 分支名   # 强制删除
```

### 合并分支

```bash
# 1. 切回目标分支（通常是 main）
git switch main

# 2. 合并
git merge feature-login
```

### 合并方式

| 方式 | 说明 |
|------|------|
| Fast-forward | 快进合并，没有冲突时使用 |
| 三方合并 | 有冲突时，Git 自动合并或提示冲突 |

### 冲突解决

当两个分支修改了同一行代码时，会产生冲突。

冲突标记：
```markdown
<<<<<<< HEAD
当前分支的内容
=======
被合并分支的内容
>>>>>>> feature-login
```

解决步骤：
1. 手动选择保留哪个（或两者都保留）
2. 删除冲突标记 `<<<<< ==== >>>>`
3. `git add .`
4. `git commit`

---

## GitHub 远程仓库

### 添加远程仓库

```bash
git remote add origin https://github.com/用户名/仓库名.git
```

### 查看远程仓库

```bash
git remote -v
```

### 推送

```bash
git push -u origin main    # 首次推送，设置上游
git push                   # 后续推送
```

### 拉取

```bash
git pull                  # 拉取并合并
git fetch                 # 仅拉取，不合并
```

### 克隆

```bash
git clone https://github.com/用户名/仓库名.git
```

---

## 常用命令速查

```bash
# 初始化
git init                 # 初始化仓库

# 暂存和提交
git add .                # 暂存所有
git commit -m "说明"      # 提交

# 查看
git status               # 状态
git log                  # 历史
git diff                 # 改动

# 撤销
git restore 文件          # 丢弃修改
git restore --staged .   # 取消暂存

# 分支
git branch               # 查看分支
git branch xxx           # 创建分支
git switch xxx           # 切换分支
git switch -c xxx        # 创建并切换
git merge xxx            # 合并分支

# 远程
git remote add origin url # 添加远程
git push                # 推送
git pull                # 拉取
git clone url            # 克隆
```

---

## 实际开发流程

```
1. 在 main 分支开始
git switch main

2. 创建功能分支
git switch -c feature/登录功能

3. 在功能分支开发
修改代码 → git add → git commit（多次）

4. 开发完成，切换回 main
git switch main

5. 合并功能分支
git merge feature/登录功能

6. 推送到 GitHub
git push
```

---

## 常见问题

### Q: 提交信息写错了怎么办？

```bash
git commit --amend -m "新的提交信息"
```

### Q: 不想提交但需要切换分支？

先把当前改动 stash 起来：
```bash
git stash                # 暂存改动
git switch xxx           # 切换分支
git stash pop            # 恢复改动
```

### Q: 怎么撤销最后一次提交？

```bash
git reset --hard HEAD~1  # 回退到上一个版本
```

### Q: VS Code 提交卡住怎么办？

用终端提交：
```bash
git add .
git commit -m "说明"
```

---

## 项目链接

- GitHub 仓库：https://github.com/indexxin-wq/git-test
- 远程分支：origin/main

---

## 学习记录

| 日期 | 操作 | 说明 |
|------|------|------|
| 2026-05-09 | git init | 初始化仓库 |
| 2026-05-09 | git add + commit | 第一次提交 |
| 2026-05-09 | git branch feature-login | 创建分支 |
| 2026-05-09 | git merge feature-login | 合并分支 |
| 2026-05-09 | git push | 推送到 GitHub |

---

*本手册基于 Git 实战学习编写*
