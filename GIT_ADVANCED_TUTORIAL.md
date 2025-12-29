# Git 进阶教程

> 作者：ShiYu | 日期：2024年

---

## 目录

1. [Fork 工作流](#一fork-工作流)
2. [处理代码冲突](#二处理代码冲突)
3. [GitHub Actions CI/CD](#三github-actions-cicd)
4. [常用命令速查](#四常用命令速查)

---

## 一、Fork 工作流

### 什么是 Fork？

Fork 是复制别人的仓库到你的账号下，让你可以自由修改而不影响原项目。

```
原作者仓库                          你的仓库
┌─────────────────┐    Fork     ┌─────────────────┐
│ awesome-project │  ────────→  │ awesome-project │
│ (别人的)        │              │ (你的副本)       │
└─────────────────┘              └─────────────────┘
```

### Fork 完整流程

```bash
# 1. Fork 项目
gh repo fork 用户名/项目名

# 2. 克隆到本地
gh repo clone 你的用户名/项目名
cd 项目名

# 3. 查看远程配置
git remote -v
# origin   = 你的 Fork
# upstream = 原项目

# 4. 创建功能分支
git checkout -b feature/my-feature

# 5. 开发并提交
git add .
git commit -m "Add my feature"

# 6. 推送到你的 Fork
git push -u origin feature/my-feature

# 7. 在 GitHub 上创建 PR（向原项目）

# 8. 保持与原项目同步
git fetch upstream
git checkout master
git merge upstream/master
git push origin master
```

### 向开源项目贡献代码

```
1. 找到想贡献的项目
2. 阅读 CONTRIBUTING.md（贡献指南）
3. Fork 项目
4. 创建分支，修改代码
5. 提交 PR
6. 等待审查，根据反馈修改
7. PR 被合并 → 你成为贡献者！
```

---

## 二、处理代码冲突

### 什么是冲突？

当两个人同时修改了同一行代码，Git 无法自动合并。

```
你的改动: "Hello World"
同事改动: "Hello GitHub"
         ↓
      冲突！
```

### 冲突标记

```
<<<<<<< HEAD
你当前分支的内容
=======
要合并的分支的内容
>>>>>>> branch-name
```

### 解决冲突步骤

```bash
# 1. 合并时发现冲突
git merge other-branch
# CONFLICT (content): Merge conflict in file.txt

# 2. 查看冲突状态
git status
# Unmerged paths: both modified: file.txt

# 3. 打开冲突文件，编辑解决
# - 删除冲突标记 <<<<<<< ======= >>>>>>>
# - 保留你想要的内容

# 4. 标记为已解决
git add file.txt

# 5. 完成合并
git commit -m "Resolve merge conflict"

# （可选）放弃合并
git merge --abort
```

### 解决冲突的选择

| 选择 | 操作 |
|------|------|
| 保留当前分支 | 删除对方内容，保留 HEAD 下的内容 |
| 保留对方分支 | 删除当前内容，保留 branch-name 下的内容 |
| 合并两者 | 手动整合两边的内容 |
| 重写 | 删除两边，写全新的内容 |

### 避免冲突的建议

1. **频繁拉取** - 经常 `git pull` 保持最新
2. **小步提交** - 每次只改一小部分
3. **及时合并** - 分支不要存在太久
4. **良好沟通** - 告知团队你在改什么文件

---

## 三、GitHub Actions CI/CD

### 基本概念

```
CI (持续集成): 每次提交自动测试
CD (持续部署): 测试通过自动部署

提交代码 → 触发 Actions → 运行测试 → 通过则部署
```

### 工作流文件位置

```
项目根目录/
└── .github/
    └── workflows/
        └── ci.yml    ← 工作流配置文件
```

### 工作流文件结构

```yaml
name: CI Pipeline           # 工作流名称

on:                         # 触发条件
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:                       # 任务
  test:                     # 任务名
    runs-on: ubuntu-latest  # 运行环境

    steps:                  # 步骤
      - name: Checkout
        uses: actions/checkout@v4

      - name: Run tests
        run: npm test
```

### 触发条件

```yaml
on:
  # 推送时触发
  push:
    branches: [ master, main ]
    paths:
      - 'src/**'           # 只在 src 目录改动时触发

  # PR 时触发
  pull_request:
    branches: [ master ]

  # 定时触发
  schedule:
    - cron: '0 0 * * *'    # 每天零点

  # 手动触发
  workflow_dispatch:
```

### 常用 Actions

```yaml
# 检出代码
- uses: actions/checkout@v4

# Node.js 环境
- uses: actions/setup-node@v4
  with:
    node-version: '18'

# Python 环境
- uses: actions/setup-python@v4
  with:
    python-version: '3.11'

# 缓存依赖
- uses: actions/cache@v3
  with:
    path: node_modules
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

### 实用示例：Node.js 项目

```yaml
name: Node.js CI

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '18'

    - name: Install dependencies
      run: npm ci

    - name: Run linter
      run: npm run lint

    - name: Run tests
      run: npm test

    - name: Build
      run: npm run build
```

### 实用示例：Python 项目

```yaml
name: Python CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Run tests
      run: pytest

    - name: Check code style
      run: flake8 .
```

### 查看 CI 结果

```bash
# 查看最近的运行
gh run list

# 查看特定运行详情
gh run view <run-id>

# 查看失败的日志
gh run view <run-id> --log-failed
```

---

## 四、常用命令速查

### Fork 相关

| 命令 | 作用 |
|------|------|
| `gh repo fork user/repo` | Fork 仓库 |
| `git remote -v` | 查看远程配置 |
| `git fetch upstream` | 获取原项目更新 |
| `git merge upstream/master` | 合并原项目更新 |

### 冲突相关

| 命令 | 作用 |
|------|------|
| `git status` | 查看冲突文件 |
| `git diff` | 查看差异 |
| `git add <file>` | 标记冲突已解决 |
| `git merge --abort` | 放弃合并 |

### GitHub Actions 相关

| 命令 | 作用 |
|------|------|
| `gh run list` | 查看运行列表 |
| `gh run view <id>` | 查看运行详情 |
| `gh run watch` | 实时查看运行状态 |
| `gh run rerun <id>` | 重新运行 |

---

## 学习资源

- [GitHub Actions 官方文档](https://docs.github.com/actions)
- [Actions Marketplace](https://github.com/marketplace?type=actions)
- [Git 官方文档](https://git-scm.com/doc)

---

> 实践是最好的学习方式，多动手尝试！
