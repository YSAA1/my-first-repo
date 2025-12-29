# Git 与 GitHub 完整教程

> 作者：ShiYu | 创建日期：2024年

---

## 目录

1. [基础概念](#基础概念)
2. [环境配置](#环境配置)
3. [基础操作](#基础操作)
4. [分支与协作](#分支与协作)
5. [完整工作流](#完整工作流)
6. [命令速查表](#命令速查表)

---

## 基础概念

### Git vs GitHub

| 名称 | 是什么 | 类比 |
|------|--------|------|
| Git | 版本控制工具 | 游戏存档系统 |
| GitHub | 代码托管平台 | 云存档服务器 |

### 核心术语

```
Repository (仓库)  = 项目文件夹
Commit (提交)      = 保存一次改动（存档点）
Branch (分支)      = 平行开发线（平行宇宙）
Push (推送)        = 上传到远程
Pull (拉取)        = 从远程下载
Clone (克隆)       = 下载整个项目
PR (Pull Request)  = 请求合并代码
```

### 三个工作区域

```
┌─────────────────────────────────────────────────────────┐
│                      你的电脑                            │
│                                                         │
│   工作区              暂存区              本地仓库        │
│  (Working)          (Staging)          (Repository)     │
│      │                  │                   │           │
│      │ ── git add ──→   │ ── git commit ──→ │           │
│      │                  │                   │           │
└──────│──────────────────│───────────────────│───────────┘
       │                  │                   │
       │                  │                   │ ── git push ──→ GitHub
       │                  │                   │
       │                  │                   │ ←── git pull ── GitHub
```

---

## 环境配置

### 1. 安装 Git

```bash
# Windows - 下载安装
https://git-scm.com/download/win

# 验证安装
git --version
```

### 2. 配置用户信息

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"

# 查看配置
git config --list
```

### 3. 安装 GitHub CLI

```bash
# Windows
winget install GitHub.cli

# 登录 GitHub
gh auth login
```

---

## 基础操作

### 场景一：创建新项目并上传到 GitHub

```bash
# 1. 创建项目文件夹
mkdir my-project
cd my-project

# 2. 初始化 Git 仓库
git init

# 3. 创建文件
echo "# My Project" > README.md

# 4. 添加到暂存区
git add .

# 5. 提交
git commit -m "Initial commit"

# 6. 创建 GitHub 仓库并推送
gh repo create my-project --public --source=. --push
```

### 场景二：修改文件并推送

```bash
# 1. 修改文件...

# 2. 查看改动
git status              # 查看哪些文件改了
git diff                # 查看具体改了什么

# 3. 提交并推送
git add .
git commit -m "描述你做了什么"
git push
```

### 场景三：从 GitHub 拉取更新

```bash
# 检查远程是否有更新
git fetch
git status

# 拉取更新
git pull
```

### 场景四：克隆已有项目

```bash
# 克隆别人的项目
git clone https://github.com/用户名/项目名.git

# 进入项目目录
cd 项目名
```

---

## 分支与协作

### 分支概念图

```
主分支 (master/main):     A ── B ── C ── D ── E (正式版本)
                                    \       ↗
功能分支 (feature):                   └─ X ─ Y (开发新功能)
```

### 分支操作

```bash
# 查看所有分支
git branch

# 创建并切换到新分支
git checkout -b feature/new-feature

# 切换分支
git checkout master
git checkout feature/new-feature

# 删除分支
git branch -d feature/new-feature
```

### Pull Request 工作流

```bash
# 1. 创建功能分支
git checkout -b feature/add-login

# 2. 开发功能并提交
git add .
git commit -m "添加登录功能"

# 3. 推送分支到 GitHub
git push -u origin feature/add-login

# 4. 创建 PR
gh pr create --title "添加登录功能" --body "实现了用户登录"

# 5. 合并 PR（通过审查后）
gh pr merge --squash --delete-branch

# 6. 切回主分支并更新
git checkout master
git pull
```

---

## 完整工作流

### 日常开发流程

```
┌──────────────────────────────────────────────────────────────┐
│                        标准工作流                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. 开始工作前，拉取最新代码                                   │
│     $ git pull                                               │
│                                                              │
│  2. 创建功能分支                                              │
│     $ git checkout -b feature/xxx                            │
│                                                              │
│  3. 开发功能（写代码）                                        │
│                                                              │
│  4. 提交改动                                                  │
│     $ git add .                                              │
│     $ git commit -m "描述"                                   │
│                                                              │
│  5. 推送到 GitHub                                            │
│     $ git push -u origin feature/xxx                         │
│                                                              │
│  6. 创建 PR 请求合并                                          │
│     $ gh pr create                                           │
│                                                              │
│  7. 代码审查通过后合并                                        │
│     $ gh pr merge                                            │
│                                                              │
│  8. 切回主分支，删除功能分支                                   │
│     $ git checkout master                                    │
│     $ git pull                                               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### 团队协作场景

```
         你                    同事                   GitHub
          │                     │                       │
          │                     │    push 代码          │
          │                     │ ─────────────────────→│
          │                     │                       │
          │  git pull           │                       │
          │←────────────────────│───────────────────────│
          │                     │                       │
          │  创建分支开发        │                       │
          │  push 分支           │                       │
          │ ────────────────────│──────────────────────→│
          │                     │                       │
          │  创建 PR             │                       │
          │ ────────────────────│──────────────────────→│
          │                     │                       │
          │                     │    Review PR          │
          │←────────────────────│───────────────────────│
          │                     │                       │
          │  合并 PR             │                       │
          │ ────────────────────│──────────────────────→│
          │                     │                       │
```

---

## 命令速查表

### 基础命令

| 命令 | 作用 |
|------|------|
| `git init` | 初始化仓库 |
| `git clone <url>` | 克隆仓库 |
| `git status` | 查看状态 |
| `git diff` | 查看改动 |
| `git add .` | 添加所有改动 |
| `git commit -m "消息"` | 提交 |
| `git push` | 推送到远程 |
| `git pull` | 拉取更新 |
| `git log --oneline` | 查看提交历史 |

### 分支命令

| 命令 | 作用 |
|------|------|
| `git branch` | 查看分支 |
| `git branch <name>` | 创建分支 |
| `git checkout <name>` | 切换分支 |
| `git checkout -b <name>` | 创建并切换 |
| `git merge <name>` | 合并分支 |
| `git branch -d <name>` | 删除分支 |

### GitHub CLI 命令

| 命令 | 作用 |
|------|------|
| `gh auth login` | 登录 GitHub |
| `gh repo create` | 创建仓库 |
| `gh pr create` | 创建 PR |
| `gh pr list` | 查看 PR 列表 |
| `gh pr merge` | 合并 PR |
| `gh repo clone <repo>` | 克隆仓库 |

### 快捷组合

```bash
# 快速提交推送
git add . && git commit -m "消息" && git push

# 创建分支并推送
git checkout -b feature/xxx && git push -u origin feature/xxx

# 完成功能后
gh pr create && gh pr merge --squash --delete-branch
```

---

## 常见问题

### Q: 提交信息写错了怎么办？

```bash
# 修改最后一次提交信息（未推送时）
git commit --amend -m "新的消息"
```

### Q: 想撤销还没提交的改动？

```bash
# 撤销某个文件的改动
git checkout -- 文件名

# 撤销所有改动
git checkout -- .
```

### Q: Push 被拒绝怎么办？

```bash
# 通常是因为远程有更新，先 pull
git pull
# 然后再 push
git push
```

### Q: 分支冲突怎么解决？

```bash
# 1. 先 pull 最新代码
git pull

# 2. 如果有冲突，手动编辑冲突文件
# 3. 解决后重新提交
git add .
git commit -m "解决冲突"
git push
```

---

## 学习资源

- [Git 官方文档](https://git-scm.com/doc)
- [GitHub 官方指南](https://docs.github.com)
- [GitHub CLI 文档](https://cli.github.com/manual)

---

> 记住：多练习，多犯错，多学习！
