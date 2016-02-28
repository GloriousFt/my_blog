---
layout: post
title: Git最常用的操作
date: 2016-02-28 18:22:17
type: post
published: true
status: publish
categories:
- Technology
tags:
- git
---

### git用户记录
```
git config --global user.name "name" // 记录用户名
git config --global user.email "email" // 记录邮箱,为了登记每次提交的信息
```

### git追踪
```
git add . // "."就是追踪所有新的改动文件
git add *.* // 追踪指定文件
```

### git提交和撤销
```
git reset --hard // 强制撤回,危险操作,经常用于回退merge操作
git commit -m "post" // 提交add内容,说明为"post"
git commit -a "post" // 不需add,直接提交改动的内容
git add *.*
git commit --amend // 使用上一次提交说明"post",提交所有add内容
```
Reset的详细介绍
[reset](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E7%BD%AE%E6%8F%AD%E5%AF%86)

### git移除提交内容
```
git rm --cached // 移除暂存区域的文件但是保留工作区的文件
git rm a.a // 移除a.a这个文件,与add相类似,但是是移除文件
```

### git日志
```
git status // 查看当前仓库状态
git log -p -3 // -p显示每次提交内容差异,-3表示最近的3次提交
git log --stat // 显示紧要的行数统计
```

### git远程仓库
```
git remote -v // 显示所有远程仓库
git remote add "RepoName" "Repo" //增加远程仓库,命名为"RepoName"
git remote remove "RepoName" //删除名字为"Repo"的远程仓库
```

### git分支
```
git branch newB // 创建新的分支newB
git checkout newB // 转移分支到newB
git merge master // 合并master分支到当前分支
git branch -d newB //删除本地newB分支
git branch --merged // 查看哪些分支已被并入当前分支
git branch -v // 查看每个分支最后一次提交信息
git push origin newB // 推送本地分支newB到仓库
git push origin :newB // 删除远程newB分支
```

### git仓库初始化
```
git init // 对现有项目进行git初始化
git clone "repoURL" // 克隆远程仓库到当前文件夹
```
**还有一些如rebase的操作我用的不多,就不在这里记录了.**





