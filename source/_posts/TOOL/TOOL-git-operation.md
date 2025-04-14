---
title: Git学习笔记
category_bar: true
math: false
tags: [Git]
category: [工具]
date: 2025-04-06 19:12:48
---

## 创建仓库
### 初始化
- `git init`
### 克隆
- `git clone <repo> <dir>`
```bash
git clone git@github.com:User/Repo
git clone git@github.com:User/Repo localDir
```
## 基本操作
### 提交
- `git add`：添加文件到暂存区。
  - `git add .`：添加所有文件。
  - `git add file.txt`
- `git status` ：查看仓库当前的状态，显示有变更的文件。
- `git commit`：提交暂存区到本地仓库。
  - `git commit`
  - `git commit -m "message"`
`git log`：查看历史提交记录。
### 远程操作
- `git remote`
  - `git remote`：列出当前仓库中已配置的远程仓库。
  - `git remote -v`：列出当前仓库中已配置的远程仓库，并显示它们的 URL。
  - `git remote add <remote_name> <remote_url>`：添加一个新的远程仓库。指定一个远程仓库的名称和 URL，将其添加到当前仓库中。
  - `git remote rename <old_name> <new_name>`：将已配置的远程仓库重命名。
  - `git remote remove <remote_name>`：从当前仓库中删除指定的远程仓库。
  - `git remote set-url <remote_name> <new_url>`：修改指定远程仓库的 URL。
  - `git remote show <remote_name>`：显示指定远程仓库的详细信息，包括 URL 和跟踪分支。
- `git fetch`
- `git pull`
  - `git pull <远程仓库名> <远程分支名>:<本地分支名>`
  - `git pull <远程仓库名> <分支名>`：本地分支名与远程分支名相同。
  - `git pull --rebase <远程仓库名> <远程分支名>:<本地分支名>`：使用rebase模式拉取、合并。
- `git push`
  - `git push <远程主机名> <本地分支名>:<远程分支名>`
  - `git push <远程主机名> <分支名>`：本地分支名与远程分支名相同。
  - `git push --force`：强制推送。
### 分支管理
- `git checkout`
  - `git checkout -b <branchname>`：创建分支并切换到该分支。
  - `git checkout <branchname`：切换分支
- `git branch`
  - `git branch`：查看所有分支。
  - `git branch -r` ：查看远程分支。
  - `git branch -a`：查看所有本地和远程分支。
  - `git branch -d <branchname>`：删除分支。
  - `git branch -D <branchname>`：强制删除分支。
- `git merge <branchname>`：将其他分支合并到当前分支。
## 其他
### 暂存
- `git stash`
  - `git stash`：保存当前工作进度。
  - `git stash save "info"`：保存并附加信息。
  - `git stash list`：查看现有stash。
  - `git stash apply`：应用最近一次stash。
  - `git stash pop`：应用并删除最近一次stash。
  - `git stash drop <stashname>`：删除某个stash，如`stash@{n}`。
  - `git stash clear`：清空所有stash。
### 变基
- `git rebase <branchname>`：变基当前分支到指定分支。
### 回退
- `git reset [--soft | --mixed | --hard] [HEAD]`
```bash
git reset HEAD^            # 回退所有内容到上一个版本
git reset HEAD~n           # 回退所有内容到前第n个版本
git reset HEAD^ hello.php  # 回退 hello.php 文件的版本到上一个版本
git reset 052e            # 回退到指定版本
```
